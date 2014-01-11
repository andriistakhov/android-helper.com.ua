---
author: andrew
comments: true
date: 2012-09-17 15:05:34+00:00
layout: post
slug: autocompletetextview-%d0%b0%d0%b2%d1%82%d0%be-%d0%b7%d0%b0%d0%bf%d0%be%d0%bb%d0%bd%d0%b5%d0%bd%d0%b8%d0%b5-%d0%b2-edittext
title: AutoCompleteTextView - авто заполнение в edittext
wordpress_id: 616
categories:
- Android для начинающих
- Программирование
---

Добрый день друзья.





Сегодня я вам расскажу о таком android элементе как AutoCompleteTextView.





Для чего же нужен этот элемент. А нужен он для того что бы во время ввода выдавался соответствующий список возможных вариантов.





Давайте теперь рассмотрим как с этим работать.


<!-- more -->


К примеру нам нужно автозаполнение для списка месяцев года.





![](http://android-helper.com.ua/images/uploads/2012/09/ExAutoCompleteTextView.png)





Для этого подготовим нужный нам массив строк (/res/values/myvalues.xml):






	<?xml version="1.0" encoding="UTF-8"?>

	<resources>

	<array name="month">

       <item>January</item>

       <item>February</item>

       <item>March</item>

       <item>April</item>

       <item>May</item>

       <item>June</item>

       <item>July</item>

       <item>August</item>

       <item>September</item>

       <item>October</item>

       <item>November</item>

       <item>December</item>

	</array>

	</resources>







В главной активити напишем следуюющее:



	import android.app.Activity;

	import android.os.Bundle;

	import android.text.Editable;

	import android.text.TextWatcher;

	import android.widget.ArrayAdapter;

	import android.widget.AutoCompleteTextView;

 

	public class ExAutoCompleteTextView extends Activity implements TextWatcher {

  

 	AutoCompleteTextView autoCompleteTextView;

  

   	/** Called when the activity is first created. */

   	@Override

   	public void onCreate(Bundle savedInstanceState) {

       super.onCreate(savedInstanceState);

       setContentView(R.layout.main); 

       autoCompleteTextView = (AutoCompleteTextView)findViewById(R.id.input);

       String[] month = getResources().getStringArray(R.array.month);

       autoCompleteTextView.addTextChangedListener(this); 

       autoCompleteTextView.setAdapter(new ArrayAdapter<String>(this, android.R.layout.simple_dropdown_item_1line, month)); 

   	}

 

 		@Override

 		public void afterTextChanged(Editable arg0) {

  	// TODO Auto-generated method stub

   

 		}

 

 		@Override

 		public void beforeTextChanged(CharSequence arg0, int arg1, int arg2, int arg3) {

  	// TODO Auto-generated method stub

   

 		}

 

		@Override

 		public void onTextChanged(CharSequence arg0, int arg1, int arg2, int arg3) {

  	// TODO Auto-generated method stub

   

 		}

	}








Ну и сама раскладка main.xml:



	<?xml version="1.0" encoding="utf-8"?>

	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

   	android:orientation="vertical"

   	android:layout_width="fill_parent"

   	android:layout_height="fill_parent"

   	>

	<TextView

   	android:layout_width="fill_parent"

   	android:layout_height="wrap_content"

   	android:text="@string/hello"

   	/>

	<AutoCompleteTextView

   	android:id="@+id/input"

   	android:layout_width="fill_parent"

   	android:layout_height="wrap_content"

   	android:completionThreshold="1"

   	/>

	</LinearLayout>








Вот и все.





Жду ваших комментариев и прошу подписаться на моем блоге.
