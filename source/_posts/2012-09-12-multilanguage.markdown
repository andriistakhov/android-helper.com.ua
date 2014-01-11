---
author: andrew
comments: false
date: 2012-09-12 09:12:10+00:00
layout: post
slug: multilanguage
title: Мультиязычное приложение на android
wordpress_id: 605
categories:
- Android для начинающих
- Android для профессионалов
- Программирование
---

Добрый день друзья.





Очень много программистов сейчас сталкиваются с много язычными приложениями.





Я решил поделится опытом работы с такими приложениями.


<!-- more -->


Есть 2 подхода разработки таких приложений.








  1. Язык приложения зависит от языка системы



  2. Возможность менять язык в самом приложении






Ну что приступим.





### Язык приложения зависит от языка системы





В android для реализации данного подхода, используется следующая структура вложения папок.





![](http://android-helper.com.ua/images/uploads/2012/09/m1.png)





Тут все просто, создаем папку с расширением ru (Для России), fr - Франции и так далее.





Больше информации вы можете получить [тут](http://developer.android.com/intl/ru/training/basics/supporting-devices/languages.html).







strings.xml - с папки values





	<resources>



    <string name="app_name">multilanguage</string>

    <string name="hello_world">Hello world!</string>

    <string name="menu_settings">Settings</string>

    <string name="title_activity_main">MainActivity</string>



	</resources>










strings.xml - с папки values-ru









	<resources>



    <string name="app_name">multilanguage</string>

    <string name="hello_world">Привет Мир!</string>

    <string name="menu_settings">Настройки</string>

    <string name="title_activity_main">Главное активити</string>



	</resources>







### Возможность менять язык в самом приложении





Вот тут более интересней тема.





Для того что бы приложение на лету поменяло язык нужно сделать следующее.





Создаем файл который наследуется от Application.





	package com.example.multilanguage;



	import java.util.Locale;



	import android.app.Application;

	import android.content.res.Configuration;



	public class MyApp extends Application {



	private Locale locale = null;



	@Override

	public void onConfigurationChanged(Configuration newConfig) {

		super.onConfigurationChanged(newConfig);

		if (locale != null) {

			newConfig.locale = locale;

			Locale.setDefault(locale);

			getBaseContext().getResources().updateConfiguration(newConfig,

					getBaseContext().getResources().getDisplayMetrics());

		}

	}



	@Override

	public void onCreate() {

		super.onCreate();



		Configuration config = getBaseContext().getResources().getConfiguration();



		String lang = "ru";

		if (!"".equals(lang) && !config.locale.getLanguage().equals(lang)) {

			locale = new Locale(lang);

			Locale.setDefault(locale);

			config.locale = locale;

			getBaseContext().getResources().updateConfiguration(config,

					getBaseContext().getResources().getDisplayMetrics());

		}

	}



	}





Вот и все.





Теперь если вам нужно изменить язык при нажатии на кнопку вызываете просто:






	Configuration config = getBaseContext().getResources().getConfiguration();



		String lang = "ru";

		if (!"".equals(lang) && !config.locale.getLanguage().equals(lang)) {

			locale = new Locale(lang);

			Locale.setDefault(locale);

			config.locale = locale;

			getBaseContext().getResources().updateConfiguration(config,

					getBaseContext().getResources().getDisplayMetrics());

		}







Если вам понравилась статья пишите комментарии и рассказывайте друзьям.





Коды вы можете скачать [тут](http://android-helper.com.ua/forms)
