---
author: andrew
comments: true
date: 2013-02-01 08:25:16+00:00
layout: post
slug: android-virtual-device
title: Быстрая альтернатива Android Virtual Device
wordpress_id: 1298
categories:
- Android для начинающих
- Android для профессионалов
- Памятка разработчику
- Программирование
---

Очень познавательная статья.





Читать всем разработчикам!





Перед любым начинающим Android-разработчиком рано или поздно встает вопрос о том, где и как удобнее запускать и отлаживать свои программы.













Самый верный способ — через Android Debug Bridge на реальном Android-устройстве. Но если, по каким то причинам, этот способ не подходит, то остается два варианта — воспользоваться штатным эмулятором Android Virtual Device (AVD) из Android SDK, или установить Android на виртуальную машину.  Про второй вариант я и хочу рассказать в этой статье. Android на виртуальной машине серьезно превосходит AVD по быстродействию и времени загрузки, но есть и недостатки — сложнее эмулировать телефонию (возможно, при помощи дополнительных программ), пока не разобрался как смонтировать SD-карту.







 <!-- more -->





**Итак! Нам понадобится**:





1. Готовая к работе среда разработки и Android SDK Tools - см. StartAndroid.ru урок 2.  
[http://startandroid.ru/uroki/vse-uroki-spiskom/9-urok-2-ustanovka-i-nastrojka-sredy-razrabotki.html](http://startandroid.ru/uroki/vse-uroki-spiskom/9-urok-2-ustanovka-i-nastrojka-sredy-razrabotki.html)





2. VirtualBox последней версии.  
[https://www.virtualbox.org/](https://www.virtualbox.org/).





3. Образ Android. Выбираем любую версию с сайта проекта Android-x86.  
[http://code.google.com/p/android-x86/](http://code.google.com/p/android-x86/)









### Шаг 1 — устанавливаем Virtual Box.





![](http://startandroid.ru/images/stories/articles/A0002/A0002_050.png)





![](http://startandroid.ru/images/stories/articles/A0002/A0002_060.png)





![](http://startandroid.ru/images/stories/articles/A0002/A0002_070.png)





![](http://startandroid.ru/images/stories/articles/A0002/A0002_080.png)





![](http://startandroid.ru/images/stories/articles/A0002/A0002_090.png)





![](http://startandroid.ru/images/stories/articles/A0002/A0002_100.png)













### Шаг 2 — Создаем виртуальную машину с Android.





![](http://startandroid.ru/images/stories/articles/A0002/A0002_110.png)





![](http://startandroid.ru/images/stories/articles/A0002/A0002_120.png)





![](http://startandroid.ru/images/stories/articles/A0002/A0002_130.png)





![](http://startandroid.ru/images/stories/articles/A0002/A0002_140.png)





![](http://startandroid.ru/images/stories/articles/A0002/A0002_150.png)





![](http://startandroid.ru/images/stories/articles/A0002/A0002_160.png)





![](http://startandroid.ru/images/stories/articles/A0002/A0002_170.png)













### Шаг 3 — настройка виртуальной машины и её запуск.





![](http://startandroid.ru/images/stories/articles/A0002/A0002_180.png)





![](http://startandroid.ru/images/stories/articles/A0002/A0002_190.png)





![](http://startandroid.ru/images/stories/articles/A0002/A0002_200.png)





![](http://startandroid.ru/images/stories/articles/A0002/A0002_210.png)





![](http://startandroid.ru/images/stories/articles/A0002/A0002_220.png)





Система не может найти диск для загрузки и останавливается. Монтируем скачанный образ Android.





![](http://startandroid.ru/images/stories/articles/A0002/A0002_230.png)





![](http://startandroid.ru/images/stories/articles/A0002/A0002_240.png)









Выбрать можно любой, но я советую работать с версией для EEEPC, например  
[android-x86-3.2-RC2-eeepc.iso](http://code.google.com/p/android-x86/downloads/detail?name=android-x86-3.2-RC2-eeepc.iso&can=4&q=). В других может возникнуть проблема — из под Android не будет виден виртуальный сетевой адаптер.





Перегружаем машину.





![](http://startandroid.ru/images/stories/articles/A0002/A0002_250.png)





![](http://startandroid.ru/images/stories/articles/A0002/A0002_260.png)





![](http://startandroid.ru/images/stories/articles/A0002/A0002_270.png)





Нажимаем ENTER и ждет загрузки Android.





![](http://startandroid.ru/images/stories/articles/A0002/A0002_280.png)





Закрываем обучающие подсказки. И выключаем функцию интеграции мыши, которая некорректно работает с нашей ОС, скрывая курсор.





![](http://startandroid.ru/images/stories/articles/A0002/A0002_290.png)













### Шаг 4 — настройка сети в виртуальном Android.





Щелкаем внутри окна и оказываемся в пространстве виртуальной машины.   
(чтобы вернуть курсор нужно нажать **правый CTRL**)





![](http://startandroid.ru/images/stories/articles/A0002/A0002_300.png)









Нажимаем **Alt+F1** и оказываемся в консоли.





![](http://startandroid.ru/images/stories/articles/A0002/A0002_310.png)









Присваиваем адрес сетевому адаптеру командой **ifconfig**:  
**ifconfig eth0 192.168.56.2 netmask 255.255.255.0**









Проверяем или переходим к следующему шагу. Основной комп пингуется по адресу 192.168.56.1 ("VirtualBox Host-Only Ethernet Adapter" в диспетчере устройств).  
Команда **ping**:  
**ping 192.168.56.1**  
**Ctrl+C**









Если все сработало нормально то увидим:





![](http://startandroid.ru/images/stories/articles/A0002/A0002_320.png)













### Шаг 5 — подсоединяем Android Debug Bridge (ADB) к виртуальной ОС.





Нажимаем **Alt+F7**, чтобы вернуться к графическому интерфейсу. Нажимаем **правый CTRL** и возвращаемся к реальной ОС. Не закрываем виртуальную машину.





Создаем на рабочем столе ярлык на **adb.exe**





![](http://startandroid.ru/images/stories/articles/A0002/A0002_330.png)





Нужный нам файл находится в папке Android SDK "platform-tools".





![](http://startandroid.ru/images/stories/articles/A0002/A0002_340.png)





Дописываем команду текстом "**connect 192.168.56.2**":





![](http://startandroid.ru/images/stories/articles/A0002/A0002_360.png)





![](http://startandroid.ru/images/stories/articles/A0002/A0002_370.png)









Запускаем ярлык.





Теперь ADB соединен с виртуальным Android, а он, в свою очередь, доступен для запуска и отладки программ из Eclipse. Цель достигнута. :)





Пересоединять ADB придется в том случае, когда вы закрыли или перезагрузили виртуальную машину.





**Так-же в случае, если вы перезагрузили виртуальный Android, придется возвращаться к шагу 4, так как настройки сетевого адаптера будут сброшены**. Поэтому я рекомендую не выключать виртуальную машину, а погружать её в сон, но ярлычек потом запустить все равно придется.





![](http://startandroid.ru/images/stories/articles/A0002/A0002_380.png)





![](http://startandroid.ru/images/stories/articles/A0002/A0002_390.png)













Заходим в Eclipse и проверяем как работает





![](http://startandroid.ru/images/stories/articles/A0002/A0002_400.png)





![](http://startandroid.ru/images/stories/articles/A0002/A0002_410.png)





![](http://startandroid.ru/images/stories/articles/A0002/A0002_420.png)













![](http://startandroid.ru/images/stories/articles/A0002/A0002_430.png)





Благодарности:





**renton** с habrahabr.ru — его идея.  
[http://habrahabr.ru/post/119599/](http://habrahabr.ru/post/119599/)





 Источник [startandroid.ru](http://startandroid.ru/ru/articles/listofarticles/206-bystraja-alternativa-android-virtual-device.html)
