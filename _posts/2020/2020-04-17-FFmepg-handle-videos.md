

推荐操作超级简单的视频剪辑器



`FFmpeg` 是一个跨平台的多媒体处理框架，能够编码、转码、多路复用、流媒体等操作，支持的格式从上古到至今，基本上还没有不支持的视频格式。

下载地址：
>https://www.ffmpeg.org/download.html

Macos安装方法也可以用，`brew install ffmpeg`，安装好了验证一下。输入 `ffmpeg`
![T1XUcw](https://gitee.com/chasays/mdPic/raw/master/uPic/T1XUcw.png)
输入 `man ffmpeg` 就可以看到如下结果。
![MYr0SC](https://gitee.com/chasays/mdPic/raw/master/uPic/MYr0SC.png)

## 常见的用法
主要从常用功能入手，比如剪切视频、格式转换，提取音频等。
### 格式转换
将视频从一个格式转换到另一个格式， 比如从mp4转换到avi
```
ffmpeg -i 2.mp4 -c copy 1.avi
```
![dS2kr5](https://gitee.com/chasays/mdPic/raw/master/uPic/dS2kr5.png)
这样就完成了。
![vzhZ4X](https://gitee.com/chasays/mdPic/raw/master/uPic/vzhZ4X.png)

### 改变分辨率

比如从1080p 转换到480p。
```
ffmpeg -i 2.mp4 -vf scale=480:-1 1.mp4
```

### 添加音轨

将外部音频加入视频，比如添加背景音乐或旁白。
```
 ffmpeg -i 2.MP3 -i 2.mp4 1.mp4
```
然后输出的1.MP4里面就包含了视频和声音文件。

### 剪切
这个属于无损剪切，就是之前视频是什么格式剪切之后就是什么格式的。这个非常适合Gopro等视频的拼接和剪切。
用法是：
```
ffmpeg -ss [start] -i [input] -t [duration] -c copy [output]
```
- start 是开始时间，比如 `00：00：01`，是个位数的时候，这个`0`也不能少。
- duration 就是持续时间， 需要剪切10s，那么这个值就是10
- `ffmpeg -ss [start] -i [input] -to [end] -c copy [output]` 这个里面有一个end，可以替换上面的结束时间。格式和开始时间一致。

下一篇文章推荐一个用 FFmpeg 开发的 GUI（图形界面）的操作软件。也是跨平台的，有Liunx、macOS、以及Windows。
