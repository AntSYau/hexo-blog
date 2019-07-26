---
title: 我也想要一个博客！！（一）
tags:
  - Linux
  - 博客
  - 原创
  - 服务器
url: 16.html
id: 16
categories:
  - 法克
date: 2019-04-08 13:00:01
---

之前承诺了要把搭博客的细节写出来的，那么现在开个天坑慢慢写好了，反正离考试貌似还有好长时间（逃

### 购买服务器

服务器可不是天上掉下来的，想自己单干要付出一点代价。我的VPS服务器购自 [HostUS](https://hostus.us/) ，年金是16美元（[这个配置](http://www.hostusvps.com/url/hostus-us-768mb)，节选自[这个网页](http://www.hostusvps.com/)）；当然你也可以从别的VPS提供商购买。购买服务器时会要求你设置服务器密码，这个密码一定不能忘，后面有用。服务器名称可以随便设置，不会影响使用。操作系统建议选择 CentOS 7 或者 Ubuntu 16.04 ，这两个系统的支持文档和软件包会全一些。配置好后，VPS提供商会给你提供一个IPv4地址（形如220.181.57.216），记下这个地址。

### 获得域名

我的域名是从 [Freenom](https://www.freenom.com) 免费获取的。五个免费的顶级域名都有12个月的免费期，每年到期前都能免费延12个月（其实相当于永久免费，只要你肯每年手动续域名）。网站有中文，注册都很方便。在配置域名之前，您需要了解[顶级域名、一级域名和二级域名的区别](https://zh.wikipedia.org/wiki/%E5%9F%9F%E5%90%8D#%E5%9F%9F%E5%90%8D%E5%B1%82%E6%AC%A1)。你购买的域名（例如 qhurc.com）中，“com”是顶级域名，“qhurc”是一级域名。还记得之前我们填写过的DNS记录吗？DNS记录的第一项——Name——就定义了二级域名。你从域名提供商处购买的域名为顶级域名下的一级域名——对于本博客来说，就是 .com 域名下的 qhurc.com 。之后对于该顶级域名，旗下的所有二级域名均归你支配——你可以随意地创建二级域名而不受限制，例如 fuck.qhurc.com，shit.qhurc.com ，随便你。购买域名后，会要求你添加DNS记录，如下图所示。其中，Name 栏目填写的项目就是上面提到的二级域名——当然，如果你不想指定二级域名（即浏览器直接访问 http://qhurc.com 而非 http://blog.qhurc.com），将 Name 栏留空即可。TTL 定义了你要给服务器发送的数据包贴多少钱的邮票——理论上 TTL 越高您的数据包就被允许传输地更远（经过更多的网关）。Target 处填写你的服务器的 IP 地址。

![Name 输入二级域名，Target输入VPS的IPv4地址](https://blog.qhurc.com/wp-content/uploads/2019/04/dns_name-1024x380.png)

Name 输入二级域名（http://二级域名.fkqs.ml/。若想配置形如http://fkqs.ml/的域名，则将Name留空），Target 输入VPS分配的IPv4地址

等待一段时间（几分钟），配置就生效了。不过此时服务器还没有配置web环境，故即使访问该域名也什么都显示不出来。所以下面，我们就需要

### 初次配置服务器

我们现在要通过ssh远程访问你的服务器。打开 Windows Powershell 或 Terminal，敲入

    $ ssh root@[your_server_ip] -p [your_ssh_port]

其中，开头的$是普通用户权限的意思，不需要敲入；将\[your\_server\_ip\]替换为你的VPS服务器的IPv4地址，将\[your\_ssh\_port\]替换为你的ssh端口。如果你不知道ssh端口是什么，那么直接输入22，这是默认端口。接下来，ssh可能会显示如下内容：

    $ ssh root@8.8.8.8 -p 22
    The authenticity of host '[8.8.8.8]:22 ([8.8.8.8]:22)' can't be established.
    ECDSA key fingerprint is SHA256:yiduiluanmayiduiluanmayiduiluanmayiduiluanma.
    Are you sure you want to continue connecting (yes/no)?

这段话的意思是，“我他喵的没见过这个服务器啊我不敢连啊这玩意没有病毒吧可能会把电脑搞炸的你知道吗！你还要冒着死亡的风险继续连接吗？”。当然了，你不需要理解这段话的意思，敲入“yes”+回车即可。ssh工具现在会提示你输入root用户的密码——还记得上面我让你记录的服务器密码吗？对没错！！就是那个！！！敲入密码的时候不会有显示的（对没错，连“*”号都没有）。如果登陆成功，你的终端将会显示如下内容：

    Warning: Permanently added '[8.8.8.8]:22' (ECDSA) to the list of known hosts.
    root@8.8.8.8's password:
    Last login: Sun Apr  7 21:20:00 2019 from 4.4.4.4
    [root@name ~]#

这时，你的终端已经连接到远程服务器了，你所做的所有事情、你访问的所有文件都只与远程服务器有关。注意这段代码的最后一行。root代表你的远程用户名是root，name代表你的服务器名称（你自己配置的）。“~”代表当前操作的文件夹（比如，如果你在/home/fuck/shit目录下，这个~就会变成shit。~代表用户登陆后的默认目录），#代表高级权限（与上文已经展示过的$相对——$代表低级权限）。如果你是使用默认端口登陆的，那么第一件事情就是修改ssh的默认端口——

    # vi /etc/ssh/sshd_config

同样地，#号不用键入。正常情况下，你的屏幕会变成

    #       $OpenBSD: sshd_config,v 1.93 2014/01/10 05:59:19 djm Exp $
    
    # This is the sshd server system-wide configuration file.  See
    # sshd_config(5) for more information.
    
    # This sshd was compiled with PATH=/usr/local/bin:/usr/bin
    
    # The strategy used for options in the default sshd_config shipped with
    # OpenSSH is to specify options with their default value where
    # possible, but leave them commented.  Uncommented options override the
    # default value.
    
    # If you want to change the port on a SELinux system, you have to tell
    # SELinux about this change.
    # semanage port -a -t ssh_port_t -p tcp #PORTNUMBER
    #
    #Port 9622
    #AddressFamily any
    #ListenAddress 0.0.0.0
    #ListenAddress ::

以及后面还有好多东西。我们先键入一个i，这时屏幕最后一行会变成

    -- INSERT --

这时就进入了vim编辑器的编辑模式。看到有一行写着 #port 22 了吗？把它前面的#去掉，然后把22改成其它你喜欢的端口（建议在8192以上，尽量在10000以下。这里设置为9622）。设置完成后，按一下“ESC”，你会发现最后一行的文字消失了！接着，输入——

    :wq

这三个字符会出现在最后一行消失的文字之上（这代表vim编辑器退出了编辑模式。:冒号代表进入指令模式。w代表保存，q代表退出）。确认无误后，按回车键，你会发现熟悉的命令行回来了。为了让上面的配置生效，你需要重新启动ssh服务。输入

    # systemctl restart sshd

这一操作会让你退出ssh。按照上面教过的方法重新登陆远程服务器即可（记得将\[your\_ssh\_port\]改为新的端口号哦）。如果你重新回来了，那么让我们

### 配置LNMP环境

**由于本文面向第一次配置环境的小白，所以下面所有复杂过程均应用快速脚本。**

LNMP代表Linux+Nginx+MySQL+PHP，这是WordPress必须的环境。我们现在用[LNMP.org](https://lnmp.org/auto.html)提供的脚本自动安装LNMP环境。

访问上面的网站，你会看到

![](https://blog.qhurc.com/wp-content/uploads/2019/04/lnmp_auto.png)

如果你的服务器内存大于1GB，请将MySQL版本选为8.0（或MySQL的最新版本），并将PHP版本选为7.2（或PHP的最新版本）。将数据库Root用户密码选择为你想要的，点击下方的“生成”按钮，该网站会为你生成一个脚本。将该脚本复制到你的ssh终端中并执行，形如：

    # wget http://soft.vpser.net/lnmp/lnmp1.5.tar.gz -cO lnmp1.5.tar.gz && tar zxf lnmp1.5.tar.gz && cd lnmp1.5 && LNMP_Auto="y" DBSelect="2" DB_Root_Password="lnmp.org" InstallInnodb="y" PHPSelect="5" SelectMalloc="1" ./install.sh lnmp

这段代码会执行10~20分钟。你可以坐下来泡杯咖啡，看一集《夏目友人帐》。《罪恶黑名单》也不错，但不要看太长时间——ssh在空闲一定时间后就会自动退出的，你还得重新登录一遍，很麻烦。

随后，你需要安装 PureFTPd 。输入 `# ./pureftpd.sh` 以安装 pureftpd。之后，在控制台中输入

    # usermod -G www ftp

以让 FTP 服务器对 Nginx 网站内的文件有操作权（这一步是为了支持 WordPress 的插件安装和本体更新）。

现在，你已经完成了最麻烦的内容——之后的事情，就很简单了。不过我猜你到这里已经又困又累了（我也是），所以，之后的内容回头再更新吧。