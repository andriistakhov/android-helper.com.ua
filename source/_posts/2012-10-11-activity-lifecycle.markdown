---
author: andrew
comments: true
date: 2012-10-11 09:41:17+00:00
layout: post
slug: activity-lifecycle
title: Activity Lifecycle. В каких состояниях может быть Activity
wordpress_id: 747
categories:
- Android для начинающих
- Программирование
---

Многие меня спрашивают.





Что когда нужно вызывать в Activity. И когда вызываются разные методы.





И мне попалась интересная статья.





Лично для себя я читал официальную статью. Аналогов на русской тогда не было.





И так прошу к прочтению.


<!-- more -->


### Теория





При работе приложения, мы **создаем** новые **Activity** и **закрываем** старые, **сворачиваем** приложение, снова **открываем** и т.д. Activity умеет обрабатывать все эти движения. Это необходимо, например, для освобождения ресурсов или сохранения данных. В [хелпе](http://developer.android.com/guide/topics/fundamentals/activities.html#Lifecycle) достаточно подробно это описано.





Созданное при работе приложения **Activity **может быть в одном из **трех состояний**:





**Resumed**- Activity видно на экране, оно находится в фокусе, пользователь может с ним взаимодействовать. Это состояние также иногда называют Running.  
**Paused**- Activity не в фокусе, пользователь не может с ним взаимодействовать, но его видно (оно перекрыто другим Activity, которое занимает не весь экран или полупрозрачно).  
**Stopped**- Activity не видно (полностью перекрывается другим Activity), соответственно оно не в фокусе и пользователь не может с ним взаимодействовать.





Когда Activity переходит из одного **состояния** в другое, система вызывает различные его **методы**, которые мы можем заполнять своим кодом. Схематично это можно изобразить так:





![](http://android-helper.com.ua/images/uploads/2012/10/20111005_L0023_L_StatesSchema.jpg)





Для упрощения понимания я дал краткое описание состояний в скобках под названиями. А крестом обозначил отсутствие Activity.





Итак, мы имеем следующие методы Activity, которые вызывает система:





**onCreate**() – вызывается при первом создании Activity  
**onStart**() – вызывается перед тем, как Activity будет видно пользователю  
**onResume**() – вызывается перед тем как будет доступно для активности пользователя (взаимодействие)





**onPause**() – вызывается перед тем, как будет показано другое Activity  
**onStop**() – вызывается когда Activity становится не видно пользователю  
**onDestroy**() – вызывается перед тем, как Activity будет уничтожено





Т.е. эти методы **НЕ** вызывают смену состояния. Наоборот, смена состояния Activity является триггером, который вызывает эти методы. Тем самым нас уведомляют о смене, и мы можем реагировать соответственно. Посмотрим на практике, когда и в каком порядке вызываются эти методы.





###  





### Практика





В этом уроке нам надо будет эмулировать событие смены ориентации экрана. Но эмулятор с Android 2.3 делает это криво, поэтому в проекте будем использовать версию 2.2. Для этого надо [создать](http://startandroid.ru/uroki/vse-uroki-spiskom/12-urok-3-sozdanie-avd-pervoe-prilozhenie-struktura-android-proekta.html) новое AVD по версии 2.2 





![](http://android-helper.com.ua/images/uploads/2012/10/20111005_L0023_L_AVD22.jpg)




Создадим проект (обратите внимание, используем Android 2.2.):





**Project name**: P0231_OneActivityState  
**Build Target**: Android 2.2  
**Application name**: OneActivityState  
**Package name**: ru.startandroid.develop.p0231oneactivitystate  
**Create Activity**: MainActivity





Layout не меняем, нам он сейчас не важен.



	import android.app.Activity;

	import android.os.Bundle;

	import android.util.Log;



	public class MainActivity extends Activity {

  

  	final String TAG = "States";

  

    /** Called when the activity is first created. */

    @Override

    public void onCreate(Bundle savedInstanceState) {

        super.onCreate(savedInstanceState);

        setContentView(R.layout.main);

        Log.d(TAG, "MainActivity: onCreate()");

    }

    

  	@Override

  	protected void onStart() {

    	super.onStart();

   		Log.d(TAG, "MainActivity: onStart()");

  	}



  	@Override

  	protected void onResume() {

    	super.onResume();

    	Log.d(TAG, "MainActivity: onResume()");

  	}



  	@Override

  	protected void onPause() {

    	super.onPause();

    	Log.d(TAG, "MainActivity: onPause()");

  	}



  	@Override

  	protected void onStop() {

    	super.onStop();

    	Log.d(TAG, "MainActivity: onStop()");

  	}

    

  	@Override

  	protected void onDestroy() {

   		super.onDestroy();

    	Log.d(TAG, "MainActivity: onDestroy()");

  	}

	}







Добавим все остальные **методы** из схемы, и в каждый добавим запись в **лог**.





В каментах подсказали важное замечание! При реализации этих методов обязательно вызывайте соответствующие методы супер-класса и обязательно перед вашим кодом. См. код выше. Каждый метод содержит вызов метода супер-класса и свой код расположен после этих вызовов.





Все сохраним и запустим приложение. После того, как запустилось, смотрим лог:





_MainActivity: onCreate()  
MainActivity: onStart()  
MainActivity: onResume()_





Activity создалось, прошло два состояния (Stopped, Paused) и теперь находится в третьем состоянии - Resumed. Т.е. оно создалось (onCreate), отобразилось (onStart) и получило возможность взаимодействовать с пользователем (onResume).





Теперь нажмем кнопку Back на эмуляторе. Activity закрылось. Смотрим лог:





_MainActivity: onPause()  
MainActivity: onStop()  
MainActivity: onDestroy()_





Activity проделывает путь, обратный созданию. Сначала теряет фокус (onPaused), затем исчезает с экрана (onStop), затем полностью уничтожается (onDestroy).





###  





### Смена ориентации экрана





Посмотрим, как ведет себя Activity, когда происходит смена ориентации экрана. Запустите снова приложение (либо найдите его в списке приложений в системе на эмуляторе, либо снова нажмите CTRL+F11 в Eclipse ). В логах снова отобразились три метода, вызванные при создании. Теперь в эмуляторе нажмите CTRL+F12, ориентация сменилась. Кажется, что ничего особенного не произошло, но смотрим логи и видим:





_MainActivity: onPause()  
MainActivity: onStop()  
MainActivity: onDestroy()  
MainActivity: onCreate()  
MainActivity: onStart()  
MainActivity: onResume()_





Activity полностью уничтожается и снова создается. При этом обычно выполняются процедуры сохранения и восстановления данных, чтобы не потерялись данные, и приложение сохранило свой вид. Про то, как это делается, мы будем говорить в последующих уроках.





Также есть еще метод **onRestart**. Он вызывается перед методом **onStart**, если Activity не создается с нуля, а восстанавливается из состояния **Stoped**. Его мы рассмотрим в следующем уроке.





Обычно в учебниках эта тема дается по-другому. Но мне это шаблонное объяснение кажется недостаточно понятным, поэтому я написал свое. Как всегда, надеюсь, что у меня получилось раскрыть тему )





Советую вам после этого урока прочитать хелп, ссылку на который я дал в самом начале урока. Там все очень хорошо написано. И знания лучше усвоятся. Пока что, главное – это понять в какой момент, какой метод вызывается. А уже дальше мы будем разбираться, как это можно использовать и что там кодить.





Статья взята из сайта [startandroid.ru](http://startandroid.ru/)
