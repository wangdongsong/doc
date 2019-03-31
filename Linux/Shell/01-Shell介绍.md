# Shell 介绍

### 分类  

* Bourne shell - sh  
* Korn shell - ksh  
* Bourne Again Shell - bash  

### 常用操作系统默认shell  

Linux下默认的Shell是Bourne Again shell（bash），Solaris和FreeBSD默认的是Bourne shell（sh）  

两种方法查看系统默认的Shell  

* echo $SHELL
* grep root /etc/passwd

## Shell脚本的建立与执行

### 建立

1、脚本开头使用#!/bin/bash或#!/bin/sh开头，其中#!字符称为幻数，在执行时，内核会根据#!后的解释器来确定该用哪个程序解释这个脚本中的内容。  

**说明：** 如果不在第一行，则为脚本注释行。

2、查看系统bash脚本：cat /etc/redhat-release。  
3、脚本注释：#后面的内容表示为注释。  

### 执行
 
 Shell脚本执行时，查找系统环境变量env，该变量指定环境文件（加载顺序通常是/etc/profile、~/.bash_profile、~/.bashrc、/etc/bashrc等），加载结束之后，Shell
 开始执行Shell脚本内容。从上至下，从左至右。
 
 若Shell脚本中遇到子脚本（脚本嵌套）时，先执行子脚本内容，完成后再返回父脚本继续执行父脚本内后续的命令及语句。
 
 脚本执行的方式：
 
 * bash script-name或sh script-name：当脚本文件本身没有可执行权限（即文件权限属性x位为-号）时使用该方法。
 * path/script-name或./script-name：指在当前路径下执行脚本，脚本需要有可执行权限
 * source script-name或. script-name：该方法读入或加载指定的Shell脚本文件，然后依次执行指定的Shell脚本文件中所有语句
 * sh<script-name或cat scripts-name | sh：同样适用于bash
