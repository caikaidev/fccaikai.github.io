---
layout: post
title:  "Android 运行时权限处理"
date:   2017-03-03 09:31:00 +0800
categories: jekyll update
---
### Android 运行时权限处理

#### 描述
从Android 6.0（API级别23）开始，用户开始在应用运行时向其授予权限，而不是在应用安装时授予。此方法可以简化应用安装过程，因为用户在安装或更新应用时不需要授予权限。它还让用户可以对应用的功能进行更多控制；例如，用户可以选择为相机应用提供相机访问权限，而不提供设备位置的访问权限。用户可以随时进入应用的“Settings”屏幕调用权限。具体介绍详见[官网介绍](https://developer.android.com/training/permissions/requesting.html)。

#### 权限类别
系统权限分为了两种类别：正常权限和危险权限。
正常权限：可以只要在```AndroidManifest.xml```中进行配置就可以了；
危险权限：除了在```AndroidManifest.xml```中进行配置，还需要在运行时进行权限的请求，也就是本文所需要讲述的内容。

可以通过以下命令行来查询危险权限   

```
$ adb shell pm list permissions -d -g
```

### 检查权限

当我们需要使用某一个权限时，我们要先检查一下，系统是否已经授予了我们这个权限。比如：当想调用系统相机时，需要去检查一下，是否有打开相机的权限。
使用[ContextCompat.checkSelfPermission()](https://developer.android.com/reference/android/support/v4/content/ContextCompat.html#checkSelfPermission(android.content.Context, java.lang.String))方法来检查是否具有某项权限。

```
int permissionCheck = ContextCompat.checkSelfPermission(thisActivity,
        Manifest.permission.WRITE_CALENDAR);
if(permissionCheck == PackageManager.PERMISSION_GRANTED){
	//具有此权限
}
```

当返回值permissionCheck等于[PackageManager.PERMISSION_GRANTED](https://developer.android.com/reference/android/content/pm/PackageManager.html#PERMISSION_GRANTED)时，表示用户具有此权限，可以继续打开相机；返回值等于[PackageManager. PERMISSION_DENIED](https://developer.android.com/reference/android/content/pm/PackageManager.html#PERMISSION_DENIED)时，表示未授权、必须向用户请求权限。

### 请求权限

通过[ActivityCompat.requestPermissions()](https://developer.android.com/reference/android/support/v4/app/ActivityCompat.html#requestPermissions(android.app.Activity, java.lang.String[], int))方法请求你所需要的权限。
此方法需要传入三个参数：

```
void requestPermissions (Activity activity, 
                String[] permissions, 
                int requestCode)
```
+ 当前Activity实例
+ 权限组（可以同时请求多个权限）
+ 请求码（回调的时候需要用到）

![请求权限](https://ww4.sinaimg.cn/large/006tNc79gy1fcvwtij8p4j30a00hsaa2.jpg)

```
// 先检查权限
if (ContextCompat.checkSelfPermission(thisActivity,
                Manifest.permission.READ_CONTACTS)
        != PackageManager.PERMISSION_GRANTED) {

    // 解释一下为什么需要此权限，稍后会讲到这个方法
    if (ActivityCompat.shouldShowRequestPermissionRationale(thisActivity,
            Manifest.permission.CAMETA)) {
				//可以做一个弹出框，解释一下为什么需要此权限
        

    } else {

        //请求权限
        ActivityCompat.requestPermissions(thisActivity,
                new String[]{Manifest.permission.CAMETA},
                MY_PERMISSIONS_REQUEST_READ_CONTACTS);

    }
}else{
		//已有权限、直接进行操作
}

```

### 处理权限请求相应
用户处理权限之后，会回调到```  onRequestPermissionsResult() ```方法。

```
@Override
public void onRequestPermissionsResult(int requestCode,
        String permissions[], int[] grantResults) {
    switch (requestCode) {
        case MY_PERMISSIONS_REQUEST_READ_CONTACTS: {
            // 如果请求被取消、grantResults 是空的，这里需要做一个判断是否大于0
            if (grantResults.length > 0
                && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
								//这里我们只请求了Camera权限，所以只要取grantResults[0]来判断是否授权
								//TODO 打开相机
            } else {

            		//用户拒绝授权
            }
            return;
        }

    }
}

```

### 解释为什么需要权限

当用户拒绝一次之后，```ActivityCompat.shouldShowRequestPermissionRationale```方法会返回```true```。我们就可以给用户再次做一个解释，为什么需要这个权限。

#### 备注：当用户点击不在提醒此权限授权后，返回值也是false。不再提醒之后，请求权限是直接返回授权失败的。

### 结束
运行时的权限处理到这里也差不多了，内容不是很多，Android 6.0+的占有率也越来越高了，也需要对自己的app做一下适配了。使用的时候可以进行一层封装，这样的话会更加的简单便捷。
  
这个是我自己做的一个权限请求库：[AndroidPermissionX](https://github.com/fccaikai/AndroidPermissionX)

欢迎大家给出建议，觉得好的话请给一个star。