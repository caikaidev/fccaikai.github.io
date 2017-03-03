---
layout: post
title:  "将Android项目发布到本地Maven仓库"
date:   2017-03-03 09:58:00 +0800
categories: jekyll update
---
# 说明
android studio 中我们可以通过在gradle.build中添加配置来导入引用库，如：
```
compile 'com.kcode.github.pubutils:androidLib:0.0.1'

```
通过这个可以看出'com.kcode.github.pubutils:androidLib:0.0.1'由三部分组成，分别是Maven中的

```
<?xml version="1.0" encoding="UTF-8"?>
<project xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd" xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.kcode.github.pubutils</groupId>
  <artifactId>androidLib</artifactId>
  <version>0.0.1</version>
  <packaging>aar</packaging>
</project>

```
groupId:唯一标识符
artifactId：类似于项目名称
version：版本号
现在我们来创建一个自己的本地库。

## 第一步
新建一个Android项目。在gradle.properties中添加一下信息

```
#包信息
GROUP_ID = com.kcode.github.pubutils

# Licence信息
PROJ_LICENCE_NAME=The Apache Software License, Version 2.0
PROJ_LICENCE_URL=http://www.apache.org/licenses/LICENSE-2.0.txt
PROJ_LICENCE_DEST=repo

```
GROUP_ID：就是刚才的groupId

下面的Licence信息是固定的

## 第二步
创建一个Libary,我命名为AndroidLib
在Libary的根目录下新建gradle.properties文件，添加
```
#包信息
ARTIFACTID = androidLib
LIBRARY_VERSION = 0.0.2

#Mac下地址:file:///Users/<username>/my/local/repo
LOCAL_REPO_URL=file:///Users/caik/my/local/repo
```
ARTIFACTID：开始说的到artifactId
LIBRARY_VERSION：版本号
LOCAL_REPO_URL：本地仓库的地址：file://+本地路径

## 第三步
在Library也就是刚才创建的AndroidLib下的gradle.build中添加
```
apply plugin: 'maven'

uploadArchives{
    repositories.mavenDeployer{
        repository(url:LOCAL_REPO_URL)
        pom.groupId = GROUP_ID
        pom.artifactId = ARTIFACTID
        pom.version = LIBRARY_VERSION
    }
}
```
## 第四步
最后在项目路径下执行：
```
./gradlew -p <Library name> clean build uploadArchives --info
```
我这里就是：
```
./gradlew -p androidlib clean build uploadArchives --info

```

等执行结束到刚才设置好的
```
LOCAL_REPO_URL=file:///Users/caik/my/local/repo
```
路径下就可以看到已经发布好的本次仓库

## 第五步
引用，先在最外层的gradle.build中添加本地maven库路径
```
// Top-level build file where you can add configuration options common to all sub-projects/modules.

buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.1.0'

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}

allprojects {
    repositories {
        jcenter()

        maven {
            url 'file:///Users/caik/my/local/repo/'
        }
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}

```

就是：
```
maven {
            url 'file:///Users/caik/my/local/repo/'
    }
```
然后在需要引用这个库的项目的gradle.build中添加：
```
compile 'com.kcode.github.pubutils:androidLib:0.0.1'

```
就可以了。

项目GitHub地址:https://github.com/fccaikai/LocalMavenDemo