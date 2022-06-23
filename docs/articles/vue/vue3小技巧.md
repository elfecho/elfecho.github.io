## 如何定义全局属性

我们vue2可以通过添加vue的原型链上方法进行新增全局属性

```javascript
vue.prototype.$message = Message
```

vue3可以使用以下方法一样可以实现全局属性的配置

```javascript
app.config.globalProperties.$message = ElMessage
```

## 如何获取this

```javascript
import { getCurrentInstance } from 'vue'
const { proxy }: any = getCurrentInstance(); // 这里的proxy类似于vue2的this
```
