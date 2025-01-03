# 区块链基础(七): 交易所和钱包
> 中心化交易所和钱包 `APP` 是 管理加密资产 的两种主流工具

> 加密资产钱包使用加密密钥（私钥或助记词），本质上是一个虚拟保险箱，用户可以用来发送、接收和存储数字货币。它不保存实际资金，但可以保证用户对其所拥有加密资产的控制权。相比之下，加密资产交易所则是一个市场，用户可以在此买卖和交易加密资产

> 两种主流工具在管理加密资产的方式上有很大的区别

> 这里不再赘述热钱包和冷钱包的区别，具体区别已在过往文章中写明。[冷钱包和热钱包](https://github.com/programmer-zhang/front-end/tree/master/profiles/blockchain_wallet.md)

## 交易所账户

### 加密货币交易所的主要功能
* 资产买卖
* 市场流动性
* 交易币对
* 杠杆交易
* 市场数据
* 法币兑换

### 加密货币交易所是如何运营的

> 交易所在加密货币行业充当商店的作用，既可以实现加密资产的托管，也可以进行加密资产的买卖服务，包括加密资产和法币之间的买卖，加密资产之间的相互转换

* 交易所分为 **中心化交易所**(`Binance`、`OKX`、`Coinbase`)和**去中心化交易所**(`Uniswap`、`SushiSwap`)
* **中心化交易所**
	* 其充币地址或钱包是中心化托管机构托管钱包
	* 提供一定的可靠性和客户支持服务，利用自身实力和准备金吸引客户进行加密资产托管和加密资产交易，并对客户的加密资产提供保障服务，属于中心化机构，与银行作用类似
	* 需要账户注册，并进行 `KYC`，也就是提供相应的实名信息、注册信息等
	* 需要进行加密资产和法币之间的相互交易时提供的相应法币信息保留在中心化交易所数据库中
* **去中心化交易所**
	* 其钱包地址是自托管钱包
	* 是完全的去中心化服务机构，利用链上智能合约或代码机器人进行相应操作，是完全的点对点操作，无需中心化机构的监管和保障，具备更强的隐私性和控制
	* 部分需要 `KYC`，大部分去中心化交易所不需要进行账户注册，也就不需要进行 `KYC`
	* 绝大多数去中心化交易所不支持加密资产和法币之间的相互转换，仅支持币币交易

### 加密货币交易所的安全隐患

* **中心化交易所**
	* 中心化存储用户私钥伴随着泄露风险
	* 中心化交易所的内部风险--内部贪腐、跑路、内部信息泄露等
	* 中心化交易所面临很大的黑客风险
	* 中心化交易所面临监管风险，面对不同国家的监管政策中心化交易所的运营会受到相应影响

* **去中心化交易所**
	* 私钥及助记词由用户自行控制，面临假授权被盗风险
	* 存在空气币或同名垃圾币误导用户进行兑换

### 加密货币交易所如何控制资产
* **中心化交易所**
	* 中心化存储资金
	* 资产转移需要通过交易所发起交易
* **去中心化交易所**
	* 不存储资金
	* 资金转移通过授权

### 加密货币交易所的资产转移体现在链上

> 这里主要说明中心化加密货币交易所的资金转移情况

* 由于加密货币交易所的用户充币地址为交易所分配，所以体现在链上为资金上游地址资金直接转入用户充币地址，而后自动向交易所大地址进行汇集

![](../images/blockChain/exchange_wallet_exhchange-address-funds-deposit.png)

* 资金的转出由于是通过向交易所发起提现申请完成，所以提现在链上表现为资金从交易所大地址进行转出，转入目标提现地址

![](../images/blockChain/exchange_wallet_exhchange-address-funds-withdraw.png)

## 钱包

### 钱包的主要功能
* 存储资产
* 发送和接收资产
* 私钥控制
* 签名交易

### 加密货币交易所的安全隐患
* 私钥丢失伴随着资产丢失
* 钱包软件如果没有适当的安全措施(如强密码、两步安全验证等)，会面临黑客攻击的风险
* 硬件钱包还会有硬件钱包丢失或损坏的风险，会面临资产完全丢失

### 钱包资产转移体现在链上
* 钱包地址由于是自托管地址，资产的转移在链上全部都是公开数据展示，任何人都可以查看到资产转移的详情

![](../images/blockChain/exchange_wallet_onchain-address-funds-flow.png)