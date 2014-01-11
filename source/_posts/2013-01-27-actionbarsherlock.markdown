---
author: andrew
comments: true
date: 2013-01-27 14:47:36+00:00
layout: post
slug: actionbarsherlock
title: ActionBarSherlock - или использование ActionBar на всех устройсвах
wordpress_id: 1190
categories:
- Android для начинающих
- Android для профессионалов
- Памятка разработчику
- Программирование
---

Привет друзья.





После выхода android 4. Все дизайнеры как будто сговорились и прикручивают actionbar кругом.





И заказчик говорит, что именно так должно быть и для старых версий.





И что же делать? Писать свой actionbar?


 <!-- more -->


Нам на помощь приходит очень классная и бесплатная библиотека [ActionBarSherlock](http://actionbarsherlock.com/)





Я думаю, что большая часть из вас будет ее использовать.





Но могут быть и такие которые скажут, мы не используем сторонние библиотеки.





Это вам решать, но от себя добавлю, что эту библиотеку рекомендует google.





Пришло время писать код.





### Задача





Сделать actionbar меню для всех телефонов.





### Ресурсы





ActionBarSherlock





### Примеры реализации





![feature_02](http://android-helper.com.ua/images/uploads/2013/01/feature_02.png)





 





![feature_03](http://android-helper.com.ua/images/uploads/2013/01/feature_03.png)



 





![feature_01](http://android-helper.com.ua/images/uploads/2013/01/feature_01.png)





 





 





### Реализация








  1. Подключаем проект Sherlock в eclipse. Скачать можно [тут](https://api.github.com/repos/JakeWharton/ActionBarSherlock/zipball/4.2.0).



  2. Создаем свой проект



  3. Подключаем библиотеку sherlock к вашему проекту



  4. Делаем файл меню



  5. ВАЖНО! Все активити и фрагменты должны наследоваться от SherlockActivity и SherlockFragment






### Коды





#### menu/activity_main.xml





	<menu xmlns:android="http://schemas.android.com/apk/res/android" >



    <item

        android:id="@+id/item1"

        android:icon="@android:drawable/ic_menu_agenda"

        android:orderInCategory="100"

        android:showAsAction="always|withText"

        android:title="Item 1"/>

    <item

        android:id="@+id/item2"

        android:icon="@android:drawable/ic_menu_call"

        android:orderInCategory="200"

        android:showAsAction="always|withText"

        android:title="Item 2"/>

    <item

        android:id="@+id/item3"

        android:icon="@android:drawable/ic_menu_camera"

        android:orderInCategory="300"

        android:showAsAction="ifRoom|withText"

        android:title="Item3"/>

    <item

        android:id="@+id/menu_settings"

        android:orderInCategory="400"

        android:showAsAction="never"

        android:title="@string/menu_settings"/>



	</menu>





#### MainActivity.java



	package com.ua.android_helper.testactionbar;



	import android.os.Bundle;

	import com.actionbarsherlock.app.SherlockActivity;

	import com.actionbarsherlock.view.Menu;



	public class MainActivity extends SherlockActivity {



    @Override

    protected void onCreate(Bundle savedInstanceState) {

        super.onCreate(savedInstanceState);

        setContentView(R.layout.activity_main);

    }



    @Override

    public boolean onCreateOptionsMenu(Menu menu) {

        getSupportMenuInflater().inflate(R.menu.activity_main, menu);

        return true;

    }



	}


#### AndroidManifest.xml


	<?xml version="1.0" encoding="utf-8"?>

	<manifest xmlns:android="http://schemas.android.com/apk/res/android"

    package="com.ua.android_helper.testactionbar"

    android:versionCode="1"

    android:versionName="1.0" >



    <uses-sdk

        android:minSdkVersion="8"

        android:targetSdkVersion="17" />



    <application

        android:allowBackup="true"

        android:icon="@drawable/ic_launcher"

        android:label="@string/app_name"

        android:theme="@style/Theme.Sherlock" >

        <activity

            android:name="com.ua.android_helper.testactionbar.MainActivity"

            android:label="@string/app_name" >

            <intent-filter>

                <action android:name="android.intent.action.MAIN" />



                <category android:name="android.intent.category.LAUNCHER" />

            </intent-filter>

        </activity>

    </application>



	</manifest>



**Видео**

{% youtube GOB72vWY_5s %}


