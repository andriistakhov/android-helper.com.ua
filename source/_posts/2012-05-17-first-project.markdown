---
author: andrew
comments: true
date: 2012-05-17 21:08:54+00:00
layout: post
slug: '%d1%81%d0%be%d0%b7%d0%b4%d0%b0%d0%bd%d0%b8%d0%b5-%d0%bf%d0%b5%d1%80%d0%b2%d0%be%d0%b3%d0%be-%d0%bf%d1%80%d0%be%d0%b5%d0%ba%d1%82%d0%b0-%d0%bf%d1%80%d0%b8%d0%b2%d0%b5%d1%82-android'
title: Создание первого проекта - Привет Android
wordpress_id: 256
categories:
- Android для начинающих
- Программирование
---

Добрый день уважаемые читатели.





Сегодня мы с вами напишем первое приложение для Android.


<!-- more -->


1. Первое что нужно сделать, это выкачать последний Android SDK.





У себя я использую версию 2.2 и 4.0.3. (Скачать вы их можете с SDK Android Manager)





![](http://android-helper.com.ua/images/uploads/2012/05/AndroidSDK.jpg)





2. Указать в Eclipse где у вас android sdk





Window - Preference





![](http://android-helper.com.ua/images/uploads/2012/05/android1.jpg)





 





После того как все это сделано, мы можем приступить к созданию своего первого приложения.





New - Android Project.





Дальше все по скринам.





![](http://android-helper.com.ua/images/uploads/2012/05/android2.jpg)




![](http://android-helper.com.ua/images/uploads/2012/05/android3.jpg)




![](http://android-helper.com.ua/images/uploads/2012/05/android4.jpg)





После нажатия на Finish, у вас должно получиться следующее дерево вашего проекта:





![](http://android-helper.com.ua/images/uploads/2012/05/android5.jpg)



Открыв файл Example1Activity.java вы увидите следующий код.






	package com.in.androidhelper;



	import android.app.Activity;

	import android.os.Bundle;



	public class Example1Activity extends Activity {

 	/** Called when the activity is first created. */

	@Override

	public void onCreate(Bundle savedInstanceState) {

    	super.onCreate(savedInstanceState);

    	setContentView(R.layout.main);</p>

   	}

	}






Но у нас с вами задача написать "Привет Android".  
Для этого откройте файл main.xml  
И измените текст текстового поля.  
Должно получиться так:






	<?xml version="1.0" encoding="utf-8"?>

	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

		android:layout_width="fill_parent"

		android:layout_height="fill_parent"

		android:orientation="vertical" >



		<TextView

			android:layout_width="fill_parent"

			android:layout_height="wrap_content"

			android:text="Привет Android" />



	</LinearLayout>







Первое задание мы с вами успешно прошли.





Если у вас есть вопросы вы всегда можете задать их на сайте либо в соц. сетях.





Подписывайтесь на рассылку и получайте только самое свежее.





Планируется проведение тренинга по Android. Если вы хотите повысить свои знания тогда вам к нам :).





Все вопросы задавайте в соц. сетях.




