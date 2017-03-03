---
layout: post
title:  "Realm for Android"
date:   2017-03-03 09:53:00 +0800
categories: jekyll update
---
##简介

realm是一个跨平台移动数据库引擎，支持iOS、OS
X（Objective-C和Swift）、Android以及React Native。根据官网的介绍来说，是比Sqlite存储更加快读、高效。现在来的介绍一下realm环境的配置以及简单的使用。

官网：[realm](https://realm.io)
github:[realm-java](https://github.com/realm/realm-java)
官方教程：[realm docs](https://realm.io/docs/java/latest/)

##Realm使用
###集成
* 一
	
	在Project目录下的```build.gradle```中做一下修改：
	```
	buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.2.1'
        classpath "io.realm:realm-gradle-plugin:2.2.0"
        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}
	```

----

* 二

    在module目录下的```build.gradle```中添加：
    ```
    apply plugin: 'realm-android'
    ```
    
    ----
    
###使用

* 初始化
    
    在```Application```中进行初始化
    
    ```
public class App extends Application {
        @Override
        public void onCreate() {
            super.onCreate();
            initRealm();
        }
    
        private void initRealm() {
            Realm.init(this);
        }
}
    ```
    注：不要忘记在```manifest```中注册```Application```

-----

* 创建实体类

    创建一个User实体类，需要```extedns RealmObject```，

    ```
    public class User extends RealmObject {
        private String name;
        private int age;
        //get set方法省略
    }
    
    ```
    
    或者 ```implements RealmModel```同时加上注解```@RealmClass```。
    
    ```
    @RealmClass
public class User implements RealmModel {
        private String name;
        private int age;
        //get set方法省略
}
    ```
* 插入数据


   ```
   Realm realm = Realm.getDefaultInstance();
        /***写法一***/
        realm.executeTransaction(new Realm.Transaction() {
            @Override
            public void execute(Realm realm) {
                User user = realm.createObject(User.class);
                user.setName(name);
                user.setAge(Integer.parseInt(age));

                //也可以使用下面的方法
                /*
                User user = new User();
                user.setName(name);
                user.setAge(Integer.parseInt(age));
                realm.copyToRealm(user);
                */
            }
        });


        /* 写法二
        realm.executeTransactionAsync(new Realm.Transaction() {
            @Override
            public void execute(Realm realm) {
                User user = realm.createObject(User.class);
                user.setName(name);
                user.setAge(Integer.parseInt(age));
            }
        }, new Realm.Transaction.OnSuccess() {
            @Override
            public void onSuccess() {
                //成功回调
            }
        });*/

        //写法三
        /*realm.beginTransaction();
        User user = realm.createObject(User.class);
        user.setName(name);
        user.setAge(Integer.parseInt(age));
        realm.commitTransaction();*/
   ```
* 更新数据
    
    这里是先根据name去查询到对应的记录，然后将所有记录的age修改为10。
    ```.where(Class<E> clazz)```表示要查询哪个类的数据。
    ```equalTo(String fieldName, String value)```表示查询```fieldName```值为```value```的记录。

    ```findAll()```  表示返回符合该条件的所有记录。返回类型为```RealmResults<E>```.但是我们也可以直接用```List<?>```接收。

    ```

        Realm realm = Realm.getDefaultInstance();
        final User user = new User();
        user.setName(name);
        user.setAge(Integer.parseInt(age));

        realm.executeTransaction(new Realm.Transaction() {
            @Override
            public void execute(Realm realm) {
                List<User> data = realm.where(User.class).equalTo("name",name).findAll();
                if (data.size() == 0){
                    print("未查询到对应的记录");
                    return;
                }

                //将所有姓名为{name}的年龄修改为10
                for (User user : data) {
                    user.setAge(10);
                }

                print("更新完成");
            }
        });
    ```
* 查询数据
  这里只展示了查询所有数据，根据条件查询数据可以参考刚才的更新数据。
    
    ```
Realm realm = Realm.getDefaultInstance();
 RealmQuery<User> query =  realm.where(User.class);
List<User> data =  query.findAll();
    ```
* 删除数据

    ```
    Realm realm = Realm.getDefaultInstance();
        realm.executeTransaction(new Realm.Transaction() {
            @Override
            public void execute(Realm realm) {
                RealmResults<User> results = realm.where(User.class).findAll();
                if (results.size() == 0) {
                    print("无数据");
                    return;
                }
                //删除第一条数据
                results.deleteFirstFromRealm();
                print("删除成功");
                //删除最后一条数据
                //results.deleteLastFromRealm();
            }
        });
    ```

源码地址:[RealmExample](https://github.com/fccaikai/RealmExample.git)