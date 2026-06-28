# [单元测试(JS篇)]

## 阅读本文您将收获
* 单元测试的定义
* 单元测试的必要性
* 单元测试实践

> 单元测试（Unit Testing）是前端工程师从“能跑”走向“可靠”的重要一步。

## 1. 什么是单元测试？

**单元测试**（Unit Test）是指对代码中**最小可测试单元**进行验证的自动化测试。

- 在 JS 中，一个“单元”通常是：
  - 一个函数 / 方法
  - 一个 React / Vue 组件
  - 一个工具类 / Hook

**核心特点**：
- **独立性**：不依赖数据库、网络、其他模块（使用 Mock）
- **快速**：通常在毫秒级完成
- **可重复**：每次运行结果应该一致

```js
// 这就是一个可被单元测试的“单元”
function add(a, b) {
  return a + b;
}
```

## 2. 为什么要在前端项目中写单元测试？

写单元测试不是“为了测试而测试”，而是为了解决工程实践中的真实痛点：

1. **早期发现 Bug**  
   改动一行代码就能立刻知道是否破坏了原有功能。

2. **重构安全网**  
   重构、升级依赖、修改逻辑时，测试会告诉你哪里坏掉了。

3. **提升代码质量**  
   迫使你写出**可测试**的代码（低耦合、高内聚），间接改善架构。

4. **团队协作保障**  
   新人接手代码、CI/CD 流水线自动跑测试，避免“在我本地是好的”。

5. **文档即代码**  
   测试用例本身就是最好的使用示例文档。

**量化收益**：大型项目中，单元测试覆盖率 70%+ 的代码，线上 Bug 率通常显著降低。

## 3. 前端常用的单元测试工具

| 工具          | 特点                          | 推荐场景               |
|---------------|-------------------------------|------------------------|
| **Jest**      | 开箱即用、快照测试强大        | CRA、多数 React 项目   |
| **Vitest**    | 基于 Vite，速度极快           | Vite + Vue/React 项目  |
| **React Testing Library** | 测试组件行为而非实现细节     | React 组件测试         |
| **Vue Test Utils** | Vue 官方测试工具            | Vue 项目               |

本文以 **Vitest**（推荐新手）为例演示，因为它配置简单、速度快，和 Vite 项目无缝集成。

## 4. 环境准备（MacBook + Node）

```bash
# 1. 创建项目（示例）
mkdir js-unit-test-guide && cd js-unit-test-guide
npm create vite@latest . -- --template react-ts

# 2. 安装测试依赖
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

**配置 `vite.config.ts`**（或 `vite.config.js`）：

```ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'jsdom',
    globals: true,
    setupFiles: './src/test/setup.ts',
  },
});
```

**package.json** 中添加脚本：

```json
{
  "scripts": {
    "test": "vitest",
    "test:ui": "vitest --ui",
    "coverage": "vitest run --coverage"
  }
}
```

## 5. 如何编写单元测试？

### 5.1 基础函数测试

```ts
// src/utils/math.ts
export function add(a: number, b: number): number {
  return a + b;
}

export function divide(a: number, b: number): number {
  if (b === 0) throw new Error('除数不能为0');
  return a / b;
}
```

```ts
// src/utils/math.test.ts
import { describe, it, expect } from 'vitest';
import { add, divide } from './math';

describe('数学工具函数', () => {
  it('add 应该正确相加', () => {
    expect(add(1, 2)).toBe(3);
    expect(add(-1, 1)).toBe(0);
  });

  it('divide 应该抛出错误', () => {
    expect(() => divide(10, 0)).toThrow('除数不能为0');
  });
});
```

**常用断言**：
- `expect(value).toBe(expected)` —— 严格相等
- `expect(value).toEqual(expected)` —— 深度相等（对象/数组）
- `expect(fn).toThrow()` —— 抛出异常

### 5.2 React 组件测试（推荐写法）

```tsx
// src/components/Button.tsx
import { useState } from 'react';

export default function Button({ label = 'Click me' }) {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(c => c + 1)}>
      {label} ({count})
    </button>
  );
}
```

```tsx
// Button.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { describe, it, expect } from 'vitest';
import Button from './Button';

describe('Button 组件', () => {
  it('点击后计数器应该增加', () => {
    render(<Button label="Increment" />);
    
    const button = screen.getByText('Increment (0)');
    fireEvent.click(button);
    
    expect(screen.getByText('Increment (1)')).toBeInTheDocument();
  });
});
```

## 6. 最佳实践（工程角度）

1. **测试文件名**：`xxx.test.ts` 或 `xxx.spec.ts`
2. **优先测试纯函数**，再测组件
3. **遵循 AAA 模式**：Arrange（准备数据）→ Act（执行操作）→ Assert（验证结果）
4. **Mock 外部依赖**：`vi.fn()`、`vi.mock()`
5. **目标覆盖率**：新项目建议 60%~80%，核心业务模块尽量高
6. **CI 集成**：GitHub Actions / GitLab CI 自动跑 `npm test`
7. **不要测试实现细节**，重点测**输入输出和用户行为**

## 7. 总结与下一步

单元测试不是额外负担，而是**投资回报率极高**的工程实践。它让你写出更清晰、更健壮的代码，在团队协作和长期维护中优势巨大。

**建议行动路线**：
1. 先在工具函数层开始写测试（最容易上手）
2. 逐步覆盖核心组件
3. 接入 CI，养成“先写测试再改代码”的习惯（TDD 可选）