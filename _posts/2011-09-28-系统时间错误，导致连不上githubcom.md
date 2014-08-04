title: 系统时间错误，导致连不上github.com
tags:
  - 未分类
date: 2011-09-28 07:59:38
---

今天发现自己怎么都打不开[https://github.com](https://github.com)，翻墙也不行，错误信息如下：

![](http://freewind.me/wp-content/uploads/2011/09/zrclip_001p75b0d1ef.png)

怎么弄都没弄好，最后仔细地看错误提示：ERR_CERT_DATE_INVALID，再看系统时间是2007年。

原来上午为了测试新的显卡，把主板拆了，导致系统时间变成了2007年。

难道是这个原因？马上把它改成正确的时间，再访问一切正常。没想到时间不对也有可能访问不了网站。