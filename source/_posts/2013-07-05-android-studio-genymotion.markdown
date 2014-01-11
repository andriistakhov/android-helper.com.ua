---
author: andrew
comments: true
date: 2013-07-05 09:49:21+00:00
layout: post
slug: android-studio-genymotion
title: Android Studio и альтернативный емулятор - Genymotion
wordpress_id: 1625
categories:
- Android для начинающих
- Android для профессионалов
- Памятка разработчику
- Программирование
---

В этой статье я расскажу как как установить среду разработки Android используя Android Studio и Genymotion. При переходе от Eclispe/ADT к Android Studio я исследовал альтернативные конфигурации для улучшения сборочной среды. Нашел неплохой эмулятор Genymotion. Интересный, довольно быстрый и очень простой.





**Что нам нужно?**




<!-- more -->



  * [Android Studio](http://android-helper.com.ua/android-studio/)



  * [Oracle VM VirtualBox](https://www.virtualbox.org/wiki/Downloads)



  * [Genymotion Android emulator](http://www.genymotion.com/)



  * [Genymotion Android studio plugin](http://www.genymotion.com/)






**Установка Oracle VM VirtualBox** Если вы используете Windows в загруженном вами пакете будет все необходимое, в противном случае вам нужно будет скачать и установить ее самостоятельно. Процесс простой и прямолинейный но эти действия необходимо выполнить иначе Android Emulator не запуститься.





**Установка Genymotion Android device emulator** Первое что нужно это сделать создать аккаунт, чтобы иметь возможность скачать эмулятор. Вы можете выбирать среди платформ, в моем случае это Mac OS X. Когда вы его скачали и установили у вас должно получиться что-то такое:



![Genymotion Android device emulator](https://lh4.googleusercontent.com/-XTLtY_Lu-uo/UdZ1ohxTDpI/AAAAAAAAIxU/UHNpDYatWSE/w878-h549-no/emulator1.png)  
  
Genymotion Android device emulator



У вас может быть доступно множество эмуляторов. Вы выбираете и грузите его с сети. Как результат у вас готовый эмулятор.



![Genymotion Android device emulator](https://lh3.googleusercontent.com/-bSICk3KwvT0/UdZ1oresDZI/AAAAAAAAIxE/VXQYFWzmdBs/w878-h549-no/emulator2.png)  
  
Genymotion Android device emulator



**Установка Android Studio Plugin** Чтобы интегрировать средство разработки с эмулятором вам необходимо установить плагин для Android Studio. Зайдите в Preferences там Plugin, выбираем нужный нам. Добавляем плагин который мы уже скачали и видим:



![Genymotion Android Studio Plugin](https://lh3.googleusercontent.com/-7ESa9bvI8oY/UdZ1ouyWdyI/AAAAAAAAIxQ/cod9TPdH1Qc/w878-h549-no/emulator3.png)  
  
Genymotion Android Studio Plugin



**Результат**





Теперь вы можете запустить Android проект и вы увидите:



![Genymotion Android Studio Plugin](https://lh4.googleusercontent.com/-pu_27RpWvZI/UdZ1pYXK1xI/AAAAAAAAIxM/qk4W1XYDUw4/w878-h549-no/emulator4_1.png)  
  
Genymotion Android Studio Plugin



Эмулятор полностью интегрирован в вашу среду разработки . Первый раз когда вы запустите эмулятор вы можете увидеть диалог который спросит путь к Android SDK:



![Genymotion Android Studio Plugin](https://lh3.googleusercontent.com/-kJdXolrj9RQ/UdZ1pOHCHFI/AAAAAAAAIxI/L8cSs1wTSNk/w878-h549-no/emulator4.png)  
  
Genymotion Android Studio Plugin



Как-только все настроено можно запускать свой проект. ![Genymotion Android Studio Plugin](https://lh4.googleusercontent.com/-vvfjkEI4RRw/UdZ1ph1p7II/AAAAAAAAIxc/n31dDQvUqj4/w878-h549-no/emulator5.png)





**Видео**



{% youtube 63T-8YRlFmE %}





Надеюсь статья была для вас полезной. Если да нажимайте рассказать друзьям :)
