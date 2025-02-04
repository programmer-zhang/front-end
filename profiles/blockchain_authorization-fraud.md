# 区块链安全(二): 授权诈骗

> 本文使用 `deepseek` 辅助编写

## 什么是授权诈骗？
在区块链交互中，用户常需通过「授权交易」允许智能合约操作其代币。攻击者利用这一机制设计恶意授权，窃取用户资产。

## 常见授权诈骗类型

### 1. 无限授权钓鱼
```solidity
// 恶意合约代码示例
function approve(address spender, uint256 amount) public returns (bool) {
    _allowances[msg.sender][spender] = amount;
    // 未做限额检查，可能被设置为最大值
    return true;
}
```
**风险**：用户授权时未修改默认最大值（如`2^256-1`），攻击者可随时转移用户全部代币

### 2. 伪装授权
```javascript
// 前端伪造的授权请求
const maliciousContract = {
    address: '0x恶意地址',
    abi: [{
        name: 'approve',
        type: 'function',
        inputs: [{...}] // 与合法合约相同的ABI
    }]
}
```
**特征**：伪造与知名项目相似的合约地址或界面

### 3. 授权劫持
* 攻击者通过漏洞修改已授权的合约地址，将授权转移到恶意合约。
* 当合约存储授权地址时存在设计缺陷，攻击者通过漏洞将授权目标修改为恶意合约地址。典型漏洞场景如下：

```solidity
// 存在漏洞的质押合约
contract VulnerableStaking {
    address public authorizedContract;  // 授权合约地址
    mapping(address => uint256) public balances;

    // 漏洞点：未设置权限控制
    function setAuthorizedContract(address _newAddr) public {
        authorizedContract = _newAddr; // 攻击者可以任意修改授权地址
    }

    // 用户质押操作
    function stake(uint256 amount) public {
        IERC20(token).transferFrom(msg.sender, address(this), amount);
        balances[msg.sender] += amount;
    }

    // 提现时使用被篡改的授权地址
    function withdraw() public {
        IERC20(token).transferFrom(
            address(this),
            msg.sender,
            balances[msg.sender]
        );
    }
}
```

* 攻击步骤
	1. 用户调用stake(100)完成正常质押
	2. 攻击者调用setAuthorizedContract(恶意合约地址)
	3. 用户调用withdraw()时，实际向恶意合约转账
	4. 恶意合约劫持transferFrom操作转移资金

* 安全改进方案

```
// 修复后的合约
contract SecureStaking {
    address private authorizedContract;
    address private owner;  // 添加权限控制
    
    constructor() {
        owner = msg.sender;
    }

    // 增加权限修饰符
    modifier onlyOwner() {
        require(msg.sender == owner, "Unauthorized");
        _;
    }

    // 安全设置方法
    function setAuthorizedContract(address _newAddr) public onlyOwner {
        require(_newAddr != address(0), "Invalid address");
        authorizedContract = _newAddr;
    }
}
```

* 上述改进方案中的主要防御改进点为：

	1. 合约状态修改函数必须设置权限控制（如onlyOwner）
	2. 重要参数修改需添加地址有效性验证
	3. 采用Timelock机制延迟关键参数变更

## 防范措施与代码示例

### 1. 使用有限授权
```solidity
// 安全授权方法：每次交易前授权精确数额
function safeApprove(
    IERC20 token,
    address spender,
    uint256 amount
) internal {
    require(token.approve(spender, 0), "Reset failed"); // 先重置授权
    require(token.approve(spender, amount), "Approval failed");
}
```

### 2. 定期检查授权
```javascript
// 使用ethers.js检查授权状态
const allowance = await tokenContract.allowance(
    userAddress,
    spenderAddress
);
console.log(`当前授权额度：${ethers.utils.formatEther(allowance)}`);
```

### 3. 授权撤销
```javascript
// 撤销授权示例（设置额度为0）
const tx = await tokenContract.approve(
    spenderAddress,
    0
);
await tx.wait();
```

### 4. 使用代理合约
```solidity
// 通过代理合约管理授权
contract AuthManager {
    mapping(address => mapping(address => uint256)) private _allowances;
    
    function limitedApprove(
        address token,
        address spender,
        uint256 amount
    ) external {
        IERC20(token).approve(spender, amount);
        _allowances[msg.sender][spender] = amount;
    }
}
```

## 安全实践建议

1. **交易前检查合约地址**：使用Etherscan验证合约验证状态
2. **使用钱包防护工具**：
   - MetaMask的「Token Approval」检测功能
   - RevokeCash（https://revoke.cash）管理跨链授权
3. **警惕签名请求**：区分普通交易与`approve`/`permit`请求
4. **隔离钱包**：使用不同地址进行交易与资产存储


## 开发者安全建议

```solidity
// 实现ERC20的increaseAllowance/decreaseAllowance
function increaseAllowance(address spender, uint256 addedValue) public returns (bool) {
    _approve(msg.sender, spender, _allowances[msg.sender][spender] + addedValue);
    return true;
}

function decreaseAllowance(address spender, uint256 subtractedValue) public returns (bool) {
    uint256 currentAllowance = _allowances[msg.sender][spender];
    require(currentAllowance >= subtractedValue, "ERC20: decreased allowance below zero");
    _approve(msg.sender, spender, currentAllowance - subtractedValue);
    return true;
}
```

---

## 总结
通过合理设置授权额度、定期审查合约权限、使用安全工具和保持警惕，可有效防范授权诈骗。开发者应遵循最小授权原则，用户需养成检查交易内容的习惯，共同维护区块链生态安全。