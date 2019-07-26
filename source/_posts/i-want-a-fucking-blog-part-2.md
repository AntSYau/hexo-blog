---
title: 我也想要一个博客！！（二）
tags:
  - 原创
  - 服务器
url: 98.html
id: 98
categories:
  - 法克
date: 2019-04-24 12:00:54
---

话说上回我们已经[在购买的VPS服务器上搭好了LNMP环境](https://blog.qhurc.com/i-want-a-fucking-blog-part-1/)。今天，让我们完成整个博客系统的搭建吧！首先，我们需要

### 安装 WordPress

要安装 WordPress，我们需要为 WordPress 创建一个 Nginx 站点（vHost）。Nginx 是一个轻量级的 Web 服务器。如果您按照我的前序教程（在此重申——**由于本文面向第一次配置环境的小白，所以下面所有复杂过程均应用快速脚本**）一步一步做到现在的话，您可以使用快速脚本自带的管理器配置 vHost：

    # lnmp vhost add

在配置 vHost 之前，您需要复习[顶级域名、一级域名和二级域名的区别](https://zh.wikipedia.org/wiki/%E5%9F%9F%E5%90%8D#%E5%9F%9F%E5%90%8D%E5%B1%82%E6%AC%A1)。之后，您可以跟着 LNMP.org 提供的[教程](https://lnmp.org/faq/lnmp-vhost-add-howto.html)完成 Nginx 站点的配置。注意，在这一步，你需要创建一个 ftp 用户、一个数据库（建议取名为"wordpress"）和一个数据库用户，这些东西接下来都会用到。

配置完成后，您需要将工作目录切换至 vHost 站点的根目录（将blog.qhurc.com替换成你自己的域名，下同）：

    # cd /home/wwwroot/blog.qhurc.com/

然后，就可以从 WordPress.org 获取 WordPress 安装包啦。执行下列语句下载最新版 WordPress、安装并删除安装包以节省空间：

    # wget https://cn.wordpress.org/latest-zh_CN.tar.gz
    # tar -xzv latest-zh_CN.tar.gz
    # rm -rf latest-zh_CN.tar.gz

现在，尝试登录您的网站吧。一切正常的话，您应该已经能看到 WordPress 的欢迎界面了。

![](https://blog.qhurc.com/wp-content/uploads/2019/04/wp-install-step.png)

你将会看到这个界面

在数据库名称中，输入你创建的数据库名（"wordpress"）。下方的数据库用户名和数据库密码都是你创建 Nginx vHost 时指定的（用户名与数据库名相同）。最后可以不用修改，然后点击 Submit 即可。

接下来，开启快乐的 WordPress 旅程吧！

    #TODO: SSH