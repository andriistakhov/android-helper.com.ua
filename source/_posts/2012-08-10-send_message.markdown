---
author: andrew
comments: true
date: 2012-08-10 08:01:42+00:00
layout: post
slug: send_message
title: Отправка сообщения с приложения
wordpress_id: 416
categories:
- Android для начинающих
- Программирование
---

Добрый день всем.





В предыдущем посте я писал о возможности отправки писем с вашего приложения.





Сегодня я бы хотел написать, как же можно отправлять SMS с вашего приложения.


 <!-- more -->


Вот код:


	Intent smsIntent = new Intent(Intent.ACTION_VIEW);

	smsIntent.setType("vnd.android-dir/mms-sms");

	smsIntent.putExtra("address", "12125551212,121212121212"); // C версии 2.2 можно отправлять множеству получателей

	smsIntent.putExtra("sms_body","Body of Message");

	startActivity(smsIntent);



