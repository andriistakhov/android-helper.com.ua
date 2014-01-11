---
author: andrew
comments: true
date: 2013-01-28 16:03:57+00:00
layout: post
slug: slidingmenu
title: SlidingMenu - или новое поколение dashboard
wordpress_id: 1218
categories:
- Android для начинающих
- Android для профессионалов
- Памятка разработчику
- Программирование
---

Всем привет.





Есть много программ с которых программисты и дизайнеры берут свои идеи.





И вот одна из которых это приложение facebook для android.





С этой программы дизайнеры взяли такую вещь как sliding menu.





Давайте посмотрим, что это такое.

 <!-- more -->



![Screenshot_2013-01-17-19-47-11](http://android-helper.com.ua/images/uploads/2013/01/Screenshot_2013-01-17-19-47-11.png)




 





![Screenshot_2013-01-02-02-24-31](http://android-helper.com.ua/images/uploads/2013/01/Screenshot_2013-01-02-02-24-31.png)





 





### Задача





Сделать что то подобное.





### Реализация





Для реализации данной задачи, мы будем использовать открытую библиотеку [SlidingMenu](https://github.com/jfeinstein10/SlidingMenu).





Также как она работает можете посмотреть, скачав приложение с [маркета](https://play.google.com/store/apps/details?id=com.slidingmenu.example).





### Коды





**MainActivity.java**



	public class MainActivity extends Activity {



    @Override

    protected void onCreate(Bundle savedInstanceState) {

        super.onCreate(savedInstanceState);

        setContentView(R.layout.activity_main);



        setTitle("Attach");

        // configure the SlidingMenu

        SlidingMenu menu = new SlidingMenu(this);

        menu.setMode(SlidingMenu.LEFT);

        menu.setTouchModeAbove(SlidingMenu.TOUCHMODE_FULLSCREEN);

        menu.setShadowWidthRes(R.dimen.shadow_width);

        menu.setShadowDrawable(R.drawable.shadow);

        menu.setBehindOffsetRes(R.dimen.slidingmenu_offset);

        menu.setFadeDegree(0.35f);



        // ///////////

        menu.attachToActivity(this, SlidingMenu.SLIDING_CONTENT);

        // //////////



        menu.setMenu(R.layout.menu);

    }



    @Override

    public boolean onCreateOptionsMenu(Menu menu) {

        // Inflate the menu; this adds items to the action bar if it is present.

        getMenuInflater().inflate(R.menu.activity_main, menu);

        return true;

    }



	}


**drawable/shadow.xml**


	<?xml version="1.0" encoding="utf-8"?>

	<shape xmlns:android="http://schemas.android.com/apk/res/android" >



    <gradient

        android:centerColor="#11000000"

        android:endColor="#33000000"

        android:startColor="#00000000" />



	</shape>


**layout/menu.xml**


	<?xml version="1.0" encoding="utf-8"?>

	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

    android:layout_width="match_parent"

    android:layout_height="match_parent"

    android:orientation="vertical" >



    <TextView

        android:id="@+id/textView1"

        android:layout_width="wrap_content"

        android:layout_height="wrap_content"

        android:text="Menu 1"

        android:textAppearance="?android:attr/textAppearanceLarge" />



    <TextView

        android:id="@+id/textView2"

        android:layout_width="wrap_content"

        android:layout_height="wrap_content"

        android:text="Menu 2"

        android:textAppearance="?android:attr/textAppearanceLarge" />



    <TextView

        android:id="@+id/textView3"

        android:layout_width="wrap_content"

        android:layout_height="wrap_content"

        android:text="Menu 3"

        android:textAppearance="?android:attr/textAppearanceLarge" />



    <TextView

        android:id="@+id/textView4"

        android:layout_width="wrap_content"

        android:layout_height="wrap_content"

        android:text="Menu 4"

        android:textAppearance="?android:attr/textAppearanceLarge" />



	</LinearLayout>


**values/dimens.xml**



	<?xml version="1.0" encoding="utf-8"?>

	<resources>



    <dimen name="slidingmenu_offset">60dp</dimen>

    <dimen name="list_padding">10dp</dimen>

    <dimen name="shadow_width">15dp</dimen>



	</resources>


### Видео


{% youtube eHWNIS64I0Q %}






Рабочий код на странице с [кодами](http://android-helper.com.ua/codes/).
