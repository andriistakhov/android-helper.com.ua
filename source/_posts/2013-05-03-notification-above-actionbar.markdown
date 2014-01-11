---
author: andrew
comments: true
date: 2013-05-03 21:47:23+00:00
layout: post
slug: notification-above-actionbar
title: Notification поверх ActionBar
wordpress_id: 1467
categories:
- Android для начинающих
- Android для профессионалов
- Программирование
---

> 


> 
> Привет друзья. Я очень рад, приветствовать вас на нашем блоге. Сегодня мы рассмотрим следующую задачу.
> 
> 







### Задача





Нужно сделать вывод сообщения поверх ActionBar.


 <!-- more -->

![device-2013-05-03-234631](http://android-helper.com.ua/images/uploads/2013/05/device-2013-05-03-234631.png)]
![device-2013-05-03-234636](http://android-helper.com.ua/images/uploads/2013/05/device-2013-05-03-234636.png)
![device-2013-05-03-234643](http://android-helper.com.ua/images/uploads/2013/05/device-2013-05-03-234643.png)
![device-2013-05-03-234657](http://android-helper.com.ua/images/uploads/2013/05/device-2013-05-03-234657.png)




### Краткое описание





И так наша задача вывести нотификацию поверх ActionBar. Ну скажем это одно из требований дизайна и заказчика.





### Видео


{% youtube xIuhouqkX8E %}






### Коды для реализации





**MainActivity.java**




    
    @Override
    
    public void onCreate(Bundle savedInstanceState) {
    
        super.onCreate(savedInstanceState);
    
        ActionBar actionBar = getActionBar();
    
        actionBar.setDisplayOptions(ActionBar.DISPLAY_SHOW_HOME
    
                | ActionBar.DISPLAY_SHOW_TITLE | ActionBar.DISPLAY_SHOW_CUSTOM);
    
    
    
        setContentView(R.layout.main);
    
    
    
        prepareNotificationBar();
    
    
    
        initButtons();
    
    }
    
    
    
    private void prepareNotificationBar() {
    
        android.view.Window window = getWindow();
    
    
    
        // Получаем полное строенние вашего приложения
    
        ViewGroup decor = (ViewGroup) window.getDecorView();
    
        // Берем первый элемент, это то что было построенно
    
        View allcontent = decor.getChildAt(0);
    
        // Удаляем его что бы очистить строение
    
        decor.removeView(allcontent);
    
    
    
        LayoutInflater li = getLayoutInflater();
    
        // загружаем части элементов
    
        FrameLayout main = (FrameLayout) li.inflate(R.layout.content_frame, null);
    
        FrameLayout notificationFrame = (FrameLayout) li.inflate(R.layout.notification_frame, null);
    
        // находим нужные нам фреймы
    
        notificationBar = (ViewGroup) main.findViewById(R.id.notificationLayout);
    
        content = (ViewGroup) main.findViewById(R.id.contentLayout);
    
    
    
        // ВАЖНО! получаем высоту статус бара
    
        // -----
    
        int resourceId = getResources().getIdentifier("status_bar_height", "dimen", "android");
    
    
    
        statusBarHeight = 0;
    
    
    
        if (resourceId > 0) {
    
            statusBarHeight = getResources().getDimensionPixelSize(resourceId);
    
        }
    
        // -----
    
    
    
        // Сдвигаем наши фреймы на высоту статус бара
    
        notificationBar.setPadding(notificationBar.getPaddingLeft(), statusBarHeight, notificationBar.getPaddingRight(),
    
                0);
    
        notificationBar.addView(notificationFrame);
    
    
    
        content.setPadding(content.getPaddingLeft(), statusBarHeight, content.getPaddingRight(),
    
                0);
    
    
    
        // добавляем фреймы
    
        content.addView(allcontent);
    
        decor.addView(main);
    
    }
    
    







### Скачать коды





Скачать коды программы вы можете на нашей [странице](http://android-helper.com.ua/codes/)





### Скачать приложение





Также мы выложили на [Play Market](https://play.google.com/store/apps/details?id=com.android_helper.ActionBarNotification) наше приложение. Установив его, вы сможете посмотреть как оно работает. (Не забудьте поставить +1 и написать позитивный комментарий :) ).





[![Android app on Google Play](https://developer.android.com/images/brand/en_app_rgb_wo_45.png)](https://play.google.com/store/apps/details?id=com.android_helper.ActionBarNotification)





### Оценка статьи





Надеюсь, что вам понравилась статья. Жду ваши комментарии и несколько лайков.
