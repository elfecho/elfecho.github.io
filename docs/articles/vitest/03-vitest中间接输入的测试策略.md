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

```typescript
export const config = {
allowTellAge: true,
age: 18,
};

export const name = "cxr";
```

`src/use-variable.ts`:

```typescript
import { name } from './config';

export function tellName() {
return `${name}-heiheihei`;
}
```

**测试方法**：我们想在测试中临时改变 `name` 的值，来验证 `tellName` 函数的输出。

`src/use-variable.spec.ts`:

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

全局依赖是单元测试中最常见的“拦路虎”。它们就像程序中的“幽灵”，看不见摸不着，却实实在在地影响着代码的行为。在这一部分，我们将深入探讨如何使用 Vitest 控制三类主要的全局依赖：**环境变量**、**自定义全局变量**和**浏览器全局对象**。

对于每一类，我们都将探讨两种核心策略：

- **直接模拟**：使用 Vitest 提供的 `stub` API，快速、直接地修改全局状态。
- **架构解耦**：通过建立“间接层”来隔离全局依赖，让代码更清晰、更健壮。

---

#### 场景1：环境变量 (`process.env` 和 `import.meta.env`)

在 Node.js 中我们使用 `process.env`，在 Vite 项目中我们常用 `import.meta.env`。

**代码示例:** `src/user.ts`

```typescript
import { getEnvUserAge } from './env'; // 这是一个间接层函数

// 方式一：直接访问 import.meta.env
export function doubleUserAgeDirectly() {
  return Number(import.meta.env.VITE_USER_AGE) * 2;
}

// 方式二：通过间接层访问
export function doubleUserAgeWithLayer() {
  return Number(getEnvUserAge()) * 2;
}
```

`src/env.ts` (我们的间接层)

```typescript
export function getEnvUserAge() {
  return import.meta.env.VITE_USER_AGE;
}
```

**测试策略:**

`src/user.spec.ts`

```typescript
import { it, expect, describe, vi, afterEach } from 'vitest';
import { doubleUserAgeDirectly, doubleUserAgeWithLayer } from './user';
import { getEnvUserAge } from './env';

// 策略A：对于直接访问，使用 vi.stubEnv
it('doubleUserAgeDirectly should work with vi.stubEnv', () => {
  vi.stubEnv('VITE_USER_AGE', '18');
  const r = doubleUserAgeDirectly();
  expect(r).toBe(36);
  vi.unstubAllEnvs(); // 记得清理！
});

// 策略B：对于间接层，mock 我们自己的模块
vi.mock('./env'); // mock 间接层模块
it('doubleUserAgeWithLayer should work with vi.mock', () => {
  vi.mocked(getEnvUserAge).mockReturnValue('2'); // 控制间接层函数的返回值
  const r = doubleUserAgeWithLayer();
  expect(r).toBe(4);
});
```

**小结**：两种方式都可行。`vi.stubEnv` 更直接，而“间接层”让依赖关系更明确。

---

#### 场景2：自定义全局变量

有时，代码会依赖一个被挂载到全局作用域（`global` 或 `window`）的自定义对象。

**代码示例:** `src/user.ts`

```typescript
// 假设 zs 是一个被其他脚本挂载到全局的配置对象
// @ts-ignore
export function getDoubleZsAge() {
  return zs.age * 2;
}
```

**测试策略：**

`src/user.spec.ts`

```typescript
import { it, expect, describe, vi } from 'vitest';
import { getDoubleZsAge } from './user';

it('should work with custom global variable using vi.stubGlobal', () => {
  // 使用 vi.stubGlobal 来伪造一个全局变量 zs
  vi.stubGlobal('zs', { age: 18 });

  const r = getDoubleZsAge();
  expect(r).toBe(36);

  vi.unstubAllGlobals(); // 同样，记得清理
});
```

**小结**：对于自定义的全局变量，`vi.stubGlobal` 是最理想、最直接的工具。

---

#### 场景3：浏览器全局对象 (`window`, `document` 等)

这是最复杂的一类，因为 `window` 上的许多属性（如 `innerWidth`, `location`）是只读的或有复杂的行为，直接修改它们非常脆弱且不可靠。

**代码示例:** `src/window.ts` (间接层)

```typescript
// 我们不直接在业务代码里用 window.innerWidth
// 而是封装一个函数，让依赖变得明确
export function getInnerWidth() {
  return window.innerWidth;
}
```

`src/user.ts`

```typescript
import { getInnerWidth } from './window';

export function getDoubleInnerWidth() {
  // 业务代码依赖的是我们自己的模块，而不是全局的 window
  return getInnerWidth() * 2;
}
```

**测试策略：**

`src/user.spec.ts`

```typescript
import { it, expect, describe, vi } from 'vitest';
import { getDoubleInnerWidth } from './user';

// 对于 window 这种复杂的原生对象，最佳实践就是 mock 我们的间接层
vi.mock('./window.ts', () => {
  return {
    // 我们告诉测试，当调用 getInnerWidth 时，不要去读 window，直接返回 200
    getInnerWidth: () => 200,
  };
});

it('should get double inner width via abstraction layer', () => {
  const r = getDoubleInnerWidth();
  expect(r).toBe(400);
});

/*
// 为什么不这样做？(不推荐)
it("double inner width", () => {
  // 直接 stub window 上的属性可能在某些环境下失败，且不清晰
  vi.stubGlobal("innerWidth", 100); 
  const r = doubleInnerWidth();
  expect(r).toBe(200);
});
*/
```

**小结**：对于浏览器原生全局对象，**强烈推荐使用“间接层”的架构模式**。它能将不稳定的 `window` 依赖，转化为我们完全可控的、稳定的模块依赖。

### 最终总结：如何选择？

|依赖类型|推荐策略|Vitest API|备注|
|---|---|---|---|
|**环境变量**|**直接模拟** 或 **间接层**|`vi.stubEnv` / `vi.mock`|两者皆可，根据代码是否方便重构来选择。|
|**自定义全局变量**|**直接模拟**|`vi.stubGlobal`|这是最简单、最有效的方案。|
|**浏览器全局对象**|**强烈推荐间接层**|`vi.mock`|最稳定、最清晰的做法，能从根本上改善代码设计。|

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