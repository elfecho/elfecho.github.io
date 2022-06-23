## 如何获取this

```javascript
import { getCurrentInstance } from 'vue'
const { proxy }: any = getCurrentInstance(); // 这里的proxy类似于vue2的this
```

## 如何定义全局属性

我们vue2可以通过添加vue的原型链上方法进行新增全局属性

```javascript
vue.prototype.$message = Message
```

vue