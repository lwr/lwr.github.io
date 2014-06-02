---
layout: post
tagline:
category: null
tags: []
published: true
title: 下载 4k 的 youtube 视频
---

今天尝试了下去下载个 4k 的油土鳖

源视频地址在这里:

> <http://www.youtube.com/watch?v=o_24LPjOIHI>

*10m* 的屌丝网络注定是无法愉快的观看的，硬来只能是卡的壹[哔-]
所以嘛，还是下载下来看比较省心。

## → → You Get!

首先想到的是之前用过的神器 [you-get](https://github.com/soimort/you-get)
这个神器最大的亮点是可以自动合并优酷和土豆那些恶心的分段视频
做了少量准备 (*proxifier* 挂全局代理) 后马上开动，然后没多久就下好了

~~~
you-get 'http://www.youtube.com/watch?v=o_24LPjOIHI'

Video Site: YouTube.com
Title:      NORTHERN INDIA  4K (Ultra HD) 50/60fps
Type:       MPEG-4 video (video/mp4)
Size:       79.75 MiB (83623035 Bytes)

Downloading NORTHERN INDIA  4K (Ultra HD) 50-60fps.mp4 ...
100.0% ( 79.7/79.7 MB) [========================================] 1/1
~~~

囧~~

显然这个下的是 720p 版本
众所周知（好吧如果你不知道，那就在前面自动脑补个【小】字），油土鳖的视频 1080 以上分辨率的都不带音频，是分离的两个文件，在浏览器上只有 flash 方式可以看到 1080 以上质量的视频，用自动转 html5 的插件都只能看到最高 720p 的质量

翻了下 you-get 的各个选项，貌似都还没有支持
那么，是去给作者发个 issue 呢还是发个 issue 呢？

... 此处略去 1000 字...

## → → Youtube-DL！

然后很快就搜到这个项目 [youtube-dl](https://github.com/rg3/youtube-dl)

由于之前的 you-get 是要求 pyphon3 的，我下意识的也用了 python3 安装，OK

```bash
↪ git:master)$ sudo pip-3.4 install youtube-dl
... ...
↪ git:master)$ youtube-dl
-bash: youtube-dl: command not found
```

这下子又囧了，马上检查了下环境设置

```bash
↪ git:master)$ cat ~/.pip/pip.conf
[install]
install-option = --install-scripts=/usr/local/bin
```

貌似完全没有问题啊，python 的世界我不懂，还是放弃治疗吧

突然想起 youtube-dl 是支持 python2 的，嗯，还是用系统原生的好了

```bash
↪ git:master)$ sudo easy_install youtube-dl
... ...
↪ git:master)$ youtube-dl
Usage: youtube-dl [options] url [url...]

youtube-dl: error: you must provide at least one URL
```

果然，这次可以成功安装到 `/usr/local/bin` 了，喔也

Youtube-DL 还是很强大的，可以列出所有的流，并且可以指定下载哪一个流

~~~bash
↪ git:master)$ youtube-dl -F 'http://www.youtube.com/watch?v=o_24LPjOIHI&noredirect=1'

[youtube] Setting language
[youtube] o_24LPjOIHI: Downloading webpage
[youtube] o_24LPjOIHI: Downloading video info webpage
[youtube] o_24LPjOIHI: Extracting video information
[youtube] o_24LPjOIHI: Encrypted signatures detected.
[info] Available formats for o_24LPjOIHI:
format code extension resolution  note
171         webm      audio only  DASH audio , audio@ 48k (worst)
140         m4a       audio only  DASH audio , audio@128k
160         mp4       144p        DASH video , video only
242         webm      240p        DASH video , video only
133         mp4       240p        DASH video , video only
243         webm      360p        DASH video , video only
134         mp4       360p        DASH video , video only
244         webm      480p        DASH video , video only
135         mp4       480p        DASH video , video only
247         webm      720p        DASH video , video only
136         mp4       720p        DASH video , video only
248         webm      1080p       DASH video , video only
137         mp4       1080p       DASH video , video only
264         mp4       1440p       DASH video , video only
138         mp4       2160p       DASH video , video only
272         unknown_videounknown
17          3gp       176x144
36          3gp       320x240
5           flv       400x240
43          webm      640x360
18          mp4       640x360
22          mp4       1280x720    (best)
~~~

OK，革命已经成功了一大半，就剩下一个事情，合并视频流和音频流了

## → → YTDL！

放狗找到这个 Youtube 视频

> <http://www.youtube.com/watch?v=G7uztVbg7CQ>

然后下载了这个脚本

> <http://quidsup.net/sh/ytdl.sh> 或 <https://gist.github.com/ckorn/7284075>

然后我们就可以开始愉快的下载油土鳖视频了，之前我们还是要稍微准备一下的

```bash
sudo port install ffmpeg
```

网速或机器比较慢的话，这个要稍微忍受一下 (用 homebrew 的异类如果你不小心撞进这里的话，现在就可以离开了)

那段脚本可能太古老了，需要手工改一下
就这一句

```bash
ffmpeg -i "$File1" -i "$File2" -sameq -threads 0 "$Out"
```

要改成

```bash
ffmpeg -i "$File1" -i "$File2" -q:v 0 -q:a 0 -strict -2 -threads 0 "$Out"
```

- `-sameq` 选项已经废弃，要用 `-qscale 0` 代替
- `aac` 编码方式为实验功能，需加入强制选项

不然你就等着报错吧


懒人可以直接戳 [这里](https://gist.github.com/lwr/7b1e57ea5540d8b1af55)
<script src="https://gist.github.com/lwr/7b1e57ea5540d8b1af55.js"></script>

以下是执行结果

```bash
↪ svn:lwr)$ ytdl 'http://www.youtube.com/watch?v=o_24LPjOIHI&noredirect=1'
... 之前那个 youtube-dl -F 输出的内容，下面的选择不是太自动 ...
... 默认对应的是视频 1080p / 音频 256kbps 的，自己选一下就是 ...

Quality for Video (default 137): 138
Quality for Audio (default 141): 140
NORTHERN INDIA 4K (Ultra HD) 50_60fps-o_24LPjOIHI.mp4
NORTHERN INDIA 4K (Ultra HD) 50_60fps-o_24LPjOIHI.m4a
NORTHERN INDIA 4K (Ultra HD) 50_60fps.mp4
[youtube] Setting language
[youtube] o_24LPjOIHI: Downloading webpage
[youtube] o_24LPjOIHI: Downloading video info webpage
[youtube] o_24LPjOIHI: Extracting video information
[download] Destination: NORTHERN INDIA  4K (Ultra HD) 50_60fps-o_24LPjOIHI.mp4
[download] 100% of 476.36MiB in 08:02
[youtube] Setting language
[youtube] o_24LPjOIHI: Downloading webpage
[youtube] o_24LPjOIHI: Downloading video info webpage
[youtube] o_24LPjOIHI: Extracting video information
[download] Destination: NORTHERN INDIA  4K (Ultra HD) 50_60fps-o_24LPjOIHI.m4a
[download] 100% of 4.05MiB in 00:05

Combining Audio and Video files with FFMpeg
ffmpeg version 2.2 Copyright (c) 2000-2014 the FFmpeg developers
  built on Mar 25 2014 08:36:03 with Apple LLVM version 5.0 (clang-500.2.79) ...
  configuration: --prefix=/opt/local --enable-swscale ...
  libavutil      52. 66.100 / 52. 66.100
  libavcodec     55. 52.102 / 55. 52.102
  libavformat    55. 33.100 / 55. 33.100
  libavdevice    55. 10.100 / 55. 10.100
  libavfilter     4.  2.100 /  4.  2.100
  libavresample   1.  2.  0 /  1.  2.  0
  libswscale      2.  5.102 /  2.  5.102
  libswresample   0. 18.100 /  0. 18.100
  libpostproc    52.  3.100 / 52.  3.100

Input #0, mov,mp4,m4a,3gp,3g2,mj2, from 'video.mp4':
  Metadata:
    major_brand     : dash
    minor_version   : 0
    compatible_brands: iso6avc1mp41
    creation_time   : 2014-05-13 18:21:53
  Duration: 00:04:24.20, start: 0.000000, bitrate: 15125 kb/s
    Stream #0:0(und): Video: h264 (High) (avc1 / 0x31637661), yuv420p,
                      3840x2160 [SAR 1:1 DAR 16:9], 15121 kb/s,
                      29.97 fps, 29.97 tbr, 90k tbn, 59.94 tbc (default)
    Metadata:
      creation_time   : 2014-05-13 18:21:53
      handler_name    : VideoHandler
Input #1, mov,mp4,m4a,3gp,3g2,mj2, from 'audio.m4a':
  Metadata:
    major_brand     : dash
    minor_version   : 0
    compatible_brands: iso6mp41
    creation_time   : 2014-05-13 18:20:52
  Duration: 00:04:24.25, start: 0.000000, bitrate: 128 kb/s
    Stream #1:0(und): Audio: aac (mp4a / 0x6134706D), 44100 Hz, stereo,
                      fltp, 125 kb/s (default)
    Metadata:
      creation_time   : 2014-05-13 18:20:52
      handler_name    : SoundHandler

[libx264 @ 0x7f9f4a90b600] using SAR=1/1
[libx264 @ 0x7f9f4a90b600] using cpu capabilities: MMX2 SSE2Fast SSSE3 SSE4.2 AVX
[libx264 @ 0x7f9f4a90b600] profile High, level 5.1
[libx264 @ 0x7f9f4a90b600] 264 - core 142 - H.264/MPEG-4 AVC codec - Copyleft ...
Output #0, mp4, to 'NORTHERN INDIA  4K (Ultra HD) 50_60fps.mp4':
  Metadata:
    major_brand     : dash
    minor_version   : 0
    compatible_brands: iso6avc1mp41
    encoder         : Lavf55.33.100
    Stream #0:0(und): Video: h264 (libx264) ([33][0][0][0] / 0x0021), yuv420p,
                      3840x2160 [SAR 1:1 DAR 16:9], q=-1--1, 30k tbn,
                      29.97 tbc (default)
    Metadata:
      creation_time   : 2014-05-13 18:21:53
      handler_name    : VideoHandler
    Stream #0:1(und): Audio: aac ([64][0][0][0] / 0x0040), 44100 Hz, stereo,
                      fltp, 128 kb/s (default)
    Metadata:
      creation_time   : 2014-05-13 18:20:52
      handler_name    : SoundHandler
Stream mapping:
  Stream #0:0 -> #0:0 (h264 -> libx264)
  Stream #1:0 -> #0:1 (aac -> aac)
Press [q] to stop, [?] for help
frame= 7918 fps=6.3 q=-1.0 Lsize=  405200kB time=00:04:24.24 bitrate=12561.6kbits/s
video:400780kB audio:4137kB subtitle:0 data:0 global headers:0kB muxing overhead 0.069752%
[libx264 @ 0x7f9f4a90b600] frame I:79    Avg QP:17.78  size:316265
[libx264 @ 0x7f9f4a90b600] frame P:3562  Avg QP:21.27  size: 80731
[libx264 @ 0x7f9f4a90b600] frame B:4277  Avg QP:23.07  size: 22878
[libx264 @ 0x7f9f4a90b600] consecutive B-frames: 20.7% 18.1% 11.4% 49.9%
[libx264 @ 0x7f9f4a90b600] mb I  I16..4: 20.7% 68.9% 10.4%
[libx264 @ 0x7f9f4a90b600] mb P  I16..4:  6.6% 19.2%  0.3%
                           P16..4: 32.5%  5.9%  2.1%  0.0%  0.0%
                           skip:33.5%
[libx264 @ 0x7f9f4a90b600] mb B  I16..4:  0.5%  1.1%  0.0%
                           B16..8: 30.3%  1.5%  0.1%  direct: 2.7%
                           skip:63.7%  L0:47.6% L1:50.7% BI: 1.7%
[libx264 @ 0x7f9f4a90b600] 8x8 transform intra:72.5% inter:87.1%
[libx264 @ 0x7f9f4a90b600] coded y,uvDC,uvAC intra: 27.2% 46.4% 7.5% inter: 5.7% 13.6% 0.4%
[libx264 @ 0x7f9f4a90b600] i16 v,h,dc,p: 32% 28% 14% 25%
[libx264 @ 0x7f9f4a90b600] i8 v,h,dc,ddl,ddr,vr,hd,vl,hu: 32% 21% 33%  2%  2%  2%  2%  2%  2%
[libx264 @ 0x7f9f4a90b600] i4 v,h,dc,ddl,ddr,vr,hd,vl,hu: 24% 22% 11%  6%  8%  8%  9%  6%  6%
[libx264 @ 0x7f9f4a90b600] i8c dc,h,v,p: 53% 22% 22%  3%
[libx264 @ 0x7f9f4a90b600] Weighted P-Frames: Y:5.2% UV:2.4%
[libx264 @ 0x7f9f4a90b600] ref P L0: 73.3%  8.4% 14.8%  3.4%  0.1%
[libx264 @ 0x7f9f4a90b600] ref B L0: 91.2%  8.0%  0.8%
[libx264 @ 0x7f9f4a90b600] ref B L1: 96.9%  3.1%
[libx264 @ 0x7f9f4a90b600] kb/s:12427.03

File NORTHERN INDIA 4K (Ultra HD) 50_60fps.mp4 created
```

现在终于可以愉快的打开这个四百多MB的视频来看了，是不是很折腾？
