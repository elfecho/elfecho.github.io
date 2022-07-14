## 下载Excel

### 通过url下载

后端直接提供文件地址，直接使用浏览器进行下载

```javascript
// 方式一：
window.location.href = `${location.origin}/operation/ruleImport/template`
// 方式二
window.open(`${location.origin}/operation/ruleImport/template`)
```

这两种使用方法的不同：
- window.location：当前页跳转，也就是重新定位当前页；只能在网站中打开本网站的网页。
- window.open：在新窗口中打开链接；可以在网站上打开另外一个网站的地址。

