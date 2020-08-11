---
title: Python处理PDF及生成多层PDF
date: 2017-04-24 15:08:01
tag: 
---

Python提供了众多的PDF支持库，本文是在Python3环境下，试用了两个库来完成PDF的生成的功能。PyPDF对于读取PDF支持较好，但是没找到生成多层PDF的方法。Reportlab看起来更成熟，能够利用Canvas很方便的生成多层PDF，这样就能够实现图片扫描上来的内容也可以进行内容搜索的目标。

### Reportlab

#### 生成双层PDF
双层PDF应用PDF中的Canvas概念，先画文字，最后将图片画上去，这样就是两层的PDF。
```python
import os
# import urllib2
import time
from reportlab import platypus
from reportlab.lib.pagesizes import letter
from reportlab.lib.units import inch
from reportlab.platypus import SimpleDocTemplate, Image
from reportlab.pdfgen import canvas

image_file = "./42.png"

# Use Canvas to generate pdf
c = canvas.Canvas('reportlab_canvas.pdf', pagesize=letter)
width, height = letter

c.setFillColorRGB(0,0.77,0.77)
# say hello (note after rotate the y coord needs to be negative!)
c.drawString( 3*inch, 3*inch, "Hello World")
c.drawImage(image_file, 0 , 0)
c.showPage()
c.save()
```

### PyPDF2

#### 读取PDF
```python
from PyPDF2 import PdfFileWriter, PdfFileReader

output = PdfFileWriter()
input1 = PdfFileReader(open("jquery.pdf", "rb"))

# print document info
print(input1.getDocumentInfo())

# print how many pages input1 has:
print ("pdf_document.pdf has %d pages." % input1.getNumPages())

# print page content
page_content = input1.getPage(0).extractText()
print( page_content )

# add page 1 from input1 to output document, unchanged
output.addPage(input1.getPage(0))

# add page 2 from input1, but rotated clockwise 90 degrees
output.addPage(input1.getPage(1).rotateClockwise(90))

# finally, write "output" to document-output.pdf
outputStream = open("PyPDF2-output.pdf", "wb")
output.write(outputStream)
```
但是PyPDF获取PDF内容有很多问题，可以看这个[问题列表](https://github.com/mstamy2/PyPDF2/issues?page=2&q=is%3Aissue+is%3Aopen)。文档中也有说明。
> | extractText(self) | ## | # Locate all text drawing commands, in the order they are provided in the | # content stream, and extract the text. This works well for some PDF | # files, but poorly for others, depending on the generator used. This will | # be refined in the future. Do not rely on the order of text coming out of | # this function, as it will change if this function is made more | # sophisticated. | #
| # Stability: Added in v1.7, will exist for all future v1.x releases. May | # be overhauled to provide more ordered text in the future. | # @return a unicode string object

参考资料：
1、[PDF 1.0](https://github.com/jaraco/PDF)
2、[PyPDF 2](https://pypi.python.org/pypi/PyPDF2)
3、[PyPDF2 Homepage](http://mstamy2.github.io/PyPDF2/)
4、[PyPDF2 Documentation](https://pythonhosted.org/PyPDF2/)
5、[python name 'file' is not defined的解决办法](http://blog.csdn.net/menuconfig/article/details/8672118)
6、[ReportLab](http://www.reportlab.com/opensource/)
7、[用Python/reportlab生成PDF](https://www.dup2.org/node/1202)
8、[Writing Pdf with Python: Add image](http://www.tylerlesmann.com/2009/jan/28/writing-pdfs-python-adding-images/)












