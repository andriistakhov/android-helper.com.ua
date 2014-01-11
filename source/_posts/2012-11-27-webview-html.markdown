---
author: andrew
comments: true
date: 2012-11-27 21:49:18+00:00
layout: post
slug: webview-html
title: WebView - html страницы в android
wordpress_id: 960
categories:
- Android для начинающих
- Android для профессионалов
- Программирование
---

Всем привет.





К одной статье мне прислали комментарий.





Благодаря ему, я написал данную статью.





Как видно из названия, то речь пойдет о **WebView**


 <!-- more -->


Кода много не будет, так как вы всегда можете скачать готовый проект на нашей странице с кодами.





Рассмотрим два варианта загрузки html страницы в WebView








  1. **Локальный файл**



  2. **Веб-страница (на примере http://android-helper.com.ua/)**






#### Локальный файл





		WebView mWebView = (WebView) myView.findViewById(R.id.web);



		WebSettings settings = mWebView.getSettings();

		settings.setJavaScriptEnabled(true);

		settings.setDefaultTextEncodingName("utf-8");

		settings.setDefaultZoom(WebSettings.ZoomDensity.MEDIUM);



		mWebView.loadUrl("file:///android_asset/www/index.html");





#### Веб-страница (на примере http://android-helper.com.ua/)






		WebView mWebView = (WebView) myView.findViewById(R.id.web);



		WebSettings settings = mWebView.getSettings();

		settings.setJavaScriptEnabled(true);

		settings.setDefaultTextEncodingName("utf-8");

		settings.setDefaultZoom(WebSettings.ZoomDensity.MEDIUM);

		mWebView.loadUrl("http://android-helper.com.ua");







Напомню, что скачать полный пример вы можете на странице с [кодами](http://android-helper.com.ua/codes/).
