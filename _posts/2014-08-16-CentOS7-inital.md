---
layout : post
title : CentOS 7 日常使用配置
category : other
tagline : "Supporting tagline"
tags : [CentOS]
---
{% include JB/setup %}


***
##安装提示
***

* 选择Gnome Desktop后，把开发环境也勾选上，这样可以方便编译一些软件。
* 安装完成之后可能没有windows的引导
 * 将以下代码添加进/etc/grub.d/40_custom

```
menuentry "Windows"{
    set root=(hd0,1)
    chainloader +1
}
```
* 然后更新引导

```
grub2-mkconfig -o /boot/grub2/grub.cfg
```

***
##日常使用
***
###1.中文输入法
找到Settings程序
**Settings**->**region and language**
###2.第三方源

http://fedoraproject.org/wiki/EPEL/FAQ#howtouse

#####epel

```
rpm -Uvh http://download.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
```

#####nux-desktop

```
rpm -Uvh http://li.nux.ro/download/nux/dextop/el7/x86_64/nux-dextop-release-0-1.el7.nux.noarch.rpm
```
###3.ntfs
```
需要epel
yum install ntfs-3g
```
###4.chrome

官网下载

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

#####gedit3中文支持

```
gsettings set org.gnome.gedit.preferences.encodings auto-detected "['UTF-8','CURRENT','GB18030','ISO-8859-15','UTF-16']"
```

###6.音乐播放器audacious

```
yum install audacious audacious-plugins-freeworld
```

###7.vlc

#####安装vlc

```
yum install vlc
yum remove totem
```

#####如果VLC字幕乱码
*  首先启动VLC，按Ctrl+P,左下角的显示设置 选 全部
*  依次点开：视频－字幕／OSD－文本渲染器 右侧的字体栏中，选择一个中文字体。（micro)
*  接着点开：输入／编码－其它编码器－字幕 右侧的 字幕文本编码 选 GB18030
*  然后 把 自动检测 UTF－8  和字幕 格式化字幕 前面的勾去掉

####8.WPS的安装
* 从WPS的官网下载安装包wps-office-9.1.0.4945-1.a16p3.i686.rpm
* 使用yum install,不要使用rpm -ivh

```
yum install wps-office-9.1.0.4945-1.a16p3.i686.rpm
```
* 安装之后还有公式的字体问题下载symbol－fonts_all.deb
* 解压之后可以找到字体，将这些字体复制到~/.fonts中(如果没有该文件夹，就新建一个)

```
cd ~/.fonts
mkfontscale
mkfontdir
fc-cache
```

####9.画图软件gimp

```
yum install gimp
```
####9.BT软件

```
yum install transmission
```


***
##开发环境
***
###1.开发的依赖
```
yum groupinstall 'Development Tools'
```

###2.vim 插件
neocomplcache和tagslist
###3.Java开发环境配置

```
yum install java-1.7.0-openjdk
yum install java-1.7.0-openjdk-devel
echo 'JAVA_HOME=/usr/lib/jvm/java-1.7.0-openjdk' >> ~/.bashrc
```
###4.Java IDE配置
#####Eclipse

* Eclipse的快捷方式
 * 图片icon.xmp太大,可以右键resize(33%),然后保存为icon.png
* 将下面的内容写入文本，命名为Eclipse.desktop
 * 其中的Exec是启动的eclipse的路径
 * Icon是图标所在的路径

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

* 编辑器的插件(高亮插件)
 * http://blog.csdn.net/ichsonx/article/details/9148497
#####Idea
* Idea.Desktop
 * 其中的Exec是启动的eclipse的路径
 * Icon是图标所在的路径

```
[Desktop Entry]
Version=1.0
Name=Idea
Exec=/home/young/software/idea/idea-IC-135.1230/bin/idea.sh
Terminal=false
Icon=/home/young/software/idea/idea-IC-135.1230/bin/idea.png
Type=Application
Categories=Development;
```
###5.安装Racket
* 编译查看src/READEME
* 安装好，快捷方式share/appliction，修改图标和程序路径(变成绝对路径)
