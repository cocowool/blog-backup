---
title: 用PHP实现的四则运算表达式计算
date: 2011-07-30 14:03:01
tag: 
---

题目要求：有一个四则运算的字符串表达式，编写一个函数，计算四则运算的结果

PHP实现：
1<?php
2
3/**
4* 计算四则运算表达式
5*/
6
7error_reporting(E_ALL);
8
9$exp='(1+2*(3+5)/4)*(3+(5-4)*2)';
10$arr_exp=array();
11
12for($i=0;$i<strlen($exp);$i++){
13$arr_exp[]=$exp[$i];
14}
15$result=calcexp(array_reverse($arr_exp) );
16echo$exp.'='.$result;
17
18functioncalcexp($exp){
19$arr_n=array();
20$arr_op=array();
21
22while( ($s=array_pop($exp))!=''){
23if($s=='('){
24$temp=array();$quote=1;$endquote=0;
25while( ($t=array_pop($exp))!=''){
26if($t=='('){
27$quote++;
28}
29if($t==')'){
30$endquote++;
31if($quote==$endquote){
32break;
33}
34}
35array_push($temp,$t);
36}
37$temp=array_reverse($temp);
38array_push($arr_n,calcexp($temp) );
39}elseif($s=='*'||$s=='/'){
40$n2=array_pop($exp);
41if($n2=='('){
42$temp=array();$quote=1;$endquote=0;
43while( ($t=array_pop($exp))!=''){
44if($t=='('){
45$quote++;
46}
47if($t==')'){
48$endquote++;
49if($quote==$endquote)
50break;
51}
52array_push($temp,$t);
53}
54$temp=array_reverse($temp);
55$n2=calcexp($temp);
56}
57
58$op=$s;
59$n1=array_pop($arr_n);
60
61$result=operation($n1,$op,$n2);
62array_push($arr_n,$result);
63}elseif($s=='+'||$s=='-'){
64array_push($arr_op,$s);
65}else{
66array_push($arr_n,$s);
67}
68}
69
70$n2=array_pop($arr_n);
71while( ($op=array_pop($arr_op))!=''){
72$n1=array_pop($arr_n);
73$n2=operation($n1,$op,$n2);
74}
75
76return$n2;
77}
78
79functionoperation($n1,$op,$n2){
80switch($op) {
81case'+':
82returnintval($n1)+intval($n2);
83break;
84case'-':
85returnintval($n1)-intval($n2);
86break;
87case'*':
88returnintval($n1)*intval($n2);
89break;
90case'/':
91returnintval($n1)/intval($n2);
92break;
93}

94}

这个实现方式中使用了两个堆栈，一个用来存储数字，一个用来存储运算符，遇到括号以后就递归进入括号内运算，实现方式有点笨拙，后面补充一下“逆波兰表达式”的算法实现。












