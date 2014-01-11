---
author: andrew
comments: true
date: 2012-09-06 15:21:43+00:00
layout: post
slug: caltophone
title: Как сделать телефонный звонок в Android
wordpress_id: 579
categories:
- Android для начинающих
- Программирование
---

Добрый день друзья.





Сегодня я покажу вам как можно звонить из своего приложения.





Прошу к прочтению.


 <!-- more -->


Создадим activity_main.xml:


	<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"

    xmlns:tools="http://schemas.android.com/tools"

    android:layout_width="match_parent"

    android:layout_height="match_parent" >



    <EditText

        android:id="@+id/editText1"

        android:layout_width="wrap_content"

        android:layout_height="wrap_content"

        android:layout_alignParentLeft="true"

        android:ems="10"

        android:inputType="numberSigned" >



        <requestFocus />

    </EditText>



    <Button

        android:id="@+id/button1"

        android:layout_width="wrap_content"

        android:layout_height="wrap_content"

        android:layout_alignParentTop="true"

        android:layout_toRightOf="@+id/editText1"

        android:text="Звонок" />



	</RelativeLayout>








Создаем MainActivity.java:


	package com.example.call_to_phone;



	import android.app.Activity;

	import android.content.Intent;

	import android.net.Uri;

	import android.os.Bundle;

	import android.view.Menu;

	import android.view.View;

	import android.view.View.OnClickListener;

	import android.widget.Button;

	import android.widget.EditText;



	public class MainActivity extends Activity implements OnClickListener {



	private EditText callNumber;



	@Override

	public void onCreate(Bundle savedInstanceState) {

		super.onCreate(savedInstanceState);

		setContentView(R.layout.activity_main);

		callNumber = (EditText) findViewById(R.id.editText1);

		Button callButton = (Button) findViewById(R.id.button1);

		callButton.setOnClickListener(this);



	}



	@Override

	public boolean onCreateOptionsMenu(Menu menu) {

		getMenuInflater().inflate(R.menu.activity_main, menu);

		return true;

	}



	@Override

	public void onClick(View arg0) {

		if (callNumber != null) {

			String number = String.format("tel:%s", callNumber.getText().toString());

			startActivity(new Intent(Intent.ACTION_CALL, Uri.parse(number)));

		}

	}

	}








Как вы видите при нажатии на кнопку мы сразу начинаем звонок.





Для вызова нужно писать:



	String number = String.format("tel:%s", callNumber.getText().toString());

	startActivity(new Intent(Intent.ACTION_CALL, Uri.parse(number)));







Как видите все очень быстро из без проблем.





Прошу оценить статью и дать пару комментариев.





Коды вы можете скачать [тут](http://android-helper.com.ua/forms)
