---
title: phpExcel导出文件时内存溢出处理方法
date: 2021-04-06 18:04:55
tags:
keywords: phpExcel, phpExcel导出, phpExcel php, php
description: PHPExcel导出文件容易内存溢出，可使用官方提供的参数进行优化。
---

在使用PHPExcel导出文件时，经常会因为文件过大导致PHP内存溢出报错，为了解决这个问题，可以使用PHPExcel提供的参数进行优化。这里说的Excel文件过大并不一定是文件大小，更关键的在于文件内存放的数据和格式，如果数据很多，格式又比较丰富，那很容易会将PHP的内存耗尽。

资料2中指出，Excel中一个单元格在不启用缓存的情况下大概占用内存是1K，一个8000行、31列的表格（248000个单元格）需要242MB内存。如果启用缓存，则会降到80MB，效果还是非常明显的。

使用中需要注意，PHPExcel的内存优化参数并不在PHPExcel对象中，需要在PHPExcel实例化之前设置。

```php
$cacheMethod = PHPExcel_CachedObjectStorageFactory::cache_to_phpTemp;
$cacheSettings = array( 'memoryCacheSize' => '512MB');
PHPExcel_Settings::setCacheStorageMethod($cacheMethod,$cacheSettings);

$oExcel = new PHPExcel();
```

### PHPExcel_Settings::setCacheStorageMethod() 的几个参数

#### 将单元格数据序列化后保存在内存中

```php
PHPExcel_CachedObjectStorageFactory::cache_in_memory_serialized; 
```

#### 将单元格序列化后再进行Gzip压缩，然后保存在内存中

```php
PHPExcel_CachedObjectStorageFactory::cache_in_memory_gzip; 
```

#### 缓存在临时的磁盘文件中，速度可能会慢一些

```php
PHPExcel_CachedObjectStorageFactory::cache_to_discISAM;
```

#### 保存在php://temp

```php
PHPExcel_CachedObjectStorageFactory::cache_to_phpTemp; 
```

#### 保存在memcache中

```php
$cacheMethod = PHPExcel_CachedObjectStorageFactory::cache_to_memcache;  
$cacheSettings = array( 'memcacheServer'  => 'localhost',  
    'memcachePort'    => 11211,  
    'cacheTime'       => 600  
);  
PHPExcel_Settings::setCacheStorageMethod($cacheMethod, $cacheSettings);
```

### 其它降低内存使用的方法

如果不需要读取Excel单元格格式，可以设置为只读取数据。
```php
$objReader = PHPExcel_IOFactory::createReader('Excel2007');
$objReader->setReadDataOnly(true);
$objPHPExcel = $objReader->load("test.xlsx”);
```

如果Excel中有多个Sheet，但是我们只需要读取其中几个，为了减少内存消耗，也可以设置。
```php
$objReader = PHPExcel_IOFactory::createReader('Excel2007');
$objReader->setLoadSheetsOnly( array("Worksheet1", "Worksheet2") );
$objPHPExcel = $objReader->load("test.xlsx”);
```

如果只需要读取Sheet中一定区域，也可以设置过滤器。
```php
class MyReadFilter implements PHPExcel_Reader_IReadFilter
{
    public function readCell($column, $row, $worksheetName = '') {
        // Read title row and rows 20 - 30
        if ($row == 1 || ($row >= 20 && $row <= 30)) {
            return true;
        }

        return false;
    }
}

$objReader = PHPExcel_IOFactory::createReader('Excel2007');
$objReader->setReadFilter( new MyReadFilter() );
$objPHPExcel = $objReader->load("test.xlsx”);
```

参考资料：
1、[phpExcel how do I pass settings into class](http://stackoverflow.com/questions/14642865/phpexcel-how-do-i-pass-settings-into-class)
2、[How to fix memory getting exhausted with PHPExcel?](https://stackoverflow.com/questions/3537604/how-to-fix-memory-getting-exhausted-with-phpexcel)
3、[Lowest Memory Cache Method for PHPExcel](http://stackoverflow.com/questions/27607250/lowest-memory-cache-method-for-phpexcel)