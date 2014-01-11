---
author: andrew
comments: true
date: 2013-11-16 16:03:39+00:00
layout: post
slug: ganymotion-root
footer: true
title: Genymotion root - Как получить права рут на емуляторе
wordpress_id: 1715
categories:
- Памятка разработчику
- Программирование
---

### Как зарутить ganymotion?

Всем привет.

Один из участников моего коучинга, задал мне [вопрос на форуме](http://forum.android-helper.com.ua/viewtopic.php?f=15&t=9). Один из ответов был использовать Ganymotion в его разработке. На что он ответил, что не может добраться к базе данных.

Я реально проверил это. И знаете, что оно так и есть. Было принято решение получить доступ к базе любой ценой. И это свершилось.

Сегодня я хотел бы поделится своим опытом - **"Как получить доступ к базе данных в Ganymotion?"**

Готовы? Тогда вперед.

<!-- more -->

#### Подготовка

1. [Установка Ganymotion](http://android-helper.com.ua/android-studio-genymotion/)
2. Выбираем из списка нужный емулятор
3. [Скачиваем root файл](https://drive.google.com/file/d/0B-p1r5SNN4adTGgyajV6ckNtV00/edit?usp=sharing).
4. Открываем **terminal** - Mac (**cmd** - Windows)

#### Root

Заходим в папку где у вас скачан файл (см. выше)

И в terminal пишем следующее!

	adb devices
	adb root
	adb connect <IP вашего устройства см. adb devices вместе с портом>
	adb remount
	adb push su /system/xbin/
	adb push su /system/bin
	adb shell chmod 6755 /system/bin/su
	adb shell chmod 6755 /system/xbin/su

Все ваш емулятор теперь имеет права root.

#### Видео урок

{% youtube rsBGF9_Un8o %}


1. Ставьте лайк
2. Пишите комментарии
3. Рассказывайте друзьям