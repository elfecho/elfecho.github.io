## 下载iconfont

阿里图标库：[https://www.iconfont.cn/](https://www.iconfont.cn/)

下载好iconfont压缩包后，提取iconfont.css和iconfont.ttf两个文件

## 字体转码

因为uni-app引入不了`ttf文件`需要对字体进行转码

将 iconfont.ttf文件转位 base64  
转码地址：[https://www.giftofspeed.com/base64-encoder/](https://www.giftofspeed.com/base64-encoder/)

## 转码内容替换

打开`iconfont.css`文件

![upgit_20220729_1659066134.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220729_1659066134.png)

内容替换

```css
@font-face {
  font-family: "iconfont"; /* Project id  */
  src: url('data:application/x-font-woff2;charset=utf-8;base64,转码内容粘贴到这里');
}
```

##  样式引入

```bash
<style lang="scss">
	/*每个页面公共css */
	@import '~@/static/iconfont/iconfont.css'
</style>
```