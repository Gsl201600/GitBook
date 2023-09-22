---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Git SourceTree配置

如果你是通过`https`协议拉的代码可以不用配置`SSH Key`，通过`git@`协议的需要配置

创建`SSH Key`，因为本地的`Git`仓库与`Github`远程仓库之间是通过`SSH`加密的。首先，需要到主目录上查看是否有`.ssh`目录，再查看`.ssh`目录下有没有`id_rsa`和`id_rsa.pub`文件，打开终端输入命令`cd ~/.ssh`查看是否有`ssh`文件，如下

![查找.ssh目录](https://raw.githubusercontent.com/Gsl201600/PicGoImg/master/img/2018.06.27.01.jpg)

发现没有上述的两个文件，这时需要创建： 输入`ssh-keygen -t rsa -C "youremail@example.com"` 会在`.ssh`目录下生成`id_rsa、id_rsa.pub`两个文件，`id_rsa`是私钥，需要自己保留好，`id_rsa.pub`是公钥，别人知道也无妨，中间引号内容是自己的邮箱账号，后面连续输入三次回车即可

输入命令`cat ~/.ssh/id_rsa.pub`查看获取到的公钥，复制这个公钥内容，添加到`github`的`ssh`配置里面

配置完之后回到终端，输入`ssh git@github.com`验证一下`ssh`是否连接成功，这时候出现例如

```
PTY allocation request failed on channel 0
Hi *****! You've successfully authenticated, but GitHub does not provide shell access.
Connection to github.com closed.
```

这种即为`ssh`连接成功

这一步的主要目的是生成一个`known_hosts`文件，否则会出现在`SourceTree`上输入`ssh`地址之后，一直在转圈验证地址的情况

![](https://raw.githubusercontent.com/Gsl201600/PicGoImg/master/img/2018.06.27.02.png)

[附：我的博客地址](https://gsl201600.github.io/2019/02/26/GitSourceTree%E9%85%8D%E7%BD%AE/)
