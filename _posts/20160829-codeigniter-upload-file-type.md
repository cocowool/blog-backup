---
title: Codeigniter文件上传类型不匹配错误
date: 2016-08-29 16:01:01
tag: 
keywords: codeigniter, codeigniter文件上传
description: Codeigniter文件上传类型不匹配错误解决记录。
---

Codeigniter的文件上传类方便了我们使用PHP来处理文件上传的操作，使用起来非常简单，如下：

```php
$config['upload_path'] = './uploads/';
$config['allowed_types'] = 'gif|jpg|png';
$config['max_size'] = '100';
$config['max_width']  = '1024';
$config['max_height']  = '768';

$this->load->library('upload', $config);

if ( ! $this->upload->do_upload())
{
  $error = array('error' => $this->upload->display_errors());
  $this->load->view('upload_form', $error);
}
else
{
  $data = array('upload_data' => $this->upload->data());
  $this->load->view('upload_success', $data);
}
```

如果只是处理图片类型的文件，基本上不会遇到这个坑，如果处理到了 excel、zip、rar类型的文件，你可能就会遇到明明在 allowed_types 中允许的文件类型，最后收获了 “The filetype you are attempting to upload is not allowed.”的错误，为什么会这样呢？

Codeigniter的文件上传类型判断在 is_allowed_filetype 这个函数中处理，造成这个错误的主要原因是因为判断逻辑中有一个 mime 类型判断的步骤。

什么是 Mime 呢？

MIME是Multipurpose Internet Mail Extention的缩写，是描述消息内容类型的互联网标准。

为什么需要判断 Mime？因为如果只从文件后缀来判断文件类型，是非常危险的。不怀好意的用户可能会把一个可执行文件后缀改成图片类型，上传成功后，如果能够获得文件的地址，并且文件在可执行目录，就能够执行动态脚本，还是很危险的。著名的DedeCMS就很多这种漏洞。

针对不同的后缀，Codeigniter会从 config/mimes.php 文件匹配POST过来的数据中的 file_type 属性，只有一样才会校验通过，否则就会发生文件类型不匹配的错误。

找到问题的原因，解决起来就很方便了。我们只需要在 config/mimes.php 文件中，添加对应的后缀以及file_type 这样就能解决这个问题。下面是我为几种常见文件增加的配置：

```php
'docx'     =>     array('application/vnd.openxmlformats-officedocument.wordprocessingml.document', 'application/zip'),
'xlsx'     =>     array('application/vnd.openxmlformats-officedocument.spreadsheetml.sheet', 'application/zip'),
'xlsm'     =>     array('application/vnd.openxmlformats-officedocument.spreadsheetml.sheet','application/vnd.ms-excel.sheet.macroenabled.12', 'application/zip'),
'word'     =>     array('application/msword', 'application/octet-stream'),
'rar'     =>     array('application/octet-stream'),
'zip'     =>  array('application/x-zip', 'application/zip', 'application/x-zip-compressed', 'application/octet-stream'),
```

> 我这里使用的Codeigniter是2.x版本的，至于现在3.x版本中是否还存在这个问题并没有测试，有遇到的朋友可以分享一下。

参考资料：
1、[W3c Mime](http://www.w3school.com.cn/media/media_mimeref.asp)
2、[百科 Mime](http://baike.baidu.com/link?url=eBw3pgimYhpR-A69nQAvo4NFNIIv7Doa_oQ6NFxuK8vULgSh6qQBdbiCt1-iSuX4BMnSWf23dGKHBBhpelbzQ_)