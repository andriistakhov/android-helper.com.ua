---
author: andrew
comments: true
date: 2012-05-29 07:55:09+00:00
layout: post
slug: scrollview-%d0%b8-%d0%bc%d0%bd%d0%be%d0%b3%d0%be-%d1%80%d0%b0%d0%b7%d0%bd%d1%8b%d1%85-%d0%b5%d0%bb%d0%b5%d0%bc%d0%b5%d0%bd%d1%82%d0%be%d0%b2
title: ScrollView и много разных елементов
wordpress_id: 297
categories:
- Android для начинающих
- Программирование
---

Сегодня мы продолжим цикл изучения ScrollView.





Из прошлых уроков вы узнали, что в ScrollView можно  добавить текст.





Сегодня же мы добавим кнопки :).


<!-- more -->


main.xml:








    <?xml version="1.0" encoding="utf-8"?>

    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

        android:id="@+id/LinearLayout01"

        android:layout_width="fill_parent"

        android:layout_height="fill_parent" >



        <ScrollView

            android:id="@+id/ScrollView01"

            android:layout_width="fill_parent"

            android:layout_height="110px" >



            <LinearLayout

                android:id="@+id/LinearLayout02"

                android:layout_width="wrap_content"

                android:layout_height="30px"

                android:orientation="vertical" >



                <Button

                    android:id="@+id/Button01"

                    android:layout_width="wrap_content"

                    android:layout_height="wrap_content"

                    android:text="1" />



                <Button

                    android:id="@+id/Button02"

                    android:layout_width="wrap_content"

                    android:layout_height="wrap_content"

                    android:text="2" />



                <Button

                    android:id="@+id/Button03"

                    android:layout_width="wrap_content"

                    android:layout_height="wrap_content"

                    android:text="3" />

            </LinearLayout>

        </ScrollView>



    </LinearLayout>








Результат:





 [![](http://android-helper.com.ua/images/uploads/2012/05/device-2012-05-29-105334.png)](http://android-helper.com.ua/images/uploads/2012/05/device-2012-05-29-105334.png)
