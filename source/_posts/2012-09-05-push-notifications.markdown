---
author: andrew
comments: true
date: 2012-09-05 13:51:28+00:00
layout: post
slug: push-notifications
title: Service. Уведомления - notifications
wordpress_id: 564
categories:
- Android для начинающих
- Android для профессионалов
- Программирование
---

Добрый день друзья.





Я продолжаю собирать информацию по android программированию у себя на сайте.





Сегодня я нашел достойную статью. Которую следует прочесть как экспертам так и начинающим.





Речь сегодня пойдет о Push-notification.


<!-- more -->


Оригинал статьи [тут](http://startandroid.ru/uroki/vse-uroki-spiskom/164-urok-99-service-uvedomlenija-notifications.html).





Читаем, комментируем.









В принципе, **уведомления **– отдельная от **сервисов **тема. Но чаще всего уведомления используются именно в сервисах, поэтому я решил дать эту тему сейчас.





В андроид (версии < 3) есть строка вверху экрана. Называется она статус-бар. Туда обычно в виде иконок сваливаются различные уведомления для пользователя (новые письма, смс и прочие). Пользователь открывает статус бар – видит там чуть более подробную инфу о событии. Дальше он может либо стереть это уведомление, либо нажать на него и перейти непосредственно к событию.





В этом уроке научимся все это проделывать. Для начала разберем уведомление на логические составляющие, чтобы проще было понять как его создавать и отправлять.





**Первая часть** – то, что видно в статус-баре, когда уведомление только приходит – иконка и текст. Текст потом исчезает и остается только иконка.





**Вторая часть** – то, что мы видим, когда открываем статус бар (тянем вниз). Там уже полноценный View с иконкой и двумя текстами, т.е. более подробная информация о событии.





**Третья часть** – то, что произойдет, если мы нажмем на View из второй части. Тут обычно идет вызов Activity, где мы можем просмотреть полную информацию и обработать событие.





Кроме этого есть еще несколько возможностей, по которым совсем кратко пробежимся в конце урока.





Создадим приложение и сервис. Сервис, как будто загружает файл и посылает уведомление, по нажатию на которое будет открываться приложение и отображать имя файла.









**activity_main.xml**:


	<?xml version="1.0" encoding="utf-8"?>

	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

    android:layout_width="fill_parent"

    android:layout_height="fill_parent"

    android:orientation="vertical" >



    <Button

        android:id="@+id/btnStart"

        android:layout_width="wrap_content"

        android:layout_height="wrap_content"

        android:onClick="onClickStart"

        android:text="Start" >

    </Button>



    <Button

        android:id="@+id/btnStop"

        android:layout_width="wrap_content"

        android:layout_height="wrap_content"

        android:onClick="onClickStop"

        android:text="Stop" >

    </Button>



    <TextView

        android:id="@+id/tv"

        android:layout_width="fill_parent"

        android:layout_height="wrap_content"

        android:text="" >

    </TextView>



	</LinearLayout>


Кнопки для старт/стопа сервиса и TextView для отображения результата





Создаем сервис **MyService**.java и прописываем его в манифесте. В манифесте же настроим сервис так, чтобы он работал в отдельном процессе. Для этого надо в его атрибуте process написать двоеточие и какое-нить слово. В итоге у вас должен получится следующий файл манифест:



	<manifest xmlns:android="http://schemas.android.com/apk/res/android"

    package="com.example.push_notification"

    android:versionCode="1"

    android:versionName="1.0" >



    <uses-sdk

        android:minSdkVersion="8"

        android:targetSdkVersion="15" />



    <application

        android:icon="@drawable/ic_launcher"

        android:label="@string/app_name"

        android:theme="@style/AppTheme" >

        <activity

            android:name=".MainActivity"

            android:label="@string/title_activity_main" >

            <intent-filter>

                <action android:name="android.intent.action.MAIN" />



                <category android:name="android.intent.category.LAUNCHER" />

            </intent-filter>

        </activity>



        <service

            android:name="MyService"

            android:process=":myservice" >

        </service>

    </application>



	</manifest>








Система эту строку добавит к package сервиса и, тем самым, получит название нового процесса, в котором и запустит сервис





 ![](http://android-helper.com.ua/images/uploads/2012/09/L0099_020.jpg)




**MainActivity.java**:



	package com.example.push_notification;



	import android.app.Activity;

	import android.content.Intent;

	import android.os.Bundle;

	import android.text.TextUtils;

	import android.view.View;

	import android.widget.TextView;



	public class MainActivity extends Activity {



	public final static String FILE_NAME = "filename";



	/** Called when the activity is first created. */

	@Override

	public void onCreate(Bundle savedInstanceState) {

		super.onCreate(savedInstanceState);

		setContentView(R.layout.activity_main);



		TextView tv = (TextView) findViewById(R.id.tv);



		Intent intent = getIntent();



		String fileName = intent.getStringExtra(FILE_NAME);

		if (!TextUtils.isEmpty(fileName)) {

			tv.setText(fileName);

		}

	}



	public void onClickStart(View v) {

		startService(new Intent(this, MyService.class));

	}



	public void onClickStop(View v) {

		stopService(new Intent(this, MyService.class));

	}



	}








В onCreate мы вытаскиваем из intent и кладем в TextView текст. Этот текст мы будем отправлять из сервиса через уведомление.



**onClickStart** и **onClickStop** – это обработчики кнопок. Стартуют и останавливают сервис.







**MyService.java**:




	package com.example.push_notification;



	import java.util.concurrent.TimeUnit;

	import android.app.Notification;

	import android.app.NotificationManager;

	import android.app.PendingIntent;

	import android.app.Service;

	import android.content.Intent;

	import android.os.IBinder;



	public class MyService extends Service {



	NotificationManager nm;



	@Override

	public void onCreate() {

		super.onCreate();

		nm = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);

	}



	@Override

	public int onStartCommand(Intent intent, int flags, int startId) {

		try {

			TimeUnit.SECONDS.sleep(5);

		} catch (InterruptedException e) {

			e.printStackTrace();

		}

		sendNotif();

		return super.onStartCommand(intent, flags, startId);

	}



	void sendNotif() {

		// 1-я часть

		Notification notif = new Notification(R.drawable.ic_launcher, "Text in status bar", System.currentTimeMillis());



		// 3-я часть

		Intent intent = new Intent(this, MainActivity.class);

		intent.putExtra(MainActivity.FILE_NAME, "somefile");

		PendingIntent pIntent = PendingIntent.getActivity(this, 0, intent, 0);



		// 2-я часть

		notif.setLatestEventInfo(this, "Notification's title", "Notification's text", pIntent);



		// ставим флаг, чтобы уведомление пропало после нажатия

		notif.flags |= Notification.FLAG_AUTO_CANCEL;

		notif.number = 3;



		// отправляем

		nm.notify(1, notif);

	}



	@Override

	public IBinder onBind(Intent arg0) {

		return null;

	}

	}







В **onCreate **получаем менеджер уведомлений – [NotificationManager](http://developer.android.com/reference/android/app/NotificationManager.html). Он нам понадобится, чтобы отправить уведомление.





В **onStartCommand **запускаем паузу на 5 секунд (эмулируем закачку файла) и после этого отправляем уведомление. Именно из-за этой паузы мы и используем другой процесс, чтобы не тормозило основное приложение.





В **sendNotif **мы создаем и отправляем уведомление. Правда, немного в иной последовательности, что я описывал выше. Сначала первая часть, потом третья, потом вторая.





Первая часть – создаем [Notification](http://developer.android.com/reference/android/app/Notification.html). В конструкторе указываем иконку и текст, которые будут видны в статус-баре. Также мы здесь указываем время. Обычно это текущее время. Но можно указать и прошлое и будущее. По этому времени уведомления будут отсортированы в статус-баре и в его раскрытой части.





Третья часть – создаем Intent, который мы бы использовали для вызова нашего Activity. Туда помещаем имя загруженного файла. Activity его достанет и поместит в TextView. Далее мы оборачиваем этот Intent в PendingIntent, с помощью метода [getActivity](http://developer.android.com/reference/android/app/PendingIntent.html#getActivity(android.content.Context, int, android.content.Intent, int)). На вход ему передаем контекст и Intent. Второй параметр не используется (так написано в хелпе). А четвертый – это флаги, влияющие на поведение PendingIntent. Они не относятся к теме урока, мы их не используем.





Теперь этот созданный PendingIntent содержит информацию о том, что надо вызывать Activity, а также объект Intent, который для этой цели надо использовать. Это будет использовано при нажатии на уведомлении.





Вторая часть – вызываем метод [setLatestEventInfo](http://developer.android.com/reference/android/app/Notification.html#setLatestEventInfo(android.content.Context, java.lang.CharSequence, java.lang.CharSequence, android.app.PendingIntent)). Передаем на вход контекст, текст-заголовок, подробный текст и PendingIntent. Теперь, когда мы откроем статус-бар, мы увидим два этих текста (заголовок и подробный). А, когда нажмем на уведомление, система использует PendingIntent для запуска Activity.





Далее мы для созданного уведомления ставим флаг [FLAG_AUTO_CANCEL](http://developer.android.com/reference/android/app/Notification.html#FLAG_AUTO_CANCEL), чтобы оно исчезло из статус-бара после нажатия. По умолчанию оно не исчезает и продолжает висеть.





Далее вызываем метод [notify](http://developer.android.com/reference/android/app/NotificationManager.html#notify(int, android.app.Notification)) для менеджера уведомлений и передаем туда ID и созданное уведомление. ID используется, если мы хотим изменить или удалить уведомление.





Все сохраним, запустим.





![](http://android-helper.com.ua/images/uploads/2012/09/device-2012-09-05-163359.png)




Жмем **Start **и сразу закрываем приложение кнопкой **Назад**.





Проходит 5 сек и появляется уведомление (первая часть)





![](http://android-helper.com.ua/images/uploads/2012/09/device-2012-09-05-163430.png)







Жмем на уведомление.





Открывается наше приложение (третья часть) и в TextView появляется текст, переданный из сервиса.





![](http://android-helper.com.ua/images/uploads/2012/09/device-2012-09-05-163441.png)




Теперь вкратце пробежимся по остальным интересным возможностям уведомлений.





### Обновление старого или новое уведомление





Если вы создадите новое уведомление и отправите его (notify) с тем же ID, что и у уже существующего уведомления, то новое заменит старое. Таким образом, вы можете уведомления обновлять.





Если же надо показать новое уведомление, то используйте другой ID.





### Удаление





Чтобы убрать уведомление из статус-бара, используется метод [cancel](http://developer.android.com/reference/android/app/NotificationManager.html#cancel(int)) у менеджера уведомлений. На вход подается ID. Либо используйте метод [cancelAll](http://developer.android.com/reference/android/app/NotificationManager.html#cancelAll()), чтобы удалить все уведомления.





### Звук





Если хотите, чтобы уведомление появилось со стандартным звуком, добавьте флаг [Notification.DEFAULT_SOUND](http://developer.android.com/reference/android/app/Notification.html#DEFAULT_SOUND) в поле уведомления [defaults](http://developer.android.com/reference/android/app/Notification.html#defaults).





А для использования своих звуков используется поле [sound](http://developer.android.com/reference/android/app/Notification.html#sound).





Чтобы проиграть файл с **SD**:





_notif.sound = Uri.parse("file:///sdcard/notification/ringer.mp3");_





Чтобы использовать какую-либо из стандартных мелодий, используем **Content Provider**:





_notif.sound = Uri.withAppendedPath(Audio.Media.INTERNAL_CONTENT_URI, "6");_





Подробнее [здесь](http://developer.android.com/guide/topics/ui/notifiers/notifications.html#Sound).





### Вибра





Если хотите, чтобы уведомление появилось со стандартной виброй, добавьте флаг [Notification.DEFAULT_VIBRATE](http://developer.android.com/reference/android/app/Notification.html#DEFAULT_VIBRATE) в поле уведомления defaults.





А для использования своей комбинации вибры используется поле [vibrate](http://developer.android.com/reference/android/app/Notification.html#vibrate). В это поле помещается массив long-чисел. Первое – длительность паузы (в миллисекундах) перед началом вибрирования, второе – длительность вибрирования, третье – длительность паузы, четвертое – длительность вибрирования … и т.д. Т.е. создаете свою комбинацию пауз и вибрирования. И мобила при получении уведомления вам ее провибрирует.





Подробнее [здесь](http://developer.android.com/guide/topics/ui/notifiers/notifications.html#Vibration).





Для работы вибры необходимо прописать права [VIBRATE](http://developer.android.com/reference/android/Manifest.permission.html#VIBRATE) в манифесте.





### Индикатор





Если хотите, чтобы уведомление появилось с миганием индикатора, добавьте флаг [Notification.DEFAULT_LIGHTS](http://developer.android.com/reference/android/app/Notification.html#DEFAULT_LIGHTS) в поле уведомления defaults.





А для использования своей комбинации мигания индикатора используются поля





[ledARGB](http://developer.android.com/reference/android/app/Notification.html#ledARGB) – здесь задается цвет





[ledOnMS](http://developer.android.com/reference/android/app/Notification.html#ledOnMS) – время «горения»





[ledOffMS](http://developer.android.com/reference/android/app/Notification.html#ledOffMS) – время «не горения»





И в поле [flags](http://developer.android.com/reference/android/app/Notification.html#flags) надо добавить флаг [Notification.FLAG_SHOW_LIGHTS](http://developer.android.com/reference/android/app/Notification.html#FLAG_SHOW_LIGHTS).





В итоге индикатор будет мигать с заданными значениями и с заданным цветом. В хелпе написано, что не все девайсы поддерживают разные цвета. Поэтому выбранный вами цвет не гарантируется.





Подробнее [здесь](http://developer.android.com/guide/topics/ui/notifiers/notifications.html#Lights).





### Число





У Notification есть поле [number](http://developer.android.com/reference/android/app/Notification.html#number). Вы можете поместить туда число больше нуля и оно отобразится на уведомлении.





Например, при notif.number = 3 уведомление будет выглядеть так:





![](http://android-helper.com.ua/images/uploads/2012/09/L0099_070-300x210.jpg)





### Флаги





Добавляются в поле flags





[FLAG_INSISTENT](http://developer.android.com/reference/android/app/Notification.html#FLAG_INSISTENT) – звук уведомления будет повторяться, пока не откроют статус-бар





[FLAG_ONGOING_EVENT](http://developer.android.com/reference/android/app/Notification.html#FLAG_ONGOING_EVENT) – уведомление появляется не в обычной секции, а в ongoing (постоянные). Уведомления из этой секции не удаляются при нажатии кнопки очистки уведомлений.





[![](http://android-helper.com.ua/images/uploads/2012/09/L0099_080-300x210.jpg)](http://android-helper.com.ua/images/uploads/2012/09/L0099_080.jpg)





[FLAG_NO_CLEAR ](http://developer.android.com/reference/android/app/Notification.html#FLAG_NO_CLEAR)– уведомление не удалится при очистке всех уведомлений





Не очень понимаю, в чем разница между ongoing и тем, что уведомление не удалится после нажатия на кнопку очистки всех уведомлений. Но флаги такие есть, и я о них упомянул.





Надеюсь вам статья понравилась.





Ожидаю ваших комментариев!





Коды вы можете скачать [тут](http://android-helper.com.ua/forms)
