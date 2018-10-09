---
title: '用Github Page+Hexo轻松搭建个人博客'
date: 2017-12-23 09:58:04
updated: 2017-12-31 09:58:04
categories:
    - Blog
tags:
    - Blog
    - Hexo
---

在2018年来临之际，笔者拖延了一年多的博客终于上线了，本文基于笔者的实践经验，即使你是一个技术小白，按照本文的步骤，也可以轻松搭建自己的博客。

### 为什么要搭建个人博客？

- 总结和写作能力很重要
- 独立的才是自己的，所有文章都可永久保存在自己的服务器
- 畅所欲言，不受监管审查
- 很酷

<!--more-->

### 搭建个人博客的方法

搭建个人博客，一般有三种方法：
- Wordpress：70%的网站都采用这个方法，商业化成熟，简单门槛低；
- 静态网页+博客框架：代表是Github Page+Hexo，有一定的技术门槛，好处是不用VPS，本文会详细介绍此方案；
- 自己编写一个网站：前后端都自己实现，嗯，门槛高又费时间，不提了。

### Github Page是什么
Github是全球最大的开源社区，Github Page是面向用户、组织和项目开放的公共静态页面搭建托管服务，站点可以被免费托管在Github上。

### Hexo是什么
你可以将你的静态页面直接放在Github Page上，也可以用Hexo或者Jekyll等博客框架自动生成站点。
> Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。

### 准备
1. Github账号：官网：[Github](https://github.com/)
2. Git：[Git](https://git-scm.com/)下载安装程序
3. Node.js：你可以通过[Node.js](https://nodejs.org)下载安装程序，也可以用Git Bash安装
4. Markdown编辑器：用于写文章，如果你还不知道Markdown，强烈建议你去了解下[Markdown](http://wowubuntu.com/markdown/#list)的语法

### Hexo
#### 安装
打开Git Bash，输入以下命令安装Hexo
```
$ npm install -g hexo-cli
```

#### 初始化
打开Git Bash，输入命令
```
$ hexo init [folder]
```
[folder]代表的是你的hexo文件夹名，如果不写，就默认在当前文件夹初始化。
然后进入你的hexo文件夹
```
$ cd [folder]
```

#### 生成静态文件
需要执行generate命令生成静态文件，才能部署到服务器
```
$ hexo g
```

#### 启动服务器
由于还没有配置Github，所以我们先部署到本地服务器
```
$ hexo server
```
默认情况下，访问网址为： `http://localhost:4000/`
在某些情况下，你可能4000的端口被占用，可以选择重设端口
```
$ hexo -p 5000 server
```
这样的话地址就变成了`http://localhost:5000/`
打开浏览器，输入地址你就可以看到你初始的博客啦。

### Github配置
接下来，我们来看看如何将Hexo部署到Github Page上

#### 创建Github仓库
创建与你的Github用户名相对应的Github Page仓库，格式必须是username.github.io。
![创建Github Page仓库](http://os21ow86u.bkt.clouddn.com/Snipaste_2017-12-30_14-00-07.png)

#### 建立关联
进入你刚创建的仓库，复制仓库的地址
![复制仓库地址](http://os21ow86u.bkt.clouddn.com/Snipaste_2017-12-30_15-27-53.png)

打开hexo文件夹下的_config.yml文件，拉倒最下面，修改deploy为以下代码，repository后面的是你的仓库地址

```yaml
deploy: 
  type: git
  repository: git@github.com:peterzhen40/peterzhen40.github.io.git
  branch: master
```
这里需要注意的是，Github仓库有两种连接方式：
- HTTPS：每次上传到仓库都需要输入账号密码
- SSH：不用每次上传后输入账号密码，但需要配置SSH Key

具体的配置方法这里就不再详述了，有兴趣的朋友请参考这条[链接](http://beiyuu.com/github-pages)

#### 部署

输入以下命令部署到Github Page

```
$ hexo d
```

然后在浏览器输入`username.github.io`就可以看到你的博客啦，如：[peterzhen40.github.io](http://peterzhen40.github.io)

之后的部署就是三步

```
$ hexo clean
$ hexo g
$ hexo d
```

### 绑定域名

如果你不喜欢Github Page指定的子域名，你可以选择自己购买域名，然后绑定使用。

#### 购买域名

域名购买水还挺深的，国内的有阿里云（万网），但国内的域名需要备案，你懂的；国外的选择就多了，比较著名的有GoDaddy，记得用优惠码就是了，就不详细介绍了。

#### 域名解析

用域名供应商提供的域名解析器或者[DNSpod](https://www.dnspod.cn/)，添加以下记录：

![域名解析设置](http://os21ow86u.bkt.clouddn.com/Snipaste_2017-12-31_14-50-45.png)

其中两条记录A指向的ip地址是Github Page提供的ip，CNAME记录指定的是你在Github创建的仓库。

等解析生效之后呢，`www.username.github.io`和`username.github.io`都会指向你购买的域名。

#### 绑定域名

最后一步，你需要配置你的仓库，使得`username.github.io`能通过你的域名来访问。在hexo的source目录下，创建CNAME文件，没有后缀，打开文件后输入你的域名，如

```
peterzhen.cn
```

保存之后部署到服务器，配置就完成了。

### 写作

#### Markdown

如果你不喜欢枯燥的text文本，又对html繁杂的标签无从入手，那么笔者推荐你使用Markdown

> **Markdown** 是一种轻量级标记语言。它允许人们使用"易读易写"的纯文本格式编写文档，然后转换成有效的HTML文档。

简单来说，Markdown就是带格式的文本，Markdown的[语法](http://wowubuntu.com/markdown/#list)非常简单，"易读易写"，只要你接触过了，相信我，你会对其爱不释手，再也不想去写什么text文本和word文档了。
markdown编辑器，win平台，我推荐[Typora](https://typora.io/)。

#### Hexo写作

Hexo默认使用markdown语法来写作，你可以执行以下命令来创建一篇新文章：

```
$ hexo new [layout] <title>
```

你可以在[layout]中指定文章的布局，默认是post，hexo默认有三种布局，每种布局的路径不同：

| 布局    | 路径             |
| ----- | -------------- |
| post  | source/_posts  |
| page  | source         |
| draft | source/_drafts |

其中 draft是指草稿，默认不会显示在页面上。

在新建文件时，Hexo会根据scaffolds目录的模版文件来创建文件，所以你可以修改模版文件来达到更好的初始化效果，如：

```
---
title: {{ title }}
date: {{ date }}
categories:

tags:

---

<!--more-->
```

上面文件最上方以 `---` 分隔的区域文本有点特殊，它并不是markdown代码，而是Hexo的Front-matter，用于指定个别文件的变量，如：

| 参数         | 描述   |
| ---------- | ---- |
| title      | 标题   |
| date       | 日期   |
| categories | 分类   |
| tags       | 标签   |

需要注意的是使用categories和tags前需要先生成相应的页面

```
$ hexo new page tags
$ hexo new page categories
```

如何只显示文章的一部分和阅读更多呢？在显示内容后加一个`<!--more-->`就可以了。

### 写在最后

一个简单的博客至此就搭建完毕了，如果你想要更个性化的界面和功能的话，就要涉及到`主题`了，时间有限，这部分内容咱们下篇文章再聊，敬请关注。

其实搭建一个博客是很简单的事情，坚持总结和写作才是最难的事，别像笔者一样半年才憋出一篇文章，哈哈。






### 参考：
[hexo官方文档](https://hexo.io/zh-cn/docs/)

[HEXO搭建个人博客](http://baixin.io/2015/08/HEXO%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/)

[如何搭建一个独立博客](http://www.cnfeat.com/blog/2014/05/11/how-to-build-a-blog/)

