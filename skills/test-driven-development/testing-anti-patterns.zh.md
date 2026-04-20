# 测试反模式

**在以下情况加载此参考文档：** 编写或修改测试、添加 mock、或想要在生产代码中添加仅用于测试的方法时。

## 概述

测试必须验证真实行为，而非 mock 行为。Mock 是一种隔离手段，而非被测试的对象。

**核心原则：** 测试代码做了什么，而非 mock 做了什么。

**严格遵循 TDD 可以防止这些反模式。**

## 铁律

```
1. 绝不测试 mock 行为
2. 绝不在生产类中添加仅用于测试的方法
3. 绝不在不理解依赖关系的情况下使用 mock
```

## 反模式 1：测试 Mock 行为

**违规行为：**
```typescript
// ❌ 错误：测试 mock 是否存在
test('renders sidebar', () => {
  render(<Page />);
  expect(screen.getByTestId('sidebar-mock')).toBeInTheDocument();
});
```

**为什么这是错误的：**
- 你验证的是 mock 能工作，而不是组件能工作
- 测试在 mock 存在时通过，不存在时失败
- 对真实行为没有任何帮助

**你的人类搭档的纠正：** "我们是在测试 mock 的行为吗？"

**修复方法：**
```typescript
// ✅ 正确：测试真实组件或不使用 mock
test('renders sidebar', () => {
  render(<Page />);  // 不 mock sidebar
  expect(screen.getByRole('navigation')).toBeInTheDocument();
});

// 或者，如果必须 mock sidebar 以实现隔离：
// 不要对 mock 进行断言——测试 Page 在 sidebar 存在时的行为
```

### 门控函数

```
在对任何 mock 元素进行断言之前：
  问自己："我是在测试真实组件的行为还是仅仅测试 mock 的存在？"

  如果是在测试 mock 的存在：
    停止——删除该断言或取消 mock 该组件

  改为测试真实行为
```

## 反模式 2：在生产代码中添加仅用于测试的方法

**违规行为：**
```typescript
// ❌ 错误：destroy() 仅在测试中使用
class Session {
  async destroy() {  // 看起来像生产 API！
    await this._workspaceManager?.destroyWorkspace(this.id);
    // ... 清理
  }
}

// 在测试中
afterEach(() => session.destroy());
```

**为什么这是错误的：**
- 生产类被仅用于测试的代码污染
- 如果在生产环境中被意外调用会很危险
- 违反了 YAGNI 和关注点分离原则
- 混淆了对象生命周期和实体生命周期

**修复方法：**
```typescript
// ✅ 正确：测试工具处理测试清理
// Session 没有 destroy()——它在生产环境中是无状态的

// 在 test-utils/ 中
export async function cleanupSession(session: Session) {
  const workspace = session.getWorkspaceInfo();
  if (workspace) {
    await workspaceManager.destroyWorkspace(workspace.id);
  }
}

// 在测试中
afterEach(() => cleanupSession(session));
```

### 门控函数

```
在向生产类添加任何方法之前：
  问自己："这个方法是否仅被测试使用？"

  如果是：
    停止——不要添加它
    将其放在测试工具中

  问自己："这个类是否拥有该资源的生命周期？"

  如果否：
    停止——这个方法不属于这个类
```

## 反模式 3：不理解依赖就使用 Mock

**违规行为：**
```typescript
// ❌ 错误：Mock 破坏了测试逻辑
test('detects duplicate server', () => {
  // Mock 阻止了测试所依赖的配置写入！
  vi.mock('ToolCatalog', () => ({
    discoverAndCacheTools: vi.fn().mockResolvedValue(undefined)
  }));

  await addServer(config);
  await addServer(config);  // 应该抛出异常——但不会！
});
```

**为什么这是错误的：**
- 被 mock 的方法具有测试所依赖的副作用（写入配置）
- 为了"安全"而过度 mock 破坏了实际行为
- 测试因为错误的原因通过或因为莫名其妙的原因失败

**修复方法：**
```typescript
// ✅ 正确：在正确的层级进行 mock
test('detects duplicate server', () => {
  // Mock 慢的部分，保留测试需要的行为
  vi.mock('MCPServerManager'); // 只 mock 慢速的服务器启动

  await addServer(config);  // 配置被写入
  await addServer(config);  // 检测到重复 ✓
});
```

### 门控函数

```
在 mock 任何方法之前：
  停止——先不要 mock

  1. 问自己："真实方法有什么副作用？"
  2. 问自己："这个测试是否依赖这些副作用中的任何一个？"
  3. 问自己："我是否完全理解这个测试需要什么？"

  如果依赖副作用：
    在更低层级进行 mock（实际的慢速/外部操作）
    或使用保留必要行为的测试替身
    而不是测试所依赖的高层方法

  如果不确定测试需要什么：
    先用真实实现运行测试
    观察实际需要发生什么
    然后在正确的层级添加最小化的 mock

  危险信号：
    - "我 mock 这个以防万一"
    - "这可能会慢，最好 mock 掉"
    - 在不理解依赖链的情况下进行 mock
```

## 反模式 4：不完整的 Mock

**违规行为：**
```typescript
// ❌ 错误：部分 mock——只有你认为需要的字段
const mockResponse = {
  status: 'success',
  data: { userId: '123', name: 'Alice' }
  // 缺少：下游代码使用的 metadata
};

// 之后：当代码访问 response.metadata.requestId 时崩溃
```

**为什么这是错误的：**
- **部分 mock 隐藏了结构假设** —— 你只 mock 了你知道的字段
- **下游代码可能依赖你未包含的字段** —— 静默失败
- **测试通过但集成失败** —— Mock 不完整，真实 API 是完整的
- **虚假的信心** —— 测试对真实行为没有任何证明

**铁律：** Mock 必须是现实中存在的完整数据结构，而不仅仅是你当前测试使用的字段。

**修复方法：**
```typescript
// ✅ 正确：完整反映真实 API
const mockResponse = {
  status: 'success',
  data: { userId: '123', name: 'Alice' },
  metadata: { requestId: 'req-789', timestamp: 1234567890 }
  // 真实 API 返回的所有字段
};
```

### 门控函数

```
在创建 mock 响应之前：
  检查："真实 API 响应包含哪些字段？"

  操作：
    1. 从文档/示例中检查实际 API 响应
    2. 包含系统下游可能消费的所有字段
    3. 验证 mock 是否完全匹配真实响应的 schema

  关键：
    如果你在创建 mock，你必须理解整个结构
    当代码依赖被省略的字段时，部分 mock 会静默失败

  如果不确定：包含所有文档记录的字段
```

## 反模式 5：将集成测试作为事后补充

**违规行为：**
```
✅ 实现完成
❌ 没有编写测试
"准备好测试了"
```

**为什么这是错误的：**
- 测试是实现的一部分，不是可选的后续步骤
- TDD 本可以捕获到这个问题
- 没有测试就不能声称完成

**修复方法：**
```
TDD 循环：
1. 编写失败的测试
2. 实现使其通过
3. 重构
4. 然后才能声称完成
```

## 当 Mock 变得过于复杂时

**警告信号：**
- Mock 设置比测试逻辑还长
- 为了让测试通过而 mock 所有东西
- Mock 缺少真实组件具有的方法
- 当 mock 改变时测试就崩溃

**你的人类搭档的问题：** "我们这里真的需要使用 mock 吗？"

**考虑：** 使用真实组件的集成测试通常比复杂的 mock 更简单

## TDD 如何防止这些反模式

**为什么 TDD 有效：**
1. **先写测试** → 迫使你思考你实际在测试什么
2. **观察它失败** → 确认测试测的是真实行为，而非 mock
3. **最小化实现** → 不会悄悄加入仅用于测试的方法
4. **真实依赖** → 你在 mock 之前看到测试实际需要什么

**如果你在测试 mock 行为，你违反了 TDD** —— 你在没有先针对真实代码观察测试失败的情况下就添加了 mock。

## 快速参考

| 反模式 | 修复方法 |
|--------|----------|
| 对 mock 元素进行断言 | 测试真实组件或取消 mock |
| 在生产代码中添加仅用于测试的方法 | 移到测试工具中 |
| 不理解依赖就使用 mock | 先理解依赖关系，最小化 mock |
| 不完整的 mock | 完整反映真实 API |
| 测试作为事后补充 | TDD——先写测试 |
| 过于复杂的 mock | 考虑使用集成测试 |

## 危险信号

- 断言检查 `*-mock` 测试 ID
- 方法仅在测试文件中被调用
- Mock 设置占测试的 >50%
- 移除 mock 后测试失败
- 无法解释为什么需要 mock
- "以防万一" 地进行 mock

## 底线

**Mock 是隔离的工具，而非测试的对象。**

如果 TDD 揭示你在测试 mock 行为，那你就走错了方向。

修复方法：测试真实行为，或质疑为什么你要使用 mock。
