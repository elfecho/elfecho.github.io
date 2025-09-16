大家好！在之前的讨论中，我们聊了如何准备“直接输入”，也就是传入函数的参数。但一个健壮的程序，其行为不仅由直接参数决定，还受到大量**间接输入**的影响。

什么是间接输入？简单来说，就是**函数运行时依赖的所有外部因素**，例如它调用的其他函数、引用的第三方库、当前的环境变量，甚至是全局的 `window` 对象。无法有效控制这些间接输入，我们的单元测试就无从谈起。

今天，我们就来深入探讨四种处理间接输入的强大技术。

---

### 依赖函数调用 - Stub的应用

最常见的间接输入，就是一个函数对另一个函数的调用。我们测试A函数时，可能并不关心它调用的B函数的具体实现，我们只关心B函数返回了某个值或执行了某个动作。

这时，我们就需要 **Stub（桩）**。Stub允许我们用一个“假”的实现替换掉真实的函数，从而在测试中完全控制它。

**场景**：一个 `calculateDiscount` 函数，它依赖另一个 `isPremiumUser` 函数来判断用户是否有资格享受额外折扣。

`src/user.ts`:

```typescript
import { isPremiumUser } from './auth';

export function calculateDiscount(userId: string, basePrice: number): number {
if (isPremiumUser(userId)) {
return basePrice * 0.8; // 高级用户8折
}
return basePrice * 0.95; // 普通用户9.5折
}
```

在测试 `calculateDiscount` 时，我们不想触发真实的 `isPremiumUser` 逻辑（它可能很复杂，需要查数据库）。我们只想简单地告诉测试：“假设这个用户是高级用户”或“假设他是普通用户”。

**测试方法**：使用 `vi.spyOn` 创建一个 Stub。

`src/user.test.ts`:

```typescript
import { describe, it, expect, vi } from 'vitest';
import * as auth from './auth'; // 导入整个模块
import { calculateDiscount } from './user';

describe('calculateDiscount', () => {
it('should give 20% discount to premium users', () => {
// 1. 创建一个 isPremiumUser 函数的 Stub
const isPremiumUserStub = vi.spyOn(auth, 'isPremiumUser');
// 2. 强制它返回 true
isPremiumUserStub.mockReturnValue(true);

const discountPrice = calculateDiscount('user-premium', 100);
expect(discountPrice).toBe(80);

// 最好在测试后恢复原始实现
isPremiumUserStub.mockRestore();
});

it('should give 5% discount to regular users', () => {
// 强制 Stub 返回 false
const isPremiumUserStub = vi.spyOn(auth, 'isPremiumUser').mockReturnValue(false);

const discountPrice = calculateDiscount('user-regular', 100);
expect(discountPrice).toBe(95);

isPremiumUserStub.mockRestore();
});
});
```

通过 `vi.spyOn`，我们精确地控制了间接输入 `isPremiumUser` 的返回值，从而可以独立地、确定地测试 `calculateDiscount` 的逻辑。

---

### 第三方库、对象与常量

当我们的间接输入来源是一个完整的第三方库（如 `axios`）、一个复杂的 Class 或是一个从别处导入的常量时，`vi.spyOn` 可能不够用。这时，我们需要在模块层面进行模拟，也就是使用 `vi.mock`。

#### 示例1：模拟第三方库 (如 `axios`)

**场景**：一个 `fetchUserProfile` 函数，使用 `axios` 库来获取用户数据。

`src/api.ts`:

typescript

```typescript
import axios from 'axios';

export async function fetchUserProfile(userId: string) {
try {
const response = await axios.get(`/users/${userId}`);
return response.data;
} catch (error) {
return null;
}
}
```

**测试方法**：使用 `vi.mock` 模拟整个 `axios` 模块，避免真实的网络请求。

`src/api.test.ts`:

typescript

```typescript
import { describe, it, expect, vi } from 'vitest';
import axios from 'axios';
import { fetchUserProfile } from './api';

// 告诉 Vitest：当代码中 'import axios' 时，使用我们提供的假对象
vi.mock('axios');

describe('fetchUserProfile', () => {
it('should return user data on successful fetch', async () => {
const userData = { id: 1, name: 'John Doe' };
vi.mocked(axios.get).mockResolvedValue({ data: userData });

const profile = await fetchUserProfile('1');

expect(profile).toEqual(userData);
expect(axios.get).toHaveBeenCalledWith('/users/1');
});
});
```

---

#### 示例2：模拟 Class (类)

**场景**：我们有一个 `NotificationService` 类负责发送通知。一个业务函数 `registerUser` 在成功后会实例化这个类并调用其 `send` 方法。

`src/services/notification.ts`:

typescript

```typescript
export class NotificationService {
constructor(private transport: 'email' | 'sms') {}

send(user: string, message: string) {
// 真实的发送逻辑...
console.log(`Sending ${this.transport} to ${user}: ${message}`);
return true;
}
}
```

`src/user.ts`:

typescript

```typescript
import { NotificationService } from './services/notification';

export function registerUser(name: string) {
// ... 用户注册逻辑 ...
const notifier = new NotificationService('email');
notifier.send(name, 'Welcome to our platform!');
return true;
}
```

**测试方法**：测试 `registerUser` 时，我们不希望它真的发送通知。我们只想确认 `NotificationService` 被正确地实例化和调用了。

`src/user.test.ts`:

typescript

```typescript
import { describe, it, expect, vi } from 'vitest';
import { NotificationService } from './services/notification';
import { registerUser } from './user';

// 模拟整个 notification 模块
vi.mock('./services/notification');

describe('registerUser', () => {
it('should create a notification service and send a welcome message', () => {
registerUser('Alice');

// 检查 NotificationService 的构造函数是否被调用，并且参数是 'email'
expect(NotificationService).toHaveBeenCalledTimes(1);
expect(NotificationService).toHaveBeenCalledWith('email');

// Vitest 的 vi.mock 会自动模拟 Class 的所有实例
// 我们可以通过 vi.mocked(ClassName).mock.instances 访问到所有被创建的实例
const mockInstance = vi.mocked(NotificationService).mock.instances[0];

// 检查实例的 send 方法是否被正确调用
expect(mockInstance.send).toHaveBeenCalledTimes(1);
expect(mockInstance.send).toHaveBeenCalledWith('Alice', 'Welcome to our platform!');
});
});
```

通过这种方式，我们完全控制了 `NotificationService` 这个 Class 的行为，验证了我们的业务逻辑，同时又将测试与真实的通知发送逻辑隔离开。

---

#### 示例3：模拟常量与对象

正如您所提供的精彩示例，`vi.mock` 同样可以用来修改导入的常量或配置对象。这在测试依赖不同配置的代码时非常有用。

**场景**：我们有一个配置文件导出了 `name` 常量。一个 `tellName` 函数使用了这个常量。

`src/config.ts`:

typescript

```typescript
export const config = {
allowTellAge: true,
age: 18,
};

export const name = "cxr";
```

`src/use-variable.ts`:

typescript

```typescript
import { name } from './config';

export function tellName() {
return `${name}-heiheihei`;
}
```

**测试方法**：我们想在测试中临时改变 `name` 的值，来验证 `tellName` 函数的输出。

`src/use-variable.spec.ts`:

typescript

```typescript
import { it, expect, describe, vi } from "vitest";
import { tellName } from "./use-variable";

// vi.mock 的第二个参数是一个工厂函数，可以动态创建 mock
// 这对于只修改模块部分导出而保留其他导出非常有用
vi.mock("./config", async (importOriginal) => {
// importOriginal 是一个函数，调用它会返回原始的、未被 mock 的模块
const originalModule = await importOriginal();

return {
...(originalModule as any), // 展开原始模块的所有导出
name: "c", // 覆盖 name 的值
};
});

describe("使用变量的形式", () => {
it("tell name", () => {
const r = tellName();
// 断言函数使用了我们 mock 后的值
expect(r).toBe("c-heiheihei");
});
});
```

这个例子展示了 `vi.mock` 的一个高级用法：通过**工厂函数**，我们可以读取原始模块的内容，然后返回一个修改过的版本。这确保了我们只修改了需要测试的常量（`name`），而 `config` 对象等其他导出保持不变。

---

### 环境变量与全局对象 - 间接层处理技巧

最棘手的间接输入往往来自全局，比如 `process.env` (Node.js 环境变量) 或 `window` (浏览器全局对象)。直接在代码中使用它们会让测试变得非常困难。

**技巧**：不要直接在业务逻辑中访问全局变量，而是创建一个“间接层”（或称“抽象层”），将全局依赖隔离起来。

**场景**：一个组件只在生产环境 (`process.env.NODE_ENV === 'production'`) 下才显示某个分析脚本。

**❌ 糟糕的设计 (直接访问):**

`src/Analytics.tsx`:

```tsx
const Analytics = () => {
// 业务逻辑与全局变量紧密耦合，难以测试
if (process.env.NODE_ENV === 'production') {
return <script src="analytics.js"></script>;
}
return null;
};
```

**✅ 优秀的设计 (使用间接层):**

首先，我们创建一个配置文件来隔离全局访问。

`src/config.ts`:

```typescript
export const isProduction = process.env.NODE_ENV === 'production';
export const appVersion = '1.0.0';
```

然后，业务代码依赖我们自己的配置文件。

`src/Analytics.tsx`:

```tsx
import { isProduction } from './config';

const Analytics = () => {
// 现在，依赖的是我们自己可控的模块
if (isProduction) {
return <script src="analytics.js"></script>;
}
return null;
};
```

**测试方法**：现在测试变得异常简单，我们只需 mock 我们自己的 `config.ts` 模块。

`src/Analytics.test.tsx`:

```typescript
import { render } from '@testing-library/react';
import { describe, it, vi } from 'vitest';
import Analytics from './Analytics';

// Mock 我们自己的 config 模块
vi.mock('./config', () => ({
isProduction: true, // 假设现在是生产环境
}));

describe('Analytics', () => {
it('should render script in production', () => {
const { container } = render(<Analytics />);
// 断言 script 标签存在
expect(container.querySelector('script')).not.toBeNull();
});
});
```

这个“间接层”技巧是处理环境依赖的行业标准，它让原本难以测试的代码变得清晰可控。

---

### 依赖注入 (Dependency Injection)

前面三种方法都是在“外部”通过 mock 来控制依赖，而依赖注入（DI）则是一种从“内部”改变代码设计，使其天生对测试友好的架构模式。

**核心思想**：一个模块不应该自己创建或查找其依赖，而应该由外部提供（“注入”）给它。

**场景**：一个 `UserService` 需要一个 `Logger` 服务来记录日志。

**❌ 紧耦合设计:**

`src/UserService.ts`:

```typescript
import { Logger } from './Logger';

class UserService {
private logger = new Logger(); // UserService 自己创建了依赖

createUser(name: string) {
// ... 创建用户逻辑 ...
this.logger.log(`User ${name} created.`);
}
}
```

测试这个 `UserService` 时，我们被迫要处理真实的 `Logger`，很麻烦。

**✅ 依赖注入设计:**

`src/UserService.ts`:

```typescript
import { ILogger } from './Logger'; // 依赖于接口而非具体实现

class UserService {
// 依赖通过构造函数被“注入”
constructor(private logger: ILogger) {}

createUser(name: string) {
// ... 创建用户逻辑 ...
this.logger.log(`User ${name} created.`);
}
}
```

**测试方法**：在测试中，我们可以轻松地注入一个假的 `Logger` 对象。

`src/UserService.test.ts`:

```typescript
describe('UserService', () => {
it('should log a message when a user is created', () => {
// 1. 创建一个假的 logger 对象（一个简单的 mock）
const mockLogger = {
log: vi.fn(), // 我们只关心 log 方法是否被调用
};

// 2. 将假的 logger 注入到 UserService 中
const userService = new UserService(mockLogger);
userService.createUser('Alice');

// 3. 断言 mockLogger 的 log 方法被正确调用了
expect(mockLogger.log).toHaveBeenCalledWith('User Alice created.');
});
});
```

依赖注入是一种更优雅的架构层面的解决方案。它让代码更加模块化，并且几乎不需要 `vi.mock` 或 `vi.spyOn` 就能进行简单的测试。

### 总结

掌握如何控制间接输入是编写高质量单元测试的必经之路。

- 对于**函数调用**，使用 `vi.spyOn` 创建 **Stub**。
- 对于**第三方库或模块**，使用 `vi.mock` 在模块层面进行替换。
- 对于**环境变量和全局对象**，建立**间接层**来解耦。
- 在架构层面，优先考虑**依赖注入**，让代码天生可测试。