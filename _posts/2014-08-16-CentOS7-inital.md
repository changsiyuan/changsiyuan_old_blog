
***
##日常使用
***
###1.中文输入法
找到Settings程序
**Settings**->**region and language**
###2.第三方源
#####epel

```
rpm -Uvh http://dl.fedoraproject.org/pub/epel/beta/7/x86_64/epel-release-7-0.2.noarch.rpm
```

#####nux-desktop

```
rpm -Uvh http://li.nux.ro/download/nux/dextop/el7/x86_64/nux-dextop-release-0-1.el7.nux.noarch.rpm
```
###3.ntfs
```
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

#####gedit中文支持

gsettings set org.gnome.gedit.preferences.encodings auto-detected "['UTF-8','CURRENT','GB18030','ISO-8859-15','UTF-16']"

###6.音乐播放器audacious

```
yum install audacious audacious-plugins-freeworld
```

###7.vlc

#####安装vlc

```
yum install vlc
yum install ffmpeg
yum install gstreamer gstreamer-ffmpeg gstreamer-plugins-base gstreamer-plugins-good gstreamer-plugins-bad gstreamer-plugins-bad-free gstreamer-plugins-bad-nonfree  gstreamer-plugins-ugly 
```

#####如果VLC字幕乱码
*  首先启动VLC，按Ctrl+P,左下角的显示设置 选 全部
*  依次点开：视频－字幕／OSD－文本渲染器 右侧的字体栏中，选择一个中文字体。（micro)
*  接着点开：输入／编码－其它编码器－字幕 右侧的 字幕文本编码 选 GB18030
*  然后 把 自动检测 UTF－8  和字幕 格式化字幕 前面的勾去掉

####8.画图软件gimp

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
yum update
yum install kernel-devel
yum groupinstall 'Development Tool'
```
###2.virtualbox(XP)（可能需要内核开发包）
###3.vim 插件
neocomplcache和tagslist
###4.eclipse
#####eclipse的快捷方式，图片太大,可以右键resize
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
看github教程
生成密钥，同步
###6.安装Racket
编译查看src/READEME
安装好，快捷方式share/appliction，修改图标和程序路径(变成绝对路径)
