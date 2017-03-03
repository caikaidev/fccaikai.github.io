---
layout: post
title:  "Android 自定义轮播图片控件使用"
date:   2017-03-03 09:56:00 +0800
categories: jekyll update
---
### 效果图
直接先上效果图。

![轮播图.gif](http://upload-images.jianshu.io/upload_images/1715403-cbf7c309b85e2770.gif?imageMogr2/auto-orient/strip)

###实现原理
本篇主要讲如何在项目中引用。具体实现请查看[Android 自定义轮播图](http://www.jianshu.com/p/5c47cb6afe0f)

###添加依赖
在module的```build.gradle```中添加：

```
allprojects {
    repositories {
        jcenter()
        maven { url "https://jitpack.io" }
    }
}
```

在app的```build.gradle```中添加:

```
	compile 'com.github.fccaikai:AutoScrollViewPager:0.7.0', {
        exclude group: 'com.android.support', module: 'appcompat-v7'
    }
```


```
exclude group: 'com.android.support', module: 'appcompat-v7'
```
是为了解决appcompat版本的冲突，因为我这里用的是24.2.0版本，你们的app可能不是这个版本。

###布局文件

```
activity_main.xml
```

```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.kcode.autoviewpager.MainActivity">

    <com.kcode.autoscrollviewpager.view.AutoScrollViewPager
        android:id="@+id/viewPager"
        android:layout_width="match_parent"
        android:layout_height="220dp">

    </com.kcode.autoscrollviewpager.view.AutoScrollViewPager>
</RelativeLayout>

```

###Activity

```
package com.kcode.autoviewpager;

import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.widget.ImageView;
import android.widget.Toast;

import com.kcode.autoscrollviewpager.view.AutoScrollViewPager;
import com.kcode.autoscrollviewpager.view.BaseViewPagerAdapter;
import com.kcode.autoviewpager.bean.Picture;
import com.squareup.picasso.Picasso;

import java.util.ArrayList;
import java.util.List;

public class MainActivity extends AppCompatActivity {

    private AutoScrollViewPager mViewPager;

    private String[] paths = {"https://ss3.baidu.com/-fo3dSag_xI4khGko9WTAnF6hhy/image/h%3D200/sign=c493b482b47eca800d053ee7a1229712/8cb1cb1349540923abd671df9658d109b2de49d7.jpg",
            "https://ss0.baidu.com/94o3dSag_xI4khGko9WTAnF6hhy/image/h%3D200/sign=45fbfa5555da81cb51e684cd6267d0a4/2f738bd4b31c8701491ea047237f9e2f0608ffe3.jpg",
            "https://ss2.baidu.com/-vo3dSag_xI4khGko9WTAnF6hhy/image/h%3D200/sign=ae0e95c0fc1986185e47e8847aec2e69/0b46f21fbe096b63eb314ef108338744ebf8ac62.jpg",
            "https://ss3.baidu.com/9fo3dSag_xI4khGko9WTAnF6hhy/image/h%3D200/sign=1fad2b46952397ddc9799f046983b216/dc54564e9258d109c94bbb13d558ccbf6d814de2.jpg",
            "https://ss1.baidu.com/9vo3dSag_xI4khGko9WTAnF6hhy/image/h%3D200/sign=ff0999f6d4160924c325a51be406359b/86d6277f9e2f070861ccd4a0ed24b899a801f241.jpg"};

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

		//初始化AutoScrollViewPager对象
        mViewPager = (AutoScrollViewPager) findViewById(R.id.viewPager);
        //设置Adapter，这里需要重写loadImage方法，在里面加载图片，这里我使用的是Picasso框架，你可以换成你自己的。
        mViewPager.setAdapter(new BaseViewPagerAdapter<String>(this,initData(),listener) {
            @Override
            public void loadImage(ImageView view, int position, String url) {
                Picasso.with(MainActivity.this).load(url).into(view);
            }
        });


    }

    private List<String> initData() {
        List<String> data = new ArrayList<>();
        Picture picture ;
        for (int i = 0 ; i < paths.length ;i++){
            data.add(paths[i]);
        }
        return data;
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        //记得在销毁的时候调用onDestroy()方法。用来销毁定时器。
        mViewPager.onDestroy();
    }

	//定义点击事件
    private BaseViewPagerAdapter.OnAutoViewPagerItemClickListener listener = new BaseViewPagerAdapter.OnAutoViewPagerItemClickListener<String>() {

        @Override
        public void onItemClick(int position, String url) {
            Toast.makeText(getApplicationContext(),
                    url, Toast.LENGTH_SHORT).show();
        }
    };
}

```

#####主要代码：
* 初始化

	```
	mViewPager = (AutoScrollViewPager) findViewById(R.id.viewPager);
        mViewPager.setAdapter(new BaseViewPagerAdapter<String>(this,initData(),listener) {
            @Override
            public void loadImage(ImageView view, int position, String url) {
                Picasso.with(MainActivity.this).load(url).into(view);
            }
        });
```
* 点击事件
	
	```
	private BaseViewPagerAdapter.OnAutoViewPagerItemClickListener listener = new BaseViewPagerAdapter.OnAutoViewPagerItemClickListener<String>() {

        @Override
        public void onItemClick(int position, String url) {
            Toast.makeText(getApplicationContext(),
                    url, Toast.LENGTH_SHORT).show();
        }
    };
	```


源码地址：[AutoViewPager](https://github.com/fccaikai/AutoScrollViewPager)  欢迎大家start。还在更新中，目前可能还不是很完善。

我的简书：http://www.jianshu.com/users/629c1b27e187/latest_articles
喜欢的可以关注一下。