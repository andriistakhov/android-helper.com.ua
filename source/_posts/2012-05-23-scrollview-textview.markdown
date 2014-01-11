---
author: andrew
comments: true
date: 2012-05-23 15:26:18+00:00
layout: post
slug: scrollview-%d0%b4%d0%b2%d0%b8%d0%b3%d0%b0%d0%b5%d0%bc-textview
title: ScrollView двигаем TextView
wordpress_id: 287
categories:
- Android для начинающих
- Программирование
---

Добрый день друзья.





Сегодня мы поговорим о таком android елементе как ScrollView.





И не только поговорим, я вам покажу как можно большой текст двигать в маленьком окне.


<!-- more -->


Откроем файл main.xml





И заменим:








	<TextView

        android:layout_width="fill_parent"

        android:layout_height="wrap_content"

        android:text="Привет Android" />









на:








	<ScrollView

        android:id="@+id/ScrollView01"

        android:layout_width="fill_parent"

        android:layout_height="70px" >



        <TextView

            android:id="@+id/TEXT_VIEW"

            android:layout_width="wrap_content"

            android:layout_height="wrap_content"

            android:text="Второе задание мы с вами успешно прошли.

	Если у вас есть вопросы вы всегда можете задать их на сайте либо в соц. сетях.

	Подписывайтесь на рассылку и получайте только самое свежее.

	Планируется проведение тренинга по Android. Если вы хотите повысить свои знания тогда вам к нам  .

	Все вопросы задавайте в соц. сетях."

            android:textSize="17sp" />

    </ScrollView>









И вот что у нас получилось:





![](http://android-helper.com.ua/images/uploads/2012/05/device-2012-05-23-182038.png)
![](http://android-helper.com.ua/images/uploads/2012/05/device-2012-05-23-182049.png)
