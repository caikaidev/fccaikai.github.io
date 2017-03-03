---
layout: post
title:  "发布项目到Github"
date:   2017-03-03 10:00:00 +0800
categories: jekyll update
---
## 说明
GitHub上有许多优秀的开源代码。我们也可以讲自己的代码托管在Github上，现在就开始讲解一下如何将本地的代码提交到GitHub。现在已Android studio项目为例（其他项目都是一样）

### 第一步
先做好准备工作：
首先，你需要有一个GitHub帐号，
https://github.com ，如何注册这个就不用说了。
然后安装Git。在https://git-scm.com 下载对应的系统版本就好了。
输入
```
git --version
```
出现以下结果就说明安装成功了
![这里写图片描述](http://upload-images.jianshu.io/upload_images/1715403-00317ef326504a12?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

用刚才注册的GitHub帐号登录完成后，可以看到：
![这里写图片描述](http://upload-images.jianshu.io/upload_images/1715403-53c06db2a418de48?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


点击'New Repository'创建一个新的仓库

![这里写图片描述](http://upload-images.jianshu.io/upload_images/1715403-fb97051317dc8064?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
项目名称必须要填，描述可以不用，最后一个是否初始化README可以选，也可以后面自己新建。暂时先不选。
点击'create repository' 完成创建。
![这里写图片描述](http://upload-images.jianshu.io/upload_images/1715403-98b000fc43f00b9c?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
先把框里面的路径复制下来，后面需要用到，现在在GitHub上的操作基本已经完成了。

## 第二步
先进入到项目根目录下
```
cd <项目路径>
```
再进行初始化
```
git init
```
![这里写图片描述](http://upload-images.jianshu.io/upload_images/1715403-aaf3c53483c3efa3?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用
```
git add .
```
将本目录下的所有文件添加到git管理
然后使用
```
git commit -m '备注信息'
```
提交直本地git仓库，现在已经完成了本地的提交，现在我们需要继续关联到GitHub上
使用
```
git remote add origin https://github.com/caikcode/GitHubDemo.git
```
https://github.com/caikcode/GitHubDemo.git 就是在GitHub上新建完仓库后获得的地址
最后使用
```
git push -u origin master
```
将本地代码提交至GitHub

注意：第一次使用的时候会提示配置用户名密码，将你在GitHub上注册的配置一下就好了。