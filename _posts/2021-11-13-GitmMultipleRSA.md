---
layout: post
title: 如何支持将RSA密钥配置在不同GitHub仓库内？
date: 2021-11-13 
tags: 开发小技巧  
---



## 场景
由本地电脑的`~/.ssh/id_ras`密钥对的公钥已经配到公司的Github仓库内，然而在我往个人仓库内拉取代码进行push时，会出现如下提示：

![image-20211112184621442](/images/posts/small_tips/image-20211112184621442.png)

于是....，我就开始找寻如何一台电脑上如何能将RAS密钥配置在不同的仓库下，就有了下面操作。



## 解决方案

>主要通过生成不同的rsa密钥对，将不同的密钥对分别配置在Github仓库上，然后在对应Github项目下配置代理来实现使用不同的r sa私钥来push代码。



### 1、生成多个RSA密钥对

打开Git Bash输入以下，全部按回车，默认会生成id_rsa，id_rsa.pub文件，指定邮箱和文件名可以生成更多的key值，让不同的仓库使用(邮箱随意填)。

```
ssh-keygen -t rsa -C "kieren@kieren.com” -f ~/.ssh/id_rsa
ssh-keygen -t rsa -C "little_five@little_five.com” -f ~/.ssh/my_id_rsa
```

在~/.ssh/目录下添加config文件

![image-20211112185547849](/images/posts/small_tips/image-20211112185547849.png)



### 2、设置代理　

给代理设置不同的名称(Host)和要使用的RSA文件(IdentityFile)。

~~~
Host github.com
    HostName github.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa

Host my.github.com
    HostName github.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/my_id_rsa
~~~



### 3、在Github上配置SSH公钥

![image-20211112190229761](/images/posts/small_tips/image-20211112190229761.png)

验证是否可行：

```
ssh -T git@github.com
ssh -T git@my.github.com
```

![image-20211112190358381](/images/posts/small_tips/image-20211112190358381.png)



### 4、修改项目的`.git\config`文件

若使用的默认的RSA密钥对拉去的项目则无需修改，若需要使用代理拉去即本例中的`git@my.github.com`则需要修改。

![image-20211112190931371](/images/posts/small_tips/image-20211112190931371.png)

代码如下：

```python
url = git@github.com:xxx/xxx.git
# 修改为
url = git@my.github.com:xxx/xxx.git
```

这一步主要于第二步对应，使用代理的配置的RSA进行git操作。



 