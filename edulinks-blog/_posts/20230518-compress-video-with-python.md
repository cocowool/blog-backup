---
title: 使用Python压缩mov格式视频
date: 2023-05-18 20:03:17
keywords: 视频压缩, python 压缩, python 压缩视频
description: 本文尝试了使用 Python 编写代码来压缩视频，虽然比不上一些收费软件的效果，但是免费不香吗。
---

今天参加了一天的CTF比赛，每次CTF比赛都会要求选手录屏，下午一不小心没暂停，比赛结束后一看好家伙，视频文件竟然有 29G 这么大。这么大的文件是肯定没办法发给裁判的。

![image-20230518200445775](20230518-compress-video-with-python/image-20230518200445775.png)

本着用 Python 来提高生产效率的原则，决定这次试一试 Python 的能力，于是百度加手撸，搞出来下面一段代码。

```python
import sys
import os
import zlib
import threading
import platform
from PIL import Image

# 压缩视频的 Python 文件
# 主要依赖 ffmpeg 需要本机安装

class Compress_Video(object):
  def __init__(self,filePath,inputName,outName=""):
    #文件地址
    self.filePath = filePath
    #输入的文件名字
    self.inputName = inputName
    #输出的文件名字
    self.outName = outName
    self.system_ = platform.platform().split("-",1)[0]
    if self.system_ == "Windows":
      self.filePath = (self.filePath + "\\") if self.filePath.rsplit("\\",1)[-1] else self.filePath
    elif self.system_ == "Linux":
      self.filePath = (self.filePath + "/") if self.filePath.rsplit("/",1)[-1] else self.filePath
    self.fileInputPath = self.filePath + inputName
    self.fileOutPath = self.filePath + outName
 
  @property
  def is_video(self):
    videoSuffixSet = {"WMV","ASF","ASX","RM","RMVB","MP4","3GP","MOV","M4V","AVI","DAT","MKV","FIV","VOB"}
    suffix = self.fileInputPath.rsplit(".",1)[-1].upper()
    if suffix in videoSuffixSet:
      return True
    else:
      return False
 
  def Save_Video(self):
    fpsize = os.path.getsize(self.fileInputPath) / 1024
    if fpsize >= 150.0: #大于150KB的视频需要压缩
      if self.outName:
        compress = "ffmpeg -i '{}' -r 10 -pix_fmt yuv420p -vcodec libx264 -preset veryslow -profile:v baseline -crf 23 -acodec aac -b:a 32k -strict -5 '{}'".format(self.fileInputPath,self.fileOutPath)
        isRun = os.system(compress)
      else:
        compress = "ffmpeg -i '{}' -r 10 -pix_fmt yuv420p -vcodec libx264 -preset veryslow -profile:v baseline -crf 23 -acodec aac -b:a 32k -strict -5 '{}'".format(self.fileInputPath, self.fileInputPath)
        isRun = os.system(compress)
      if isRun != 0:
        return (isRun,"没有安装ffmpeg")
      return True
    else:
      return True
 
  def Compress_Video(self):
    thr = threading.Thread(target=self.Save_Video)
    thr.start()
 
if __name__ == "__main__":
  b = sys.argv[1:]
  savevideo = Compress_Video(b[0],b[1],b[2])
  print(savevideo.Compress_Video())
```

想起来之前还写过一篇压缩图片的文章，[使用 Python 压缩 Hexo 博客中的图片](http://edulinks.cn/2022/06/01/20220601-compress-hexo-images-with-python/) 这样以后再处理图片或视频就都不怕了。

在 macOS 上实际测试了下，一个 2.xG 左右的 mov 压缩后可以到 500MB 左右。

macOS 的 QuickTimePlayer 也有压缩功能（mov格式的压缩，从1080P->480P），对比下 ffmpeg 看，还是 QuickTimePlayer的效率高，而且从 ActivityMonitor 中观察，QuickTimePlayer 能够使用GPU，ffmpeg 我按照网上的方法添加参数后，GPU使用这块一直还是 0 。

## 参考资料

1. [MacOS上使用ffmpeg利用显卡加速批量压缩视频](https://blog.csdn.net/lbp0408/article/details/113889055)
2. [python强大功能——视频压缩](https://blog.csdn.net/Xcodd/article/details/116033765)
