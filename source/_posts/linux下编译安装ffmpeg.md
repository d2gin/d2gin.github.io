---
title: linux下编译安装ffmpeg
date: 2020-03-18 09:33:47
index_img: /cover/5546461313.jpg
banner_img: /cover/5546461313.jpg
tags:
	- linux
	- ffmpeg
---

### 前言

现在最新的FFMPEG 他自带有音频和视频解码的库，可能是因为版权的一些问题，它没有自带编码音频和视频的库，另外，如果需要使用ffplay 进行视频播放，还需要安装SDL。下面介绍在linux 下，以H264 和 MP3 视音频编码 SDL显示为例的安装。现在我需要借助ffmpeg实现视频转gif，并加入字幕。

#### 1、下载最新的安装代码。

    LAME 音频编码：http://lame.sourceforge.net/download.php
    X264 视频编码 ：http://www.videolan.org/developers/x264.html
    SDL 显示库： http://www.libsdl.org/download-2.0.php
    ASS 字幕：http://www.linuxfromscratch.org/blfs/view/svn/multimedia/libass.html
    FFMPEG ： http://www.ffmpeg.org/download.html
前面Filter的主要是看个人需求，因为我需要使用ffmpeg生成带字幕的gif图片，所以需要ASS Filter，但编译Ass需要fribidi，所以需要编译Ass前需要安装一个fribidi：http://www.linuxfromscratch.org/blfs/view/svn/general/fribidi.html

#### 2、 安装MP3编码库

```shell
./configure
make
make install
```

#### 3、安装X264视频编码库

```shell
./configure --enable-shared
make
make install
```

如果安装过程中提示yasm版本过低，可以使用 ./configure --enable-shared --disable-asm 关闭yasm，然后再安装。也可以自己先升级yasm后再安装。

#### 4、SDL显示安装

```shell
./configure
make
make install
```

#### 5、安装fribidi依赖

```shell
./configure --prefix=/usr/local/ --enable-shared
make
make install
```
安装fribidi完后需要把fribidi的`fribidi/lib/pkgconfig`目录添加到环境变量PCK_CONFIG_PATH中去

#### 6、安装libass

```shell
./configure --prefix=/usr --disable-static
make
make install
```

#### 7、安装FFMPEG

```shell
./configure --enable-libmp3lame --enable-libx264 --enable-gpl --enable-libass
make
make install
```

如果执行ffmpeg出现类似`ffmpeg: error while loading shared libraries: libass.so.9: cannot open shared object file: No such file or directory`的提示，只需要在`/etc/ld.so.conf`文件后添加一行`/usr/local/lib`即可

#### 8、测试FFMPEG

```shell
ffmpeg -h
```

{% asset_img 20200318104132.png 执行结果 %}