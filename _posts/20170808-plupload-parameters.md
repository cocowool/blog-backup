---
title: Plupload设置自定义参数
date: 2017-08-07 16:23:01
tag: 
---

在HTML 5比较流行的当下，Plupload是文件上传的不二之选，特别是Adobe宣布2020年将停止对Flash的更新支持。本文记录一下如何在上传文件的时候，传递自定义参数。
了解到两种方式，一种是通过 setOption 方法，一种是直接操作对象。
```javascript
uploader.setOption("multipart_params", {
    "post_id"    : 1,
    "post_author" : 2
})

uploader.settings.multipart_params.test_id = 2;
uploader.settings.multipart_params.test_author = "Wang";
```
参数通过POST请求发送到后端，后台可以根据实际情况获取。
较为完整的代码如下：
```javascript
var uploader = new plupload.Uploader({
    browse_button : 'rs-uploader',
    url : '/manage/upload',
    chunk_size : '1mb',
    //multipart : true,
    multi_selection : false,
    filters : {
        max_file_size : '10mb',
            mime_types: [
                {title : "Image files", extensions : "jpg,gif,png"},
                {title : "Zip files", extensions : "zip"}
            ]
    },
    flash_swf_url : '../js/Moxie.swf',
    silverlight_xap_url : '../js/Moxie.xap',
    preinit : {
            Init: function(up, info) {
                //console.log('[Init]', 'Info:', info, 'Features:', up.features);
            },
 
            UploadFile: function(up, file) {
                //console.log('[UploadFile]', file);
            }
        },
    init : {
        BeforeUpload: function(up, file) {
            console.log('[BeforeUpload]', 'File: ', file);

            //设置参数
            uploader.setOption("multipart_params", {
                "post_id"    : 1,
                "post_author" : 2
            });

            uploader.settings.multipart_params.test_id = 2;
            uploader.settings.multipart_params.test_author = "Wang";
        },
        FilesAdded: function(up, files) {
                // Called when files are added to queue
                console.log('[FilesAdded]');
 
                plupload.each(files, function(file) {
                    console.log('  File:', file);
                });

            uploader.start();
         }
    }
});

uploader.init();
});
```
> plupload 提供了四个控制请求的参数，包括 headers、multipart、multipart_params、max_retries，前三个都是用来传递参数的，但是因为前两个对于 html4 及 flash 的支持问题，我觉得还是使用 multipart_params 比较好。本文使用的 plupload 版本为 2.3.1。

参考资料：
1、[前端上传组件Plupload使用指南](http://www.cnblogs.com/2050/p/3913184.html)
2、[plupload 设置自定多参数](http://purperwind.blog.163.com/blog/static/26960703201391151625890/)
3、[使用plupload实现多文件上传，自定义参数](http://blog.csdn.net/cml_blog/article/details/22943301)












