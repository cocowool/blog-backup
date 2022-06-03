---
title: 使用 Python 压缩 Hexo 博客中的图片
date: 2022-06-01 22:27:50
keywords: python, python 图片压缩, python PIL, python Image
description: Hexo 构建的博客文件系统占用有点高，发现图片占了很大一部分，有些图片是不适合在网上播放的，但是图片特别多，想到了使用 Python 批量压缩图片，手写了一份代码，支持批量压缩某个文件夹下的所有图片。
---

> 本文代码在 macOS Monterey 12.2.1 操作系统，Python 3.6 下测试通过

Python 2 中提供了 PIL （Python Imaging Library）进行图像处理，Python3 中提供了 Pillow ，两个包的方法都完全一样，这样基本上没有迁移成本。我整理了一个 `image_tools.py` 脚本，可以将某个文件夹下的图片进行压缩，支持原地替换，或压缩后的图片输出到另外一个文件夹中，具体的代码可以在我的 [Github](https://github.com/cocowool/) 下载。

## 总结

Pillow 的 Image 对于 jpg 格式图片，Image 压缩的效果较好。Quality 设置为 80 的情况下，一个 270K 的图片可以压缩到 60K 左右，图片质量没有损失。

但是对于 PNG 存在一些问题：
* 压缩比例还是不理想，一张 PNG 格式 2.7M 的原图，压缩后 1.6M 。而使用 Pixelmator 可以压缩到 500K 左右。
* 处理 PNG 图片，处理后的图片反而变大了。

对于 PNG 图片，采用了 `libimagequant` 进行压缩处理，优化后 PNG 图片的压缩比例大概能有 75%，效果很好。

## 用法

目前支持的功能：
* 支持对 jpg / png 两种类型的图片进行压缩
* 支持遍历某个文件夹下的所有图片
* 支持将图片压缩后输出到一个单独的文件夹
* 支持对大于某一尺寸的图片，按设定的比例缩放

如果你有什么优化建议，欢迎与我取得联系或通过 github 贡献你的想法。

使用方法如下：
```sh
$ python3 /Users/shiqiang/Projects/sh-valley/python/image_tools.py -m compress -f ~/Desktop/test-original/ -o ~/Desktop/test-compress/
/Users/shiqiang/Desktop/test-original/211035560621176.jpg
{'original_size': 273884, 'img_x': 595, 'img_y': 339, 'compress_size': 36349}
/Users/shiqiang/Desktop/test-original/image-20201014191941447.png
{'original_size': 136657, 'img_x': 1552, 'img_y': 1260, 's_x': 992, 's_y': 1007, 'compress_size': 468176}
Total original size : 410541
Total compress size : 504525
```

## 源代码

```python
import json, datetime, requests, os, time, random, getopt, sys
from PIL import Image
from PIL import ImageFile
import libimagequant as liq
# from pillow import Image

# Compress Images Under folder in place
# 原地压缩替换某一文件夹下的图片文件
def compress_images(image_input_folder = '', image_output_folder = False):

    if not image_input_folder:
        print("No Image Folder Given, Exit!")
        return

    # Check Folder
    if not os.path.exists(image_input_folder):
        print("Input Folder doesn't exists! ")

    # Check Folder
    if not os.path.exists(image_output_folder):
        print("Output Folder doesn't exists! ")

    total_size = 0
    compress_size = 0
    for parent, _, file_names in os.walk(image_input_folder):
        for file_name in file_names:
            output_file_name = image_output_folder + file_name
            file_name = os.path.join(parent, file_name)

            if file_name.endswith(('jpg','bmp')):
                image_info = compress_single_image(file_name, output_file_name)
                if image_info:
                    total_size += image_info['original_size']
                    compress_size += image_info['compress_size']
            elif file_name.endswith(('png')):
                image_info = compress_png_image( file_name, output_file_name)
                if image_info:
                    total_size += image_info['original_size']
                    compress_size += image_info['compress_size']

    print('Total original size : ' + str(total_size))
    print('Total compress size : ' + str(compress_size))


def to_liq( image, attr):
    if image.mode != 'RGBA':
        image = image.convert('RGBA')

    return attr.create_rgba(image.tobytes(), image.width, image.height, image.info.get('gamma', 0))

def from_liq(result, image):
    out_img = Image.frombytes('P', (image.width, image.height), result.remap_image(image))

    palette_data = []
    for color in result.get_palette():
        palette_data.extend(color[:3])
    out_img.putpalette(palette_data)

    return out_img

# Compress Png Image use libimagequant
def compress_png_image( image_path, output_file_name = False, quality = 80):
    # Object Store Detail Inforamtion
    c_obj = {}

    original_size = os.path.getsize( image_path )
    c_obj['original_size'] = original_size

    try:
        im = Image.open(image_path)
        x, y = im.size
        c_obj['img_x'] = x
        c_obj['img_y'] = y

        attr = liq.Attr()
        liq_image = to_liq(im, attr)
        result = liq_image.quantize(attr)
        pil_image = from_liq(result, liq_image)
        pil_image.save(output_file_name, optimize = True, quality = quality)
        c_obj['compress_size'] = os.path.getsize(output_file_name)


        print(image_path)
        print(c_obj)
        # time.sleep(1)

        return c_obj
    except Exception as r:
        print("Compress Png Image Error : " + image_path)
        print("Unknow Error : %s" %(r))
        return False

# Compress Single Image And Replace the original image
# 压缩单张图片并替换保存
# @TODO 小于一定大小的图片不处理；长度、宽度超过一定限度的，处理为适合 Web；默认为只压缩图片不缩放
def compress_single_image( image_path, output_file_name = False, in_replace = True, quality = 80, size_scale = 0.8 ):
    image_x_threshold = 1024
    image_size_threshold = 10240

    # Object Store Detail Inforamtion
    c_obj = {}
    small_x = 0

    original_size = os.path.getsize( image_path )
    c_obj['original_size'] = original_size

    ImageFile.LOAD_TRUNCATED_IMAGES = True

    try:
        im = Image.open(image_path)
        x, y = im.size
        c_obj['img_x'] = x
        c_obj['img_y'] = y

        # If image size is small, direct return
        if original_size < image_size_threshold:
            return False

        # If image is too wide, scale down
        while x > image_x_threshold:
            small_x = int(x * size_scale)
            small_y = int(y * small_x / x)
            c_obj['s_x'] = small_x
            c_obj['s_y'] = small_y

            x = small_x
            y = small_y

        if small_x > 0:
            # im = im.resize( (small_x, small_y), Image.CUBIC)
            im = im.resize( (small_x, small_y), Image.ANTIALIAS)

        if output_file_name:
            # print( "input file : " + image_path)
            # print( "output file : " + output_file_name )
            # time.sleep(10)
            im.save(output_file_name, optimize = True, quality = quality)
            c_obj['compress_size'] = os.path.getsize(output_file_name)
        else:
            im.save(image_path, optimize = True, quality = quality)
            c_obj['compress_size'] = os.path.getsize(image_path)
            
        print(image_path)
        print(c_obj)
        # time.sleep(1)

        return c_obj
    except:
        print("Image Read Error : " + image_path)
        return False


if __name__ == '__main__':
    opts, args = getopt.getopt(sys.argv[1:], "m:h:f:o:", ["mode", "help", "folder", "output"])
    IMAGE_FOLDER = ''
    OUTPUT_FOLDER = False

    for user_option, user_parameter in opts:
        if user_option in ("-h", "--help"):
            print("Usage: python3 image_tools.py -m method -f image_folder_path [-o output_folder]")
        if user_option in ("-m", "--method"):
            PROCESS_METHOD = user_parameter
        if user_option in ("-f", "--folder"):
            IMAGE_FOLDER = user_parameter
        if user_option in ("-o", "--output"):
            OUTPUT_FOLDER = user_parameter

    if PROCESS_METHOD == 'compress':
        compress_images(IMAGE_FOLDER, OUTPUT_FOLDER)
```

## 参考资料

1. [Python压缩图片到指定大小](https://blog.csdn.net/weixin_50668086/article/details/123684321)
2. [使用python对图片进行压缩](https://blog.csdn.net/Rebacca122222/article/details/124918529)
3. [How to compress images with Python and PIL](https://progskillss.com/compress-images-with-python)
4. [pillow documents](https://pillow.readthedocs.io/en/latest/)
