---
title: 用PHP实现的四则运算表达式计算
date: 2011-07-30 14:03:01
tag: 
keywords: php, php四则运算
description: 本文介绍有一个四则运算的字符串表达式，编写一个函数，计算四则运算的结果的PHP实现方式。
---

题目要求：有一个四则运算的字符串表达式，编写一个函数，计算四则运算的结果

PHP实现：

```php
 <?php
 
 /**
  * 计算四则运算表达式
  */
 
 error_reporting(E_ALL);
 
 $exp = '(1+2*(3+5)/4)*(3+(5-4)*2)';
 $arr_exp = array();
 
 for($i=0;$i<strlen($exp);$i++){
     $arr_exp[] = $exp[$i];
 }
 $result = calcexp( array_reverse($arr_exp) );
 echo $exp . '=' . $result;
 
 function calcexp( $exp ){
     $arr_n = array();
     $arr_op = array();
     
     while( ($s = array_pop( $exp )) != '' ){
         if( $s == '(' ){
             $temp = array(); $quote = 1; $endquote = 0;
             while( ($t = array_pop($exp)) != '' ){
                 if( $t == '(' ){
                     $quote++;
                 }
                 if( $t == ')' ){
                     $endquote++;
                     if( $quote == $endquote ){
                         break;
                     }
                 }
                 array_push($temp, $t);
             }
             $temp = array_reverse($temp);
             array_push($arr_n, calcexp($temp) );
         }else if( $s == '*' || $s == '/' ){
             $n2 = array_pop($exp);
             if( $n2 == '(' ){
                 $temp = array(); $quote = 1; $endquote = 0;
                 while( ($t = array_pop($exp)) != '' ){
                     if( $t == '(' ){
                         $quote++;
                     }
                     if( $t == ')' ){
                         $endquote++;
                         if( $quote == $endquote )
                             break;
                     }
                     array_push($temp, $t);
                 }
                 $temp = array_reverse($temp);
                 $n2 = calcexp($temp);
             }
             
             $op = $s;
             $n1 = array_pop($arr_n);
             
             $result = operation($n1, $op, $n2);
             array_push($arr_n, $result);
         }elseif( $s == '+' || $s == '-' ){
             array_push($arr_op, $s);
         }else{
             array_push($arr_n, $s);
         }
     }
     
     $n2 = array_pop($arr_n);
     while( ($op = array_pop($arr_op)) != '' ){
         $n1 = array_pop($arr_n);
         $n2 = operation($n1, $op, $n2);
     }
     
     return $n2;
 }
 
 function operation( $n1, $op, $n2 ){
     switch ($op) {
         case '+':
             return intval($n1) + intval($n2);
             break;
         case '-':
             return intval($n1) - intval($n2);
             break;
         case '*':
             return intval($n1) * intval($n2);
             break;
         case '/':
             return intval($n1) / intval($n2);
             break;
     }
}
```



这个实现方式中使用了两个堆栈，一个用来存储数字，一个用来存储运算符，遇到括号以后就递归进入括号内运算，实现方式有点笨拙，后面补充一下“逆波兰表达式”的算法实现。












