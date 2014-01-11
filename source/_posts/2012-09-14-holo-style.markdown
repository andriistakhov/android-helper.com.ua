---
author: andrew
comments: true
date: 2012-09-14 11:42:43+00:00
layout: post
slug: holo-style
title: Добавляем к Android-приложению стили Android 4
wordpress_id: 610
categories:
- Android для начинающих
- Android для профессионалов
- Программирование
---

Недавно столкнулся с задачей - сделать приложение для android 4 с его интерфейсом.





Все было супер, пока я не запустил на android 2.3. И там увидел, что таких стилей нету.





Покопав просторы интернета. Я нашел такую [статью](http://findevelop.blogspot.com/2012/06/android-android-4.html#more)





Но так как я на своем блоге собираю всю полезную информацию. Я ее продублирую у себя на блоге.





Прошу к прочтению:

 <!-- more -->



![](http://android-helper.com.ua/images/uploads/2012/09/holo.jpg)




Ничто так не портит настроение программиста, как необходимость заниматься вместо программирования чем-то другим. Например, дизайном. Но приложение без дизайна в наше время  обречено на забвение избалованных пользователей. Как же быть? А давайте нашим формам и кнопочкам "выдадим" стандартный стиль Android Ice Cream Sandwich. Пусть наши пользователи, которые пока ещё не обновились до последней версии Android наслаждаются стильным дизайном и проникаются к нашему приложению тёплыми чувствами. В этом добром деле нам поможет библиотека [HoloEverywhere](https://github.com/ChristopheVersieux/HoloEverywhere). Использовать всю библиотеку мы не будем, нам хватит и одной темы, например моей любимой HoloEverywhereDark. А прикрутим к проекту мы её ручками, попутно изучив механизм "стилизации" Android-приложений.   
  
**Стили - это просто**  
  
В res/values/ нашего проекта создаём файл styles.xml, где мы будем описывать стили для всех контролов в нашей теме. Описывать - это громко сказано: мы просто скопируем аналогичный файл из библиотеки HoloEverywhere, удалив лишние стили. Картинки для HoloEverywhereDark копируем в свои каталоги drawable-mdpi, drawable-hdpi и drawable-xhdpi. Название всех нужных файлов заканчивается на "dark".   
Кроме картинок нам понадобятся xml-файлы описания ресурсов, напрмер цветов. Их складываем в каталог drawable. Больше нам из библиотеки HoloEverywhere ничего не понадобится.  
Посмотрим подробнее в наш styles.xml. Например:



	<style name="ButtonHoloDark" parent="android:style/Widget.Button">

 	<item name="android:background">@drawable/btn_default_holo_dark</item>

 	<item name="android:textColor">#ffffff</item>

	</style>








В этом фрагменте мы видим описание стиля кнопки с именем ButtonHoloDark. Тег style имеет обязательный атрибут name и необязательный parent. Именно parent позволяет нам реализовать иерархию стилей элементов интерфейса Android-приложений. В данном случае мы наследуем стиль стандартной кнопки и переопределяем в нашем новом стиле два item-а: цвет фона и цвет текста. Цвет можно задавать как комбинацией rgb (как в html), так и ссылкой на xml-ресурс. В данном случае для фона кнопки это файл btn_default_holo_dark.xml в каталоге drawable. Посмотрим в него и поймём почему используется xml-ресурс вместо обычного описания цвета:




	<selector xmlns:android="http://schemas.android.com/apk/res/android">

    <item android:drawable="@drawable/btn_default_normal_holo_dark" android:state_enabled="true" android:state_window_focused="false"/>

    <item android:drawable="@drawable/btn_default_disabled_holo_dark" android:state_enabled="false" android:state_window_focused="false"/>

    <item android:drawable="@drawable/btn_default_pressed_holo_dark" android:state_pressed="true"/>

    <item android:drawable="@drawable/btn_default_focused_holo_dark" android:state_enabled="true" android:state_focused="true"/>

    <item android:drawable="@drawable/btn_default_normal_holo_dark" android:state_enabled="true"/>

    <item android:drawable="@drawable/btn_default_disabled_focused_holo_dark" android:state_focused="true"/>

    <item android:drawable="@drawable/btn_default_disabled_holo_dark"/>

	</selector>






Тут описано несколько цветов, которые назначаются фону кнопки в зависимости от её состояния. Эти цвета уже не xml-ресурсы а обычные изображения (некоторые в формате nine-patch, оптимизированном для "растягивания").  
И так мы дошли до подножия "пирамиды стилей". А что же наверху?  
Мы, безусловно можем назначить готовый стиль кнопке в нашем xml-layout или в коде. Но зачем этот утомительный процесс? Назначить стиль всем элементам интерфейса в приложении можно одной инструкцией в AndroidManifest.xml:





	android:theme="@style/Theme.HoloEverywhereDark"






в теге application.  
Так мы устанавливаем "тему", которая описывается в том же styles.xml.  
Отличие от обычного описания стиля только в том, что стиль кнопки определяет в item-ах её свойства, а item-ами темы является сама кнопка и другие элементы интерфейса. Например:






	<style name="Theme.HoloEverywhereDark" parent="android:Theme.NoTitleBar">

       ...

       <item name="android:buttonStyle">@style/ButtonHoloDark</item>

       ...

	</style>







Вот тут мы и связываем тему с стилем отдельного элемента, который мы видели вначале. Как видите, использовать стили в приложении для Android ничуть не сложнее, чем в web-страницах. 





Надеюсь вам понравилась статья.





Если это так, тогда ваши комментарии будут кстати. :)
