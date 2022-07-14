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

### 通过 a 标签 download 属性结合 blob 构造函数下载

a 标签的 download 属性是 HTML5 标准新增的，作用是触发浏览器的下载操作而不是导航到下载的 url，这个属性可以设置下载时使用新的文件名称。

前端创建超链接，接收后端的文件流：

如果有传参的话，需要下载qs包

```javascript
import qs from 'qs'
axios(
	method: 'GET',
	url: `/operation/ruleImport/template`,
	paramsSerializer: (params) => {
      return qs.stringify(params, { arrayFormat: 'brackets' })
    },
	responseType: "blob" //服务器响应的数据类型，可以是 'arraybuffer', 'blob', 'document', 'json', 'text', 'stream'，默认是'json'
    })
    .then(res => 
        if(!res) return
        const blob = new Blob([res.data], { type: 'application/vnd.ms-excel' }) // 构造一个blob对象来处理数据，并设置文件类型
         
        if (window.navigator.msSaveOrOpenBlob) { //兼容IE10
            navigator.msSaveBlob(blob, this.filename)
        } else {
            const href = URL.createObjectURL(blob) //创建新的URL表示指定的blob对象
            const a = document.createElement('a') //创建a标签
            a.style.display = 'none'
            a.href = href // 指定下载链接
            a.download = this.filename //指定下载文件名
            a.click() //触发下载
        }
        // 这里也可以不创建a链接，直接window.open(href)也能下载
    })
    .catch(err => {
        console.log(err)
    })
```

注：请求后台接口时要在请求头上加{responseType: 'blob'}；download 设置文件名时，可以直接设置扩展名，如果没有设置浏览器将自动检测正确的文件扩展名并添加到文件。

### 通过 js-file-download 插件

安装

### 使用fetch下载

```

fetch(
	'http://127.0.0.1:8765/course/exportCourse/33', 
	{
		  method: 'GET',
		  headers: new Headers({
		  'Authorization': Cookie.get('Authorization') 
	}),
})
.then(res => res.blob())
.then(data => {
	const blobUrl = window.URL.createObjectURL(data);
	const a = document.createElement('a');
	a.download = this.fileName+'.xlsx';
	a.href = blobUrl;
	a.click();
});


```