## 1. 原理

- `xgplayer` 本身不会自动生成缩略图（因为那需要读取视频帧，浏览器实时生成会非常耗资源）。
- 一般做法：
    - 提前在视频转码的时候额外生成 **雪碧图（sprite sheet）**，比如一张大图包含很多缩略图
    - 生成一份 **WebVTT 文件**，标记每个缩略图对应的时间段和图片位置
    - 播放器读取这个 VTT 文件，在鼠标移到进度条某个时间点时显示对应的小图

---

## 2. 官方使用方式

`xgplayer` 提供了 `thumbnail` 插件，可以配 VTT 预览：

ts

```ts
import Player from 'xgplayer'
import 'xgplayer/dist/index.min.css'

new Player({
  id: 'mse',
  url: 'https://example.com/video.mp4',
  controls: true,
  thumbnail: {
    // 预览图的 VTT 文件路径（WEBVTT 格式）
    pic: 'https://example.com/thumbs.vtt'
  }
})
```

---

### 3. VTT 文件格式示例

假设你有一张雪碧图 `sprite.jpg`（包含很多小缩略图，大小 160×90），VTT 写法如下：

vtt

```vtt
WEBVTT

00:00.000 --> 00:10.000
sprite.jpg#xywh=0,0,160,90

00:10.000 --> 00:20.000
sprite.jpg#xywh=160,0,160,90

00:20.000 --> 00:30.000
sprite.jpg#xywh=320,0,160,90
```

解释：

- `sprite.jpg#xywh=x,y,w,h` 表示从雪碧图中取 `(x,y)` 锚点的 `w×h` 区域
- 不同时间段映射不同的缩略图位置

---

## 4. 你的项目里加法

在你的 `videoPlayer.vue` 里直接加 thumbnail 的配置就可以：

ts

```ts
playerInstance = new Player({
  id: 'mse',
  url: 'https://lf3-static.bytednsdoc.com/obj/eden-cn/nupenuvpxnuvo/xgplayer_doc/xgplayer-demo-720p.mp4',
  controls: true,
  autoplay: false,
  thumbnail: {
    pic: 'https://yourdomain.com/thumbs.vtt'
  },
  controlPlugins: [
    'play',
    Rewind10Btn,
    Forward10Btn
  ]
})
```

---

## 5. 关键点

1. **必须自己准备缩略图资源**
    - 用 ffmpeg 生成：
        
        ```bash
        # 每 10 秒生成一帧图
        ffmpeg -i video.mp4 -vf fps=1/10 thumb%04d.jpg
        ```
        
        再用工具（如 ImageMagick）拼成一张雪碧图。
2. **必须写好对应的 VTT 文件**
3. 浏览器加载视频时，会额外请求 VTT 里的 `sprite.jpg`，并根据 URL 片段 `#xywh` 裁剪显示。

---

💡 **总结**：

- xgplayer 支持进度条预览功能（thumbnail）
- 需要 VTT 预览图配置
- 我可以帮你写一个 **ffmpeg 脚本**，直接从你的视频批量生成缩略图雪碧图 + 对应 VTT 文件，这样你直接丢到服务器就能用。

---

你要我帮你写一份 **ffmpeg 一键生成缩略图 + VTT** 的完整命令吗？这样你的预览功能可以马上用起来。这样会省掉你自己去手写 VTT 的步骤。