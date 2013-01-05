---
layout: post
title: "利用Octopress在github pages上搭建个人博客"
date: 2013-01-05 22:19
comments: true
categories: [summary]
---

github不仅能够提供代码托管和版本控制系统，现在又有了github pages功能，可以让你将个人的博客搭建在此，通过git 工具方便的发布个人博客。由于pages只提供了管理和呈现静态网页的功能，而静态网页的生成则需自己实现。Jekyll工具可用来实现静态网页的生成，但是使用起来相对麻烦，需要对Html,CSS和javascript比较熟悉。作为希望更加关注内容的博客用户来说，Octopress给出了一个更好的选择，Octopress是一个基于Jekyll的博客生成框架，它处理了Jekyll那些麻烦的配置，安装后只需几条简单的命令就可以发布你的博客了。

本文就是基于该系统发布的第一篇，本文记录了在ubuntu 12.04系统下搭建该博客系统的过程，同时也加入了一些自己的配置，比如增加新浪微波侧边栏、去除分享到twitter等国内不能使用的功能、增加分享到微博等网站的功能、添加评论系统等。现在做一总结，分享给大家。

<!--more-->

## 安装

Octopress的安装还是很方便的,按照其官方文档<http://octopress.org/docs/setup>就能安装成功,在此只是翻译和简单罗列一下。

### 安装Ruby

大家现在应该已经有了git系统，关于git本文不多做介绍，默认大家已经会用git系统了。

首先安装Octopress依赖的的Ruby, Octopress 2.0依赖的是Ruby 1.9.3版本，可以在终端中输入`ruby --version`查看版本信息。Ubuntu系统中可以使用apt-get 安装，但是注意默认安装的Ruby版本可能不对，因此安装时需指明版本：

{% codeblock %}
sudo apt-get install ruby1.9.3
{% endcodeblock %}

现在终端中输入`ruby --version`查看版本信息应该是正确了。如果之前安装过Ruby但是版本不符合的，则需要使用rbenv或RVM来进行安装，具体可查看<http://octopress.org/docs/setup/rbenv/> 或 <http://octopress.org/docs/setup/rvm/>.

### 安装Octopress

首先将Octopress工具clone到本地一文件夹：
{% codeblock %}
git clone git://github.com/imathis/octopress.git octopress
cd octopress   
{% endcodeblock %}

然后安装其它依赖
{% codeblock %}
gem install bundler
rbenv rehash    # If you use rbenv, rehash to be able to run the bundle command
bundle install
{% endcodeblock %}

最后安装默认Octopress主题
{% codeblock %}
rake install
{% endcodeblock %}

至此Octopress安装完成。

## 配置github pages

github pages 可以提供一个域名为`http://username.github.com`的博客，首先需要在github新建一个名为`username.github.com`的代码仓库，然后就可以使用Octopress博客部署到该域名上了。(该username必须与你在github上的用户名一致)

在代码仓库建立完成后，可以使用Octopress提供的脚本完成后面的部署工作：
{% codeblock %}
rake setup_github_pages
{% endcodeblock %}
在此过程会询问你的代码仓库的地址，按提示完成即可。此过程可能需要提供SSH key,如果没有可以参照<https://help.github.com/articles/generating-ssh-keys>设置。

然后就可以将默认的博客发布到你自己的域名了：
{% codeblock %}
rake generate
rake deploy
{% endcodeblock %}

几分钟后，你的域名上应该已经有了默认的博客了，至此安装和配置github pages就完成了。你还可以将Octopress目录加入到github中进行管理：
{% codeblock %}
git add .
git commit -m 'your message'
git push origin source
{% endcodeblock %}

## 配置Octopress

Octopress的配置主要在`_config.yml`文件中，可以配置博客的主副标题，作者名，邮箱等，配置十分方便。在此建议将国内不能使用的功能，如twitter,google+等相关项设为false或直接删除，随后将介绍如何使用新浪微博等代替。

## 开始写文章

使用Octopress书写和发布文章只需几条命令即可。首先运行
{% codeblock %}
rake new_post["title"]
{% endcodeblock %}
该命令将会在`source/_posts`目录下生成一个名为`YYYY-MM-DD-post-title.markdown`的文件，编辑该文件即可书写你的文章了。文章书写使用markdown语法，<http://wowubuntu.com/markdown/index.html>给出了markdown语法的介绍，该语法简单方便。

在文章书写完成后，就可以生成静态网页了：
{% codeblock %}
rake generate
{% endcodeblock %}
如果想要在发布到github上之前在本机进行预览，执行下面的命令，然后访问<http://localhost:4000> 就可以看到完成的状态了。
{% codeblock %}
rake preview
{% endcodeblock %}
最后可以发布到github上了，
{% codeblock %}
rake deploy
{% endcodeblock %}

至此，你已经可以在自己的博客上写文章了。<http://octopress.org/docs/blogging/plugins/>这里给出了常用的书写文章时可以添加代码、图片、视频等内容的语法，用此可以生成图文并茂的文章。

## 高级配置
在此部分主要介绍在侧边增加新浪微博秀，在文章底部增加分享到和评论的功能。

### 新浪微博秀

*  在`_config.yml`文件中将`default_asides`处代码改为如下，即去除不要的侧边栏，并加入微博侧边栏。
{% codeblock %}
default_asides: [asides/recent_posts.html, asides/github.html, custom/asides/weibo.html]
{% endcodeblock %}    
*  到<http://app.weibo.com/tool/weiboshow>获取自己微博秀的代码。
*  在`source/_includes/custom/asides`文件夹下新建weibo.html文件并编辑如下,将获取的微博秀代码插入到相应位置：
{% codeblock %}
<section>
    <h1>新浪微博</h1>
    <ul id="weibo">
    <li>

    <!-- 在此插入获得的微博秀代码 -->

      </li>
    </ul>
</section>
{% endcodeblock %}

至此你的侧边就有了你自己的微博秀了。

### 分享到和评论功能

*  在`_config.yml`文件中增加一项，可用此来控制是否开启分享到和评论功能，设为`false`则可关闭：
{% codeblock %}
# Sina Weibo
weibo_sharing: true
{% endcodeblock %}

*  编辑`/source/_includes/post/sharing.html`,增加如下代码：
{% codeblock %}
//下面的大括号与百分号间无空格，如果复制，请去掉  
{ % if site.weibo_sharing % }
{ % include custom/weibo_sharing.html % }
{ % endif % }
{% endcodeblock %}

*  在`source/_includes/custom`下新建`weibo_sharing.html`文件。
*  到<http://www.bshare.cn/>获取喜欢按钮样式的分享代码，加入到`weibo_sharing.html`中，即可增加分享到按钮功能。
*  到<http://www.uyan.cc/>获取评论功能的代码，加入到`weibo_sharing.html`中，即可增加评论功能。

## 参考
*   <http://octopress.org/docs/>
*   <https://help.github.com/categories/20/articles>
*   <http://blog.devtang.com/blog/2012/02/10/setup-blog-based-on-github/>



