---
layout : post
title : CentOS 7 安装后的配置
category : other
tagline : "Supporting tagline"
tags : [intro, beginner]
---
{% include JB/setup %}

***
##日常使用
***
###1.中文输入法
**Settings**->**region and language**
###2.epel
http://dl.fedoraproject.org/pub/epel/beta/7/x86_64/epel-release-7-0.2.noarch.rpm
###3.ntfs
```
yum install ntfs-3g
```
###4.chrome
###5.data盘
###6.修改hosts才能上google
###7.shell中文支持
**terminal**->**set character encoding**
###8.vim中文支持
修改.vimrc
###9.goagent

***
##开发环境
***
###1.
```
yum update
yum install kernel-devel
yum groupinstall 'Development Tool'
```
###2.virtualbox(XP)（可能需要内核开发包）
###3.vim 插件
###4.eclipse
#####eclipse的快捷方式，图片太大
```
[Desktop Entry]
Version=1.0
Name=Eclipse
Exec=/home/young/software/eclipse/eclipse
Terminal=false
Icon=/home/young/software/eclipse/icon.png
Type=Application
Categories=Development;
```

#####hadoop插件
#####编辑器的插件(高亮插件)
http://blog.csdn.net/ichsonx/article/details/9148497

打包jar,上传到服务器,测试
###5.git 配置
看教程
生成密钥，同步
###6.安装Racket
编译查看src/READEME
安装好，快捷方式share/appliction，修改图标和程序路径(变成绝对路径)
