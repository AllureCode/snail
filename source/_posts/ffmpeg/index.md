---
title: FFMPEG 音视图处理 
cover: https://images.xianhu.wang/scenary.jpg
tags: 
 - FFMPEG
categories:
 - FFMPEG
---
# FFMPEG 笔记

## 基础知识

容器/文件：

媒体流：

数据帧/数据包

编解码器：

复用：

解复用：

## FFMPEG参数

### 主要参数

| 参数选项 | 参数说明               |
| -------- | ---------------------- |
| -i       | 输入流                 |
| -f       | 设定输出格式（format） |
| -ss      | 开始时间               |
| -t       | 时间长度               |

### 视频参数

| 参数选项       | 参数说明                                                 |
| -------------- | -------------------------------------------------------- |
| -aframes       | 输出视频帧数                                             |
| -b             | 设定视频码率                                             |
| -b:v           | 视频码率                                                 |
| -r             | 设定帧速率                                               |
| -s             | 设定画面宽和高                                           |
| -vn            | 不处理视频                                               |
| -aspect aspect | 设定横纵比4:3 或16:9或 1.3333:1.7777                     |
| -vcodec        | 设定视频编解码器`如果用copy表示原始编解码数据必须被拷贝` |
| -vf            | 视频过滤器                                               |

### 音频参数

| 参数选项 | 参数说明                                                 |
| -------- | -------------------------------------------------------- |
| -aframes | 输出音频帧数                                             |
| -b       | 设定音频码率                                             |
| -ar      | 设定采样率                                               |
| -ac      | 设定声音的channel数                                      |
| -acodec  | 设定声音编解码器`如果用copy表示原始编解码数据必须被拷贝` |
| -an      | 不处理音频                                               |
| -af      | 音频过滤器                                               |

## FFMPEG命令抽取音视频数据

### 保留封装格式

```
ffmpeg -i test.mp4 -acodec copy audio.mp4
ffmpeg -i test.mp4 -vcodec copy video.mp4
```

### 提取视频

```
保留编码格式：ffmpeg -i test.mp4 -vcodec copy -an test_copy.h264
强制格式：ffmpeg -i test.mp4 -vcodec libx264 -an test.h264
```

### 提取音频

```
保留编码格式：ffmpeg -i test.mp4 -acodec copy -an test_copy.aac
强制格式：ffmpeg -i test.mp4 -acodec libmp3lame -an test.mp3
```

## FFMPEG命令提取像素格式

### 提取YUV

```
提取3秒数据，分辨率和源数据一致
ffmpeg -i test.mp4 -t 3 -pix_fmt yuv420p yuv420p_orig.yuv
提取3秒数据，分辨率转为320*240
ffmpeg -i test.mp4 -t 3 -pix_fmt yuv420p -s 320x240 yuv420p_320x240.yuv
```

### 提取RGB

```
提取3秒数据，分辨率转为320*240
ffmpeg -i test.mp4 -t 3 -pix_fmt rgb24 -s 320x240 rgb24_320x240.rgb
```

### RGB与YUV之间转换

```
ffmpeg -s 320x240 -pix_fmt yuv420p -i yuv420p_320x240.yuv -pix_fmt rgb24 rgb24_320x240_2.rgb
```

### 提取PCM

```
ffmpeg -i test.mp3 -ar 48000 -ac 2 -f s16le 48000_2_s16le.pcm
ffmpeg -i test.mp3 -ar 48000 -ac 2 -sample fmt s16 out s16.wav
ffmpeg -i test.mp3 -ar 48000 -ac 2 -codec:a pcm s16le out2 s16le.wav
ffmpeg -i test.mp3 -ar 48000 -ac 2 -f f32le 48000_2_f32le.pcm
```



### 播放YUV数据

```
ffplay -pixel_format yuv420p -video_size 320x240 -framerate 5 yuv420p_320x240.yuv
```

### 播放RGB数据

```
ffplay -pixel_format rgb24 -video_size 320x240 -i rgb24_320x240.rgb
ffplay -pixel_format rgb24 -video_size 320x240 -framerate 5 -i yuv420p_320x240.yuv
```

### 播放PCM数据

```
ffplay -ar 48000 -ac 2 -f f32le 48000_2_f32le.pcm
```

## FFMPEG命令封装

### 保持编码格式

```
ffmpeg -i test.mp4 -vcodec copy -acodec.copy test_copy.ts
ffmpeg -i test.mp4 -codec copy test_copy.ts
```

### 改变编码格式

```
ffmeg -i test.mp4 -vcodec libx265 -acodec libmp3lame out_h265_mp3.mkv
```

### 修改帧率

```
ffmpeg -i test.mp4 -r 15 out.mp4
```

### 修改视频码率

```
ffmpeg -i test.mp4 -b 400k output_b.mkv
```

### 修改视频码率

```
ffmpeg -i test.mp4 -b:v 400k output_b.flv
```

### 修改音频码率

```
ffmpeg -i test.mp4 -b:a 192k output_ba.mp4
```

### 修改音视频码率

```
ffmpeg -i test.mp4 -b:v 400k -b:a 192k output_bva.mp4
```

### 修改视频分辨率

```
ffmpeg -i test.mp4 -s 480x270 output_480x270.mp4
```

### 修改音频采样率

```
ffmpeg -i test.mp4 -ar 44100 output_44100hz.mp4
```

## FFMPEG裁剪视频

### 提取三个视频10秒内容

```
ffmpeg -i test.mp4 -ss 00:05:00 -t 10 -codec copy1.mp4
ffmpeg -i test2.mp4 -ss 00:05:00 -t 10 -codec copy2.mp4
ffmpeg -i test3.mp4 -ss 00:05:00 -t 10 -codec copy3.mp4
如果音视频格式不统一则强制统一为 -vcodec libx264 -acodec aac

codec copy = vcodec copy + acodec copy
```

### 将视频转为ts格式

```
ffmpeg -i copy.mp4 -codec copy -vbsf h264_m1.ts
ffmpeg -i copy2.mp4 -codec copy -vbsf h264_m2.ts
ffmpeg -i copy3.mp4 -codec copy -vbsf h264_m3.ts
```

### 将视频转为flv格式

```
ffmpeg -i copy.mp4 -codec copy 1.flv
ffmpeg -i copy2.mp4 -codec copy 2.flv
ffmpeg -i copy3.mp4 -codec copy 3.flv
```

## FFMPEG拼接视频

### mp4格式拼接

```
方法一： ffmpeg -i "concat:1.mp4|2.mp4|3.mp4" -codec copy out_1.mp4
方法二： ffmpeg -f concat -i mp4listh.txt -codec copy out_1.mp4


mp4listh.txt格式
file "1.mp4"
file "2.mp4"
file "3.mp4"
```

### ts格式拼接

```
方法一： ffmpeg -i "concat:1.ts|2.ts|3.ts" -codec copy out_ts.mp4
方法二： ffmpeg -f concat -i tslisth.txt -codec copy out_ts.mp4
```

### FLV格式拼接

```
方法一： ffmpeg -i "concat:1.flv|2.flv|3.flv" -codec copy out_flv.mp4
方法二： ffmpeg -f concat -i flvlisth.txt -codec copy out_flv.mp4
```

`注意 方法一只适合部分风中格式 比如ts`

`建议 使用方法二拼接 先转成ts格式在进行拼接`

## FFMPEG命令处理图片与视频转换

### 截取一张图片

```
ffmpeg -i test.mp4 -y -f image2 -ss 00:00:02 -vframes 1 -s 640x360 test.jpg
ffmpeg -i test.mp4 -y -f image2 -ss 00:00:02 -vframes 1 -s 640x360 test.bmp

-i 输入
-y 覆盖
-f 格式
image2 一种格式
-ss 起始位置
-vframes 帧 如果大于1 那么输出加%03d test%03d.jpg
-s 格式大小size
```

### 转换视频为图片(每一帧一张图)

```
ffmpeg -i test.mp4 -t 5 -s 640x360 -r 15 frame%03d.jpg

-r每秒多少张图片

//fps=1/60  每60s抽取一帧 fps=1每一秒抽取一帧
ffmpeg -i test.mp4 -f image2  -vf fps=1/60 -qscale:v 2 /data/mp4-%05d.jpeg

ffmpeg -i test.mp4 -vf fps=1 ./test/out%d.png
```

### 图片转为视频

```
ffmpeg -f image2 -i frame%03d.jpg -r 25 video.mp4
```

## FFMPEG生成GIT图片和视频

### 从视频中生存gif图片

```
ffmpeg -i test.mp4 -t 5 -r 1 image.gif
ffmpeg -i test.mp4 -t 5 -r 25 -s 640x360 image2.gif
```

### 将gif图片转为视频

```
ffmpeg -f gif -i image2.gif image2.mp4
```



## FFMPEG 压缩图片

```bash
ffmpeg -i image_source -q compress_level out_source
#compress_level：压缩等级为正整数，数值越大压缩等级越高
example:
ffmpeg -i test.jpg -q 100 temp.jpg
```



## FFMPEG 压缩视频

```properties
ffmpeg -i test.mp4 threads 10 -preset ultrafast -b:v 800k  output/test.mp4 
ffmpeg -i tse.ts -threads 10 -preset ultrafast  -b:v 800k output/视频22.mp4
```

## FFMPEG 转换音频m4a-mp3

```properties
./ffmpeg -i 纯音乐.m4a -y -acodec libmp3lame -aq 0 ./t.mp3
```

### FFMPEG 拉直播流截帧

```properties
./ffmpeg -i "http://va.huya.com/huyarecord/78941969-2543818084-10925615477753380864-3083206044-10057-A-0-1.flv?cid=zx&cdn=zx" -r 1 -f image2 -s 200x200  /Users/xxx/Desktop/%03d_rtsp.jpg

-r 1/20(20秒截帧一张)
```

## curl 拉流测试

```properties
 curl -L -v "http://va.huya.com/huyarecord/78941969-2543818084-10925615477753380864-3083206044-10057-A-0-1.flv?cid=zx&cdn=zx" -o ./2.flv
```

