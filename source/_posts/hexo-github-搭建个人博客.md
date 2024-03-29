---
title: hexo + github 搭建个人博客
date: 2019-03-16 23:26:36
categories: Hexo
comments: false
tags: 
- Hexo
- Next
top: 10
description: 使用hexo+github搭建免费个人博客详细教程
---

[原文链接](http://blog.liuxianan.com/build-blog-website-by-hexo-github.html)原文写的真不错，步骤清晰，我加了一些内容细化了整个过程。

## 使用github pages服务搭建博客的好处有：

1. 全是静态文件，访问速度快；
2. 免费方便，不用花一分钱就可以搭建一个自由的个人博客，不需要服务器不需要后台；
3. 可以随意绑定自己的域名，不仔细看的话根本看不出来你的网站是基于github的；
4. 数据绝对安全，基于github的版本管理，想恢复到哪个历史版本都行；
5. 博客内容可以轻松打包、转移、发布到其它平台；

## 1. 准备工作

- 有一个github账号，没有的话去注册一个；
- 安装了node.js、npm，并了解相关基础知识；
- 安装了git

## 2. 搭建github博客

### 2.1 搭建github博客

1. 注册的邮箱一定要验证，否则不会成功；
2. 仓库名字必须是：username.github.io，其中username是你的用户名；

### 2.2 配置SSH key

为什么要配置这个呢？因为你提交代码肯定要拥有你的github权限才可以，但是直接使用用户名和密码太不安全了，所以我们使用ssh key来解决本地和服务器的连接问题。

```
$ cd ~/. ssh #检查本机已存在的ssh密钥
```

如果提示：No such file or directory 说明你是第一次使用git。

```
$ ssh-keygen -t rsa -C "邮件地址"
```
然后连续3次回车，最终会生成一个文件在用户目录下，打开用户目录，找到.ssh\id_rsa.pub文件，记事本打开并复制里面的内容，打开你的github主页，进入个人设置 -> SSH and GPG keys -> New SSH key：

测试是否成功

```
$ ssh -T git@github.com # 注意邮箱地址不用改
```

你还需要配置：

```
$ git config --global user.name "lqwang521"// 你的github用户名，非昵称
$ git config --global user.email  "731696053@qq.com"// 填写你的github注册邮箱
```

## 3. 使用hexo

### 3.1 介绍

Hexo是一个简单、快速、强大的基于 Github Pages 的博客发布工具，支持Markdown格式，有众多优秀插件和主题。

[官网](http://hexo.io)
[github](https://github.com/hexojs/hexo)

### 3.2 原理

由于github pages存放的都是静态文件，博客存放的不只是文章内容，还有文章列表、分类、标签、翻页等动态内容，假如每次写完一篇文章都要手动更新博文目录和相关链接信息，相信谁都会疯掉，所以hexo所做的就是将这些md文件都放在本地，每次写完文章后调用写好的命令来批量完成相关页面的生成，然后再将有改动的页面提交到github。

### 3.3 注意事项

1. hexo不同版本差别比较大，网上很多文章的配置信息都是基于2.x的，所以注意不要被误导；
2. hexo有2种_config.yml文件，一个是根目录下的全局的_config.yml，一个是各个theme下的；
3. 如果用windows系统可需要注意
，很多命令既可以用Windows的cmd来完成，也可以使用git bash来完成，但是部分命令会有一些问题，为避免不必要的问题，建议全部使用git bash来执行；

### 3.4 安装

```
$ npm install -g hexo
```

### 3.5 初始化

进入一个提前创建好的目录执行如下命令

```
$ hexo init
```

hexo会自动下载一些文件到这个目录，包括node_modules等等

```
$ hexo g # 生成
$ hexo s # 启动服务
```

hexo s是开启本地预览服务，打开浏览器访问 http://localhost:4000 即可看到内容

### 3.6 修改主题

[hexo主题官网](https://hexo.io/themes/)

我使用的是简单大方的next主题，进入themes目录下直接获取源码就可以，这种方式可以任意切换版本和更新，推荐这种方式

```
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```

修改_config.yml中的theme: landscape改为theme: yilia，然后重新执行hexo g来重新生成。

如果出现一些莫名其妙的问题，可以先执行hexo clean来清理一下public的内容，然后再来重新生成和发布。

### 3.7 上传之前

在上传代码到github之前，一定要记得先把你以前所有代码下载下来（虽然github有版本管理，但备份一下总是好的），因为从hexo提交代码时会把你以前的所有代码都删掉。

### 3.8 上传到github

如果你一切都配置好了，发布上传很容易，一句hexo d就搞定，当然关键还是你要把所有东西配置好。

首先，ssh key肯定要配置好。

其次，配置_config.yml中有关deploy的部分：

```
deploy:
  type: git
  repository: git@github.com:liuxianan/liuxianan.github.io.git
  branch: master
```

执行hexo d的话一般会报如下错误

```
Deployer not found: github 或者 Deployer not found: git
```

安装插件解决

```
npm install hexo-deployer-git --save
```

### 3.9 保留CNAME、README.md等文件

由于hexo默认会把所有md文件都转换成html，包括README.md，所有需要每次生成之后、上传之前，手动将README.md复制到public目录，并删除README.html。

一些非md文件可以把他们放到source文件夹下，这里的所有文件都会原样复制（除了md文件）到public目录的.

### 3.10 常用hexo命令

常见命令

```
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo generate #生成静态页面至public目录
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo deploy #部署到GitHub
hexo help  # 查看帮助
hexo version  #查看Hexo的版本
```

### 3.11 修改_config.yml

需要特别注意的地方是，冒号后面必须有一个空格，否则可能会出问题。

### 3.12 开始写博客

定位到我们的hexo根目录，执行命令：

```
hexo new 'my-first-blog'
```

hexo会帮我们在_posts下生成相关md文件：

```
---
title: postName #文章页面上的显示名称，一般是中文
date: 2013-12-02 15:30:16 #文章生成时间，一般不改，当然也可以任意修改
categories: 默认分类 #分类
tags: [tag1,tag2,tag3] #文章标签，可空，多标签请用格式，注意:后面有个空格
description: 附加一段文章摘要，字数最好在140字以内，会出现在meta的description里面
---

以下是正文

```

那么hexo new page 'postName'命令和hexo new 'postName'区别

最终部署时生成：hexo\public\my-second-blog\index.html，但是它不会作为文章出现在博文目录。

#### 3.12.1 写博客工具

我一种用的都是MWeb，也可以使用Typora

#### 3.12.2. 如何让博文列表不显示全部内容

默认情况下，生成的博文目录会显示全部的文章内容，如何设置文章摘要的长度呢？

答案是在合适的位置加上`<!--more-->`即可



## 4. 绑定域名

这个是可选的，你不绑定域名肯定也是可以的，就用默认的 xxx.github.io 来访问，我是通过阿里云注册的域名，绑定域名分2种情况：带www和不带www的。

域名配置最常见有2种方式，CNAME和A记录，CNAME填写域名，A记录填写IP，由于不带www方式只能采用A记录，所以必须先ping一下你的用户名.github.io的IP，然后到你的域名DNS设置页，将A记录指向你ping出来的IP，将CNAME指向你的用户名.github.io，这样可以保证无论是否添加www都可以访问



然后到你的github项目根目录新建一个名为CNAME的文件（无后缀），里面填写你的域名，加不加www看你自己喜好，因为经测试：

- 如果你填写的是没有www的，比如 wangliquan.site，那么无论是访问 http://www.wangliquan.site 还是 http://wangliquan.site ，都会自动跳转到 http://wangliquan.site
- 如果你填写的是带www的，比如 www.wangliquan.site ，那么无论是访问 http://www.wangliquan.site 还是 http://wangliquan.site ，都会自动跳转到 http://www.wangliquan.site
- 如果你填写的是其它子域名，比如 abc.wangliquan.site，那么访问 http://abc.wangliquan.site 没问题，但是访问 http://wangliquan.site ，不会自动跳转到 http://abc.wangliquan.site

另外说一句，在你绑定了新域名之后，原来的`你的用户名.github.io`并没有失效，而是会自动跳转到你的新域名。


## 5.  图片资源的处理方案


使用完`hexo new "这是一个新的博客"`命令之后，会在source/_post文件夹里面就会出现一个“这是一个新的博客.md”的文件和一个“这是一个新的博客”的文件夹。

1. 引用图片的第一种方法

```
{% asset_img 这是一个新的博客的图片.jpg 这是一个新的博客的图片的说明 %}
```

用此种方法，而不是以前的`![]()`方法，前提是你的hexo的版本是hexo3以上，到package.json里面看一下吧。如果不是hexo3以上的版本，那就只能用第二种方法了。

2. hexo插件的方法

[插件链接](https://link.jianshu.com/?t=https://github.com/CodeFalling/hexo-asset-image)

安装

```
npm install hexo-asset-image --save
```

之后就可以按照正常的方法使用的

```
我现在写了一个段落，并且想在这个段落的某一个地方![图片上传失败...](image-43fc5f-1510018038370)引入一张图片
```

Mac有一个图床神器 iPic，在App Store中就可以下载

[iPic - Markdown 图床、文件上传工具](https://toolinbox.net/iPic/)

[在 iPic 中添加阿里云 OSS](https://toolinbox.net/iPic/AddAliOSS.html)



## 6. 设置主题

[next主题](https://github.com/iissnan/hexo-theme-next)

[NexT 使用文档](https://theme-next.iissnan.com/)

[Hexo-NexT配置超炫网页效果](https://www.jianshu.com/p/9f0e90cc32c2)



由于busuanzi(不蒜子)的网址更新,导致了使用Hexo Next主题时统计浏览数失效.

不蒜子官网:http://ibruce.info/2015/04/04/busuanzi/

解决方法:

到hexo的themes文件夹下, 进入

\themes\next\layout_third-party\analytics\busuanzi-counter.swig

将src=“https://dn-lbstatics.qbox.me/busuanzi/2.3/busuanzi.pure.mini.js”

修改为src=“https://busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js”


### 参考

[利用GitHub搭建Hexo博客并开启HTTPS](https://www.cnblogs.com/yinxiang/p/9237488.html)

[如何搭建个人博客 ？Hexo + GitHub 是一个不错的选择](https://www.jianshu.com/p/eded1dd2d794)


