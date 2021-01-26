---
title: PHP读取Excel文件内容
date: 2014-10-15 10:50:01
tag: 
keywords: php, php excel, php读取Excel
description: 项目需要读取Excel的内容，从百度搜索了下，主要有两个选择，第一个是PHPExcelReader，另外一个是PHPExcel。
---

项目需要读取Excel的内容，从百度搜索了下，主要有两个选择，第一个是PHPExcelReader，另外一个是PHPExcel。

PHPExcelReader比较轻量级，仅支持Excel的读取，实际上就是一个Reader。但是可惜的是不能够支持Excel 2007的格式（.xlsx）。

PHPExcel比较强大，能够将内存中的数据输出成Excel文件，同时还能够对Excel做各种操作，下面主要介绍下如何使用PHPExcel进行Excel 2007格式（.xlsx）文件的读取。

下载PHPExcel后保存到自己的类文件目录中，然后使用以下代码可以打开Excel 2007（xlsx）格式的文件：

```php
require_once '/libs/PHPExcel-1.8.0/Classes/PHPExcel.php';     //修改为自己的目录
echo '<p>TEST PHPExcel 1.8.0: read xlsx file</p>';
$objReader = PHPExcel_IOFactory::createReaderForFile($filename); 
$objPHPExcel = $objReader->load($filename);
$objPHPExcel->setActiveSheetIndex(1);
$date = $objPHPExcel->getActiveSheet()->getCell('A16')->getValue();
```


输出$date变量就能够看到文件中的内容了。PHPExcel使用PHPExcel_IOFactory这个类来自动匹配所上传的文件类型，当然我们也可以自己制定要解析的文件类型。之后通过load方法，将PHP文件加载到objPHPExcel对象中。如果Excel文件有多个Sheet，可以通过setActiveSheetIndex来设置当前活动的Sheet。如何通过Sheet名来获得当前Sheet我还不知道，如果有知道的可以站内我。

需要注意的是，对于Excel中的日期格式，PHPExcel读出来的是不是日期类型，需要我们使用以下方法来进行日期类型转换。

```php
echo date("Y-m-d[H:i:s",PHPExcel_Shared_Date::ExcelToPHP($date));](file:///H:i:s%26quot;,PHPExcel_Shared_Date::ExcelToPHP($date));)
```

下面的代码显示了如何遍历显示Excel的内容：
```php
<table id="table_id">
  <?php
  $objWorksheet=$objPHPExcel->getActiveSheet();
	$i= 0;
	foreach($objWorksheet->getRowIterator()as$row){
  ?>
  <tr>
  <?php
    $cellIterator=$row->getCellIterator();
  	$cellIterator->setIterateOnlyExistingCells(false);
  
  	if($i== 0){
    	echo'<thead>';
  	}
  	foreach($cellIteratoras$cell){
    	echo'<td>' .$cell->getValue() . '</td>';
  	}
  	if($i== 0){
    	echo'</thead>';
  	}

    $i++;
  ?>
  </tr>
  <?php
		}
	?>
</table>
```

参考资料：
1、[PHPExcel官网](http://phpexcel.codeplex.com/)
2、[使用PHPExcel判断和格式化Excel中的日期](http://www.jbxue.com/article/5392.html)
3、[PHPExcel中文帮助（知识点）](http://www.jbxue.com/article/6308.html)
4、[利用PHPExcel导入导出Excel文件](http://www.jb51.net/article/45382.htm)
5、[PHPExcel常用功能指南](http://www.jb51.net/article/53895.htm)