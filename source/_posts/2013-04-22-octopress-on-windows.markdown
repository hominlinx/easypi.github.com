---
layout: post
title: "在windows 7 上部署已有的中文octopress博客"
date: 2013-04-22 16:59
comments: true
categories: summary
---

之前在ubuntu下搭建了octopress的博客，现在想在实验室的windows 7上也搭建起来。网上的文章多为在windows搭建新的octopress博客，本文则是将已有的octopress博客部署到windows 7的环境下。

<!--more-->

## 安装依赖

1. [安装git](http://git-scm.com/)
2. [安装ruby for windows](http://rubyinstaller.org/downloads/),本文使用[ruby 1.9.3-p392](http://rubyforge.org/frs/download.php/76798/rubyinstaller-1.9.3-p392.exe)
3. [安装ruby develop kit](http://rubyinstaller.org/downloads/), 本文使用[DevKit-tdm-32-4.5.2-20111229-1559-sfx.exe](https://github.com/downloads/oneclick/rubyinstaller/DevKit-tdm-32-4.5.2-20111229-1559-sfx.exe) 并解压到文件夹如`c:\RubyDevKit`
4. cmd中运行命令

        cd C:/RubyDevKit
        ruby dk.rb init
        ruby dk.rb install

## 安装octopress

1. 创建博客文件夹，如`d:\myblog`
2. 克隆自己的octopress到本地， `username 改为自己的`
        git clone -b source https://github.com/username/username.github.com.git
        cd octopress 
        git clone https://github.com/username/username.github.com.git _deploy #下载_deploy版本（用于发布）

3. 安装octopress
        gem install bundler
        bundle install

4. 解决中文支持问题。由于windows 7的默认字符编码为gbk，而octopress支持的是utf8。因此需要解决二者之间的冲突，否则对中文博客会产生错误。 最简单的解决方法如下：

4.1 打开git bash， 运行`cd `到用户根文件夹

4.2 查看目录下是否存在`.bash_profile`,如果不存在则使用`touch .bash_profile`新建该文件.

4.3 编辑`.bash_profile`，添加如下两行
        export   LC_ALL=zh_CN.UTF-8
        export   LANG=zh_CN.UTF-8

4.4 重启git bash， 切换到octopress目录，生成博客并预览观察是否部署成功
        rake generate
        rake preview

## 博客发布流程记录

由于只更改了git bash的配置文件，因此博客发布都在git bash下进行。

1. 新建博客-->编辑 --> 生成 --> 预览 --> 发布
        rake new_post['title'] 
        # edit markdown file
        rake generate
        rake preview
        rake deploy

2. 备份源码到source分支
        git add .
        git commit -m "commit comments"
        git push origin source


## 参考
+ [Setup Octopress on Windows7](http://stb.techelex.com/setup-octopress-on-windows7/)
+ [为自己现有的github octopress配置环境](http://www.cnblogs.com/hangxin1940/archive/2012/03/19/2806438.html)
+ [在 Windows7 下从头开始安装部署 Octopress](http://sinosmond.github.io/blog/2012/03/12/install-and-deploy-octopress-to-github-on-windows7-from-scratch/)