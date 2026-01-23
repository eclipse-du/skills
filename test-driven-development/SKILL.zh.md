---
name: test-driven-development
description: 在实施任何功能或错误修复时，在编写实施代码之前使用
---

# 测试驱动开发 (TDD)

## 概述

先写测试。看着它失败。编写最少的代码通过。

**核心原则：** 如果你没看着测试失败，你就不知道它是否测试了正确的东西。

**违反规则的字面意思就是违反规则的精神。**

## 何时使用

**总是：**
- 新功能
- 错误修复
- 重构
- 行为改变

**例外（询问你的人类伙伴）：**
- 一次性原型
- 生成的代码
- 配置文件

想着“就这一次跳过 TDD”？停止。那是借口。

## 铁律

```
没有先行的失败测试，就没有生产代码
```

在测试之前写代码？删除它。重新开始。

**无例外：**
- 不要保留它作为“参考”
- 不要在写测试时“调整”它
- 不要看它
- 删除意味着删除

从测试重新实施。句号。

## 红-绿-重构

```dot
digraph tdd_cycle {
    rankdir=LR;
    red [label="红\n编写失败测试", shape=box, style=filled, fillcolor="#ffcccc"];
    verify_red [label="验证失败\n正确", shape=diamond];
    green [label="绿\n最少代码", shape=box, style=filled, fillcolor="#ccffcc"];
    verify_green [label="验证通过\n全绿", shape=diamond];
    refactor [label="重构\n清理", shape=box, style=filled, fillcolor="#ccccff"];
    next [label="下一个", shape=ellipse];

    red -> verify_red;
    verify_red -> green [label="是"];
    verify_red -> red [label="错误的\n失败"];
    green -> verify_green;
    verify_green -> refactor [label="是"];
    verify_green -> green [label="否"];
    refactor -> verify_green [label="保持\n绿色"];
    verify_green -> next;
    next -> red;
}
```

### 红 - 编写失败测试

编写一个显示应该发生什么的最小测试。

<好>
```typescript
test('retries failed operations 3 times', async () => {
  let attempts = 0;
  const operation = () => {
    attempts++;
    if (attempts < 3) throw new Error('fail');
    return 'success';
  };

  const result = await retryOperation(operation);

  expect(result).toBe('success');
  expect(attempts).toBe(3);
});
```
清晰的名称，测试真实行为，一件事
</好>

<坏>
```typescript
test('retry works', async () => {
  const mock = jest.fn()
    .mockRejectedValueOnce(new Error())
    .mockRejectedValueOnce(new Error())
    .mockResolvedValueOnce('success');
  await retryOperation(mock);
  expect(mock).toHaveBeenCalledTimes(3);
});
```
模糊的名称，测试模拟而不是代码
</坏>

**要求：**
- 一个行为
- 清晰的名称
- 真实代码（除非不可避免，否则无模拟）

### 验证红 - 看着它失败

**强制性。绝不跳过。**

```bash
npm test path/to/test.test.ts
```

确认：
- 测试失败（不是错误）
- 失败消息是预期的
- 失败因为功能缺失（不是拼写错误）

**测试通过？** 你在测试现有行为。修复测试。

**测试错误？** 修复错误，重新运行直到它正确失败。

### 绿 - 最少代码

编写最简单的代码通过测试。

<好>
```typescript
async function retryOperation<T>(fn: () => Promise<T>): Promise<T> {
  for (let i = 0; i < 3; i++) {
    try {
      return await fn();
    } catch (e) {
      if (i === 2) throw e;
    }
  }
  throw new Error('unreachable');
}
```
足以通过
</好>

<坏>
```typescript
async function retryOperation<T>(
  fn: () => Promise<T>,
  options?: {
    maxRetries?: number;
    backoff?: 'linear' | 'exponential';
    onRetry?: (attempt: number) => void;
  }
): Promise<T> {
  // YAGNI
}
```
过度工程
</坏>

不要添加功能，重构其他代码，或超出测试范围进行“改进”。

### 验证绿 - 看着它通过

**强制性。**

```bash
npm test path/to/test.test.ts
```

确认：
- 测试通过
- 其他测试仍然通过
- 输出原始（无错误，警告）

**测试失败？** 修复代码，不是测试。

**其他测试失败？** 现在修复。

### 重构 - 清理

仅在变绿后：
- 移除重复
- 改进名称
- 提取助手

保持测试绿色。不要添加行为。

### 重复

下一个功能的下一个失败测试。

## 好的测试

| 质量 | 好 | 坏 |
|---------|------|-----|
| **最小** | 一件事。名称中有“and”？拆分它。 | `test('validates email and domain and whitespace')` |
| **清晰** | 名称描述行为 | `test('test1')` |
| **显示意图** | 演示所需的 API | 掩盖代码应该做什么 |

## 为什么顺序很重要

**“我会在验证它工作后编写测试”**

代码之后的测试立即通过。立即通过证明不了什么：
- 可能测试错误的东西
- 可能测试实施，而不是行为
- 可能错过你忘记的边缘情况
- 你从未看到它捕获错误

先测试迫使你看到测试失败，证明它实际上测试了一些东西。

**“我已经手动测试了所有边缘情况”**

手动测试是临时的。你认为你测试了一切但：
- 没有你测试内容的记录
- 代码更改时无法重新运行
- 在压力下容易忘记情况
- “我试过时它能用” ≠ 全面

自动化测试是系统的。它们每次都以相同的方式运行。

**“删除 X 小时的工作是浪费”**

沉没成本谬误。时间已经过去了。你现在的选择：
- 删除并用 TDD 重写（X 更多小时，高信心）
- 保留并在之后添加测试（30 分钟，低信心，可能有错误）

“浪费”是保留你不能信任的代码。没有真实测试的工作代码是技术债务。

**“TDD 是教条式的，务实意味着适应”**

TDD **是**务实的：
- 在提交前发现错误（比以后调试更快）
- 防止回归（测试立即捕获破坏）
- 记录行为（测试显示如何使用代码）
- 启用重构（自由更改，测试捕获破坏）

“务实”的捷径 = 在生产中调试 = 更慢。

**“之后的测试达到相同的目标 - 是精神不是仪式”**

不。之后的测试回答“这做什么？”之前的测试回答“这应该做什么？”

之后的测试受你的实施偏见。你测试你构建的东西，而不是需要的东西。你验证记住的边缘情况，而不是发现的。

之前的测试迫使实施前发现边缘情况。之后的测试验证你记住了所有事情（你没有）。

30 分钟的之后测试 ≠ TDD。你获得了覆盖率，失去了测试工作的证明。

## 常见借口

| 借口 | 现实 |
|--------|---------|
| “太简单无法测试” | 简单代码会坏。测试只需 30 秒。 |
| “我会之后测试” | 立即通过的测试证明不了什么。 |
| “之后的测试达到相同目标” | 之后的测试 = “这做什么？” 之前的测试 = “这应该做什么？” |
| “已经手动测试过了” | 临时 ≠ 系统。无记录，无法重新运行。 |
| “删除 X 小时是浪费” | 沉没成本谬误。保留未验证的代码是技术债务。 |
| “保留作为参考，先写测试” | 你会适应它。那是之后测试。删除意味着删除。 |
| “需要先探索” | 好的。扔掉探索，从 TDD 开始。 |
| “测试难 = 设计不清楚” | 听从测试。难测试 = 难使用。 |
| “TDD 会减慢我的速度” | TDD 比调试快。务实 = 先测试。 |
| “手动测试更快” | 手动证明不了边缘情况。你会为每个更改重新测试。 |
| “现有代码没有测试” | 你在改进它。为现有代码添加测试。 |

## 危险信号 - 停止并重新开始

- 测试前的代码
- 实施后的测试
- 测试立即通过
- 无法解释为什么测试失败
- “稍后”添加测试
- 借口“就这一次”
- “我已经手动测试过了”
- “之后的测试达到相同的目的”
- “是精神不是仪式”
- “保留作为参考”或“适应现有代码”
- “已经花了 X 小时，删除是浪费”
- “TDD 是教条式的，我很务实”
- “这是不同的，因为...”

**所有这些意味着：删除代码。用 TDD 重新开始。**

## 示例：错误修复

**错误：** 接受空电子邮件

**红**
```typescript
test('rejects empty email', async () => {
  const result = await submitForm({ email: '' });
  expect(result.error).toBe('Email required');
});
```

**验证红**
```bash
$ npm test
FAIL: expected 'Email required', got undefined
```

**绿**
```typescript
function submitForm(data: FormData) {
  if (!data.email?.trim()) {
    return { error: 'Email required' };
  }
  // ...
}
```

**验证绿**
```bash
$ npm test
PASS
```

**重构**
如果需要，提取多字段验证。

## 验证清单

在标记工作完成之前：

- [ ] 每个新函数/方法都有一个测试
- [ ] 实施前看着每个测试失败
- [ ] 每个测试因预期原因失败（功能缺失，不是拼写错误）
- [ ] 编写最少代码通过每个测试
- [ ] 所有测试通过
- [ ] 输出原始（无错误，警告）
- [ ] 测试使用真实代码（仅在一个不可避免时模拟）
- [ ] 覆盖边缘情况和错误

无法勾选所有框？你跳过了 TDD。重新开始。

## 卡住时

| 问题 | 解决方案 |
|---------|----------|
| 不知道如何测试 | 编写期望的 API。先写断言。询问你的人类伙伴。 |
| 测试太复杂 | 设计太复杂。简化接口。 |
| 必须模拟一切 | 代码太耦合。使用依赖注入。 |
| 测试设置巨大 | 提取助手。仍然复杂？简化设计。 |

## 调试集成

发现错误？编写复现它的失败测试。遵循 TDD 循环。测试证明修复并防止回归。

永远不要在没有测试的情况下修复错误。

## 测试反模式

当添加模拟或测试实用程序时，阅读 @testing-anti-patterns.md 以避免常见陷阱：
- 测试模拟行为而不是真实行为
- 向生产类添加仅测试方法
- 模拟而不理解依赖项

## 最终规则

```
生产代码 → 测试存在且首先失败
否则 → 不是 TDD
```

未经你的人类伙伴许可，无例外。
