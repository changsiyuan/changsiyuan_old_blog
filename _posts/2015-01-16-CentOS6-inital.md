---
layout : post
title : CentOS 6 日常使用配置
category : other
tagline : "Supporting tagline"
tags : [CentOS]
---
{% include JB/setup %}


***
##日常使用
***
###1.中文输入法

```
yum install ibus-pinyin
```

###2.第三方源
#####epel

http://fedoraproject.org/wiki/EPEL/FAQ#howtouse

```
rpm -Uvh http://download.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm
```

###3.ntfs
```
yum install ntfs-3g
```
###4.chrome

http://chrome.richardlloyd.org.uk/

###5.中文支持

#####shell中文支持

**terminal**->**set character encoding**

#####vim中文支持

修改.vimrc

````
set encoding=utf-8
set termencoding=utf-8
set formatoptions+=mM
set fencs=utf-8,gbk
````

#####gedit2中文支持

```
gconftool-2 --set --type=list --list-type=string /apps/gedit-2/preferences/encodings/auto_detected "[UTF-8,CURRENT,GB18030,ISO-8859-15,UTF-16]"
```

###6.影音播放

```
yum install gstreamer-plugins-bad gstreamer-ffmpeg gstreamer-plugins-ugly -y
```

####7.BT软件

```
yum install transmission
```


***
##开发环境
***
###1.开发的依赖
```
yum update
yum install kernel-devel
yum groupinstall 'Development Tool'
```
###2.virtualbox(XP)（可能需要内核开发包）
###3.vim 插件
neocomplcache和tagslist
###4.eclipse
#####编辑器的插件(高亮插件)
http://blog.csdn.net/ichsonx/article/details/9148497

###5.git 配置

看github教程

生成密钥，同步
