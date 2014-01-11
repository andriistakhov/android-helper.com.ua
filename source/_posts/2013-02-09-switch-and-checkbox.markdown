---
author: andrew
comments: true
date: 2013-02-09 05:47:19+00:00
layout: post
slug: switch-and-checkbox
title: Switch и CheckBox для разных версий
wordpress_id: 1309
categories:
- Android для начинающих
- Android для профессионалов
- Программирование
---

Всем привет друзья.





Сегодня я подготовил для вас видео урок.





Как использовать Switch елемент для android 4.0+, а для версий ниже использовать CheckBox.

 <!-- more -->

{% youtube Ifs96nSYUlg %}






**Коды**





**/layout-v14/switch_for_include.xml**



	<?xml version="1.0" encoding="utf-8"?>

	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

    android:layout_width="match_parent"

    android:layout_height="match_parent"

    android:orientation="horizontal" >



    <Switch

        android:id="@+id/checkBox1"

        android:layout_width="fill_parent"

        android:layout_height="wrap_content"

        android:text="Android-helper.com.ua" />



	</LinearLayout>


**/layout/switch_for_include.xml**



	<?xml version="1.0" encoding="utf-8"?>

	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

    android:layout_width="match_parent"

    android:layout_height="match_parent"

    android:orientation="horizontal" >



    <TextView

        android:layout_width="fill_parent"

        android:layout_height="wrap_content"

        android:layout_weight="1"

        android:text="Android-helper.com.ua" />



    <CheckBox

        android:id="@+id/checkBox1"

        android:layout_width="wrap_content"

        android:layout_height="wrap_content"

        android:layout_weight="0" />



	</LinearLayout>


**/layout/activity_main.xml**


	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

    xmlns:tools="http://schemas.android.com/tools"

    android:layout_width="match_parent"

    android:layout_height="match_parent"

    android:orientation="vertical"

    tools:context=".MainActivity" >



    <TextView

        android:layout_width="wrap_content"

        android:layout_height="wrap_content"

        android:gravity="center"

        android:paddingBottom="30dp"

        android:text="Пример того как использовать Swich на разных устройствах" />



    <include layout="@layout/switch_for_include" />



	</LinearLayout>




**MainActivity.java**



	public class MainActivity extends Activity implements OnCheckedChangeListener {



    @Override

    protected void onCreate(Bundle savedInstanceState) {

        super.onCreate(savedInstanceState);

        setContentView(R.layout.activity_main);

        CompoundButton compoundButton = (CompoundButton) findViewById(R.id.checkBox1);

        compoundButton.setOnCheckedChangeListener(this);

    }



    @Override

    public void onCheckedChanged(CompoundButton arg0, boolean arg1) {

        Toast.makeText(this, "Все работает сейчас у нас " + arg1, Toast.LENGTH_LONG).show();

    }



	}


Коды программы вы можете скачать [тут](http://android-helper.com.ua/codes/)




