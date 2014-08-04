title: windows7 x64上安装oracle11g过程
tags:
  - 未分类
date: 2011-12-25 14:03:41
---

为了在windows 7 x64上安装oracle，折腾了两天，下载了几次文件，都提示各种错误，如操作系统不支持等。最后终于成功，特记录下来。

**一、下载最新的oralce11gR2，只有该版本才支持windows7 x64。**

之前的版本包括11G，在安装时都会提示操作系统不支持。虽然可通过设置让它不要验证操作系统，但对于新手来说，还是有点难度。

下载后可得到两个文件：

1.  win64_11gR2_database_1of2.zip
2.  win64_11gR2_database_2of2.zip

一共2G。解压在一起，得到一个database目录。

<span id="more-635"></span>
<p>**二、保证该database的路径中没有中文，最好也不要有空格，否则安装时会出错**

**三、运行setup.exe**

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb10.png "image")](http://freewind.me/wp-content/uploads/2011/12/image10.png)

第1步，让填电子邮件，因为我是安装在本机用于开发，所以没必要写。去掉“我希望通过…接收安全通知”前的勾

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb11.png "image")](http://freewind.me/wp-content/uploads/2011/12/image11.png)

第2步，选择安装哪些，默认选中第一个

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb12.png "image")](http://freewind.me/wp-content/uploads/2011/12/image12.png)

第3步：选择是桌面类还是服务器类，选前者

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb13.png "image")](http://freewind.me/wp-content/uploads/2011/12/image13.png)

第4步：路径及参数设置

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb14.png "image")](http://freewind.me/wp-content/uploads/2011/12/image14.png)

因为我填的密码为system，太简单了，oracle提示我是否改为复杂的密码。直接选否。

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb15.png "image")](http://freewind.me/wp-content/uploads/2011/12/image15.png)

第5步：检查环境是否满足要求

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb16.png "image")](http://freewind.me/wp-content/uploads/2011/12/image16.png)

第6步：显示前面填写的信息的汇总，供确认

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb17.png "image")](http://freewind.me/wp-content/uploads/2011/12/image17.png)

第7步：开始安装，大约要五到十分钟。

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb18.png "image")](http://freewind.me/wp-content/uploads/2011/12/image18.png)

中途防火墙会提示，选择“允许访问”

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb19.png "image")](http://freewind.me/wp-content/uploads/2011/12/image19.png)

创建数据库等最后的操作，需要十几分钟

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb20.png "image")](http://freewind.me/wp-content/uploads/2011/12/image20.png)

经过漫长的等待，终于提示以下的信息，说明安装成功。

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb21.png "image")](http://freewind.me/wp-content/uploads/2011/12/image21.png)

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb22.png "image")](http://freewind.me/wp-content/uploads/2011/12/image22.png)

**四、新建数据库实例**

1. 打开“数据库配置助手”：

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb23.png "image")](http://freewind.me/wp-content/uploads/2011/12/image23.png)

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb24.png "image")](http://freewind.me/wp-content/uploads/2011/12/image24.png)

点击下一步：

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb25.png "image")](http://freewind.me/wp-content/uploads/2011/12/image25.png)

默认选择第一个即可：

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb26.png "image")](http://freewind.me/wp-content/uploads/2011/12/image26.png)

输入数据库名，SID会自动输入，也可修改：

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb27.png "image")](http://freewind.me/wp-content/uploads/2011/12/image27.png)

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb28.png "image")](http://freewind.me/wp-content/uploads/2011/12/image28.png)

偷懒，所有用户使用同一口令：

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb29.png "image")](http://freewind.me/wp-content/uploads/2011/12/image29.png)

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb30.png "image")](http://freewind.me/wp-content/uploads/2011/12/image30.png)

因为仅用来开发，所以去掉了“快速恢复区”：

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb31.png "image")](http://freewind.me/wp-content/uploads/2011/12/image31.png)

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb32.png "image")](http://freewind.me/wp-content/uploads/2011/12/image32.png)

内存默认为3G多，太大了，调小一点：

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb33.png "image")](http://freewind.me/wp-content/uploads/2011/12/image33.png)

字符集默认为GBK，改为UTF8：

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb34.png "image")](http://freewind.me/wp-content/uploads/2011/12/image34.png)

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb35.png "image")](http://freewind.me/wp-content/uploads/2011/12/image35.png)

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb36.png "image")](http://freewind.me/wp-content/uploads/2011/12/image36.png)

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb37.png "image")](http://freewind.me/wp-content/uploads/2011/12/image37.png)

点击“确定”后，又是漫长的等待：

[![image](http://freewind.me/wp-content/uploads/2011/12/image_thumb38.png "image")](http://freewind.me/wp-content/uploads/2011/12/image38.png)