大家好！今天我们来聊一个单元测试中至关重要的话题：**测试数据的准备**。

我们都知道，测试的核心是“输入 -> 处理 -> 断言输出”。而这个“输入”——也就是测试数据——的准备方式，直接决定了你的测试是否**清晰、可读、易维护**。

在这篇文章里，我们将探讨 Vitest 中准备数据的**三种核心模式**，并介绍所有开发者都应该遵守的**两大黄金原则**。

---

## 第一部分：三大核心数据准备模式

首先，我们快速回顾一下组织测试数据的三种常见模式。

### 1. 内联 (In-line) - 最直接

将数据直接写在测试用例内部。它清晰、直观，非常适合那些简单且一次性的数据。

```typescript
it('should return the sum of two numbers', () => {
  // 数据是内联的
  const a = 5;
  const b = 10;
  expect(sum(a, b)).toBe(15);
});
```

### 2. 委托 (Delegated) - 可复用

通过一个辅助函数（工厂函数）来创建数据。当多个测试需要相似结构的数据时，这种方式可以有效减少重复代码。

```typescript
// test-utils.ts
const createUser = (overrides = {}) => {
  return { id: 1, name: 'Default User', isAdmin: false, ...overrides };
};

// component.test.ts
it('should show the admin panel for admin users', () => {
  // 通过委托创建数据
  const adminUser = createUser({ isAdmin: true });
  // ... render component with adminUser
});
```

### 3. 隐式 (Implicit) - 关注点分离

从外部文件（如 `.json`）加载数据，我们称之为 Fixture。这种方式非常适合模拟大型、静态的数据结构，比如一个完整的 API 响应。

```typescript
// fixtures/user-profile.json
// { "id": 123, "name": "John Doe", "permissions": ["read", "write"] }

// profile.test.ts
import userProfile from './fixtures/user-profile.json';

it('should display user permissions', () => {
  // 隐式地从文件加载数据
  renderProfile(userProfile);
  // ... assert that permissions are displayed
});
```

---

## 第二部分：两大黄金数据准备原则

掌握了以上三种模式后，我们还需要两个原则来指导我们做出正确的选择，写出更高质量的测试。

### 原则一：最小准备数据原则

> **核心思想：在准备测试数据时，只提供与当前测试功能强相关的数据。任何无关的数据都应该省略。**

这个原则的目的是**提升测试的可读性**。当别人（或未来的你）读到这个测试时，能立刻明白哪些数据是影响测试结果的关键。

让我们看一个反面教材和一个正面教材。

**场景**：我们要测试一个函数 `canDeletePost`，它只关心用户是否为管理员(`isAdmin`)或帖子的作者(`isAuthor`)。

**❌ 糟糕的例子 (提供了过多无关数据):**

```typescript
it('should allow an admin to delete any post', () => {
  const adminUser = {
    id: 'user-1',
    username: 'admin_user',
    email: 'admin@example.com',
    createdAt: '2023-01-01T00:00:00Z',
    profile: {
      avatar: 'url/to/avatar.jpg',
      bio: 'I am an admin.'
    },
    // --- 关键数据在这里 ---
    isAdmin: true 
  };
  const post = {
    id: 'post-123',
    authorId: 'user-2', // 不是作者
    title: 'A Post Title',
    content: 'Some content here.'
  };

  expect(canDeletePost(adminUser, post)).toBe(true);
});
```

看到问题了吗？为了验证 `isAdmin: true` 这一个关键点，我们提供了一大堆无关信息（`email`, `createdAt`, `profile`...）。这会让读者感到困惑：“难道 `email` 字段也和删除权限有关吗？”

**✅ 优秀的例子 (遵循最小数据原则):**

```typescript
it('should allow an admin to delete any post', () => {
  // 只提供最关键的数据
  const adminUser = { isAdmin: true }; 
  const post = { authorId: 'user-2' };

  // 使用 as any 或类型断言来满足 TypeScript
  expect(canDeletePost(adminUser as User, post as Post)).toBe(true);
});
```

这个版本一目了然！任何人都能立刻看出，这个测试的重点在于验证 `isAdmin` 属性。这就是高可读性的单元测试。

---

### 原则二：“后门操作”准备数据

> **核心思想：当一个系统的状态难以通过其公开的 API 或函数参数来设置时，我们可以通过“后门”直接修改其内部状态来为测试做准备。**

这个听起来有点像“作弊”，但在测试中却是非常强大和必要的技巧。

**场景**：假设我们有一个简单的身份验证 Pinia/Zustand store，一个组件的显示与否依赖于这个 store 的 `isAuthenticated` 状态。

`auth.store.ts`:

```typescript
const useAuthStore = create(() => ({
  isAuthenticated: false,
  user: null,
  login: async (user, pass) => { /* ... 复杂的 API 调用逻辑 ... */ },
}));
```

如果我们想测试当用户已登录时，`UserProfile` 组件是否正确显示。

**❌ 低效的方式 (走前门):**

```typescript
it('should display user profile when logged in', async () => {
  const store = useAuthStore();
  // 为了设置 isAuthenticated = true, 我们被迫调用了 login 函数
  // 这意味着我们还需要 mock 一个 API 请求，非常麻烦
  await store.login('test', 'password'); 
  
  render(<UserProfile />);
  // ... 断言
});
```

**✅ 高效的方式 (走后门):**

```typescript
import { useAuthStore } from './auth.store';

it('should display user profile when logged in', () => {
  // 直接通过“后门”修改 store 的内部状态
  useAuthStore.setState({ isAuthenticated: true, user: { name: 'Test User' } });

  render(<UserProfile />);
  
  expect(screen.getByText('Welcome, Test User')).toBeInTheDocument();
});
```

通过 `setState`，我们绕过了复杂的 `login` 流程，直接将系统置于我们需要的状态。这让我们的测试更**专注、更快、更稳定**。

“后门操作”的常见例子包括：

- 直接修改一个 store 的状态。
- 在测试数据库中预先插入数据。
- Mock 一个全局单例的状态。

## 总结

写好单元测试，数据准备是关键。请记住：

1. **选择合适的模式**：从**内联**开始，根据需要重构为**委托**或**隐式**。
2. **遵循最小数据原则**：只提供必要的数据，让你的测试意图清晰可见。
3. **善用后门操作**：当准备状态很复杂时，果断地直接修改内部状态，让测试保持简单和专注。

将这三大模式和两大原则结合起来，你就能写出既健壮又优雅的测试代码。希望对你有所帮助！