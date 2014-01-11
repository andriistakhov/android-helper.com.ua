---
author: andrew
comments: true
date: 2012-10-31 04:25:44+00:00
layout: post
slug: fragments-dialogfragment-%d0%b4%d0%b8%d0%b0%d0%bb%d0%be%d0%b3
title: Fragments. DialogFragment - диалог
wordpress_id: 869
categories:
- Android для начинающих
- Android для профессионалов
- Программирование
---

Всем привет.





Продолжаю собирать полезную информацию по программированию.





Сегодня хотел поделиться с вами работой DialogFragment.


<!-- more -->


Продолжаем рассматривать наследников Fragment. [DialogFragment](http://developer.android.com/reference/android/app/DialogFragment.html) – отличается от обычного фрагмента тем, что отображается как диалог и имеет соответствующие методы.





Построить диалог можно двумя способами: используя свой layout-файл и через AlertDialog.Builder. Нарисуем приложение, которое будет вызывать два диалога, построенных разными способами.









Создадим проект:





**Project name**: P1101_DialogFragment  
**Build Target**: Android 4.1   
**Application name**: DialogFragment  
**Package name**: ru.startandroid.develop.p1101dialogfragment  
**Create Activity**: MainActivity









Добавим строки в **strings.xml**:




	<string name="dialog_1">Dialog 1</string>

	<string name="dialog_2">Dialog 2</string>

	<string name="message_text">Text of your message</string>

	<string name="yes">Yes</string>

	<string name="no">No</string>

	<string name="maybe">Maybe</string>








Мы будем создавать два диалога, соответственно нам понадобятся два фрагмента.





Создадим layout-файл для первого фрагмента.





**dialog1.xml**:


	<?xml version="1.0" encoding="utf-8"?>

	<LinearLayout

 	xmlns:android="http://schemas.android.com/apk/res/android"

 	android:layout_width="wrap_content"

 	android:layout_height="match_parent"

 	android:orientation="vertical">

	<TextView

 	android:id="@+id/textView1"

 	android:layout_width="wrap_content"

 	android:layout_height="wrap_content"

 	android:layout_gravity="center"

 	android:layout_margin="20dp"

 	android:text="@string/message_text"

 	android:textAppearance="?android:attr/textAppearanceLarge">

	</TextView>

	<LinearLayout

 	android:layout_width="wrap_content"

 	android:layout_height="wrap_content">

	<Button

 	android:id="@+id/btnYes"

 	android:layout_width="wrap_content"

 	android:layout_height="wrap_content"

 	android:layout_margin="10dp"

 	android:text="@string/yes">

	</Button>

	<Button

 	android:id="@+id/btnNo"

 	android:layout_width="wrap_content"

 	android:layout_height="wrap_content"

 	android:layout_margin="10dp"

 	android:text="@string/no">

	</Button>

	<Button

 	android:id="@+id/btnMaybe"

 	android:layout_width="wrap_content"

 	android:layout_height="wrap_content"

 	android:layout_margin="10dp"

 	android:text="@string/maybe">

	</Button>

	</LinearLayout>

	</LinearLayout>




Так будет выглядеть наш диалог – текст сообщения и три кнопки.









Создаем класс **Dialog1.java**:

	package com.example.dialogfragments;



	import android.content.DialogInterface;

	import android.os.Bundle;

	import android.support.v4.app.DialogFragment;

	import android.util.Log;

	import android.view.LayoutInflater;

	import android.view.View;

	import android.view.View.OnClickListener;

	import android.view.ViewGroup;

	import android.widget.Button;



	public class Dialog1 extends DialogFragment implements OnClickListener {



		final String LOG_TAG = "myLogs";



		@Override

		public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {

		getDialog().setTitle("Title!");

		View v = inflater.inflate(R.layout.dialog1, null);

		v.findViewById(R.id.btnYes).setOnClickListener(this);

		v.findViewById(R.id.btnNo).setOnClickListener(this);

		v.findViewById(R.id.btnMaybe).setOnClickListener(this);

		return v;

		}



		public void onClick(View v) {

		Log.d(LOG_TAG, "Dialog 1: " + ((Button) v).getText());

		dismiss();

		}



		@Override

		public void onDismiss(DialogInterface dialog) {

		super.onDismiss(dialog);

		Log.d(LOG_TAG, "Dialog 1: onDismiss");

		}



		@Override

		public void onCancel(DialogInterface dialog) {

		super.onCancel(dialog);

		Log.d(LOG_TAG, "Dialog 1: onCancel");

		}

	}





В **onCreateView** мы получаем объект Dialog с помощью метода [getDialog](http://developer.android.com/reference/android/app/DialogFragment.html#getDialog()) и устанавливаем заголовок диалога. Далее мы создаем view из layout, находим в нем кнопки и ставим им текущий фрагмент в качестве обработчика.





В **onClick** выводим в лог текст нажатой кнопки и сами явно закрываем диалог методом [dismiss](http://developer.android.com/reference/android/app/DialogFragment.html#dismiss()).





Метод [onDismiss](http://developer.android.com/reference/android/app/DialogFragment.html#onDismiss(android.content.DialogInterface)) срабатывает, когда диалог закрывается. Пишем об этом в лог.





Метод [onCancel](http://developer.android.com/reference/android/app/DialogFragment.html#onCancel(android.content.DialogInterface)) срабатывает, когда диалог отменяют кнопкой Назад. Пишем об этом в лог.









Создаем второй фрагмент. Здесь мы будем строить диалог с помощью билдера, поэтому layout-файл не понадобится. Создаем только класс **Dialog2.java**:


	package com.example.dialogfragments;



	import android.app.AlertDialog;

	import android.app.Dialog;

	import android.content.DialogInterface;

	import android.content.DialogInterface.OnClickListener;

	import android.os.Bundle;

	import android.support.v4.app.DialogFragment;

	import android.util.Log;



	public class Dialog2 extends DialogFragment implements OnClickListener {



	final String LOG_TAG = "myLogs";



	@Override

	public Dialog onCreateDialog(Bundle savedInstanceState) {

		AlertDialog.Builder adb = new AlertDialog.Builder(getActivity()).setTitle("Title!")

				.setPositiveButton(R.string.yes, this).setNegativeButton(R.string.no, this)

				.setNeutralButton(R.string.maybe, this).setMessage(R.string.message_text);

		return adb.create();

	}



	public void onClick(DialogInterface dialog, int which) {

		int i = 0;

		switch (which) {

			case Dialog.BUTTON_POSITIVE:

				i = R.string.yes;

				break;

			case Dialog.BUTTON_NEGATIVE:

				i = R.string.no;

				break;

			case Dialog.BUTTON_NEUTRAL:

				i = R.string.maybe;

				break;

		}

		if (i > 0) {

			Log.d(LOG_TAG, "Dialog 2: " + getResources().getString(i));

		}

	}



	@Override

	public void onDismiss(DialogInterface dialog) {

		super.onDismiss(dialog);

		Log.d(LOG_TAG, "Dialog 2: onDismiss");

	}



	@Override

	public void onCancel(DialogInterface dialog) {

		super.onCancel(dialog);

		Log.d(LOG_TAG, "Dialog 2: onCancel");

	}

	}




Обычно для заполнения фрагмента содержимым мы использовали метод onCreateView. Для создания диалога с помощью билдера используется [onCreateDialog](http://developer.android.com/reference/android/app/DialogFragment.html#onCreateDialog(android.os.Bundle)). Создаем диалог с заголовком, сообщением и тремя кнопками. Обработчиком для кнопок назначаем текущий фрагмент.





В **onClick** определяем, какая кнопка была нажата и выводим соответствующий текст в лог. В случае создания диалога через билдер, диалог сам закроется по нажатию на кнопку, метод dismiss здесь не нужен.





Методы **onDismiss** и **onCancel** – это закрытие и отмена диалога, аналогично первому фрагменту.









Меняем layout-файл для MainActivity - **main.xml**:


	<?xml version="1.0" encoding="utf-8"?>

	<LinearLayout

 	xmlns:android="http://schemas.android.com/apk/res/android"

	xmlns:tools="http://schemas.android.com/tools"

	 android:id="@+id/LinearLayout1"

	 android:layout_width="match_parent"

	 android:layout_height="match_parent"

	 android:orientation="vertical">

	<Button

	 android:id="@+id/btnDlg1"

	 android:layout_width="wrap_content"

	 android:layout_height="wrap_content"

	 android:onClick="onClick"

	 android:text="@string/dialog_1">

	</Button>

	<Button

	 android:id="@+id/btnDlg2"

	 android:layout_width="wrap_content"

	 android:layout_height="wrap_content"

	 android:onClick="onClick"

	 android:text="@string/dialog_2">

	</Button>

	</LinearLayout>







Здесь только две кнопки.









Кодим **MainActivity.java**:



	package com.example.dialogfragments;



	import android.os.Bundle;

	import android.support.v4.app.DialogFragment;

	import android.support.v4.app.FragmentActivity;

	import android.view.View;



	public class MainActivity extends FragmentActivity {



	DialogFragment dlg1;

	DialogFragment dlg2;



	@Override

	public void onCreate(Bundle savedInstanceState) {

		super.onCreate(savedInstanceState);

		setContentView(R.layout.activity_main);

		dlg1 = new Dialog1();

		dlg2 = new Dialog2();

	}



	public void onClick(View v) {

		switch (v.getId()) {

			case R.id.btnDlg1:

				dlg1.show(getSupportFragmentManager(), "dlg1");

				break;

			case R.id.btnDlg2:

				dlg2.show(getSupportFragmentManager(), "dlg2");

				break;

			default:

				break;

		}



	}

	}




Создаем диалоги и запускаем их методом [show](http://developer.android.com/reference/android/app/DialogFragment.html#show(android.app.FragmentManager, java.lang.String)), который на вход требует FragmentManager и строку-тэг. Транзакция и коммит происходят внутри этого метода, нам об этом думать не надо.





Все сохраняем и запускаем приложение.





![](http://startandroid.ru/images/stories/lessons/L0110/L0110_010.JPG)









Жмем **Dialog1**





![](http://startandroid.ru/images/stories/lessons/L0110/L0110_020.JPG)





Отобразился наш простенький диалог.





Жмем какую-нибудь кнопку, например, **Yes **- диалог закрылся. Смотрим логи:





_Dialog 1: Yes  
Dialog 1: onDismiss_





Все верно.









Снова запустим первый диалог и нажмем клавишу **Назад **(**Back**). Смотрим лог:





_Dialog__ 1: __onCancel__  
Dialog 1: onDismiss_





Сработал onCancel – диалог был отменен, и onDismiss – диалог закрылся.





Если мы будем поворачивать экран, то каждый раз будет отрабатывать onDismiss, но диалог снова будет отображен после поворота.









Запустим второй диалог – нажмем кнопку **Dialog 2**.





![](http://startandroid.ru/images/stories/lessons/L0110/L0110_030.JPG)





Отобразился стандартный сконструированный нами диалог. Жмем, например, **No **– диалог закрылся. В логах:





_Dialog 2: No  
Dialog 2: onDismiss_









Снова запустим второй диалог и нажмем **Назад**. В логах:





_Dialog__ 2: __onCancel__  
Dialog 2: onDismiss_





Все так же, как и в первом случае.









Еще несколько слов по теме.





Если вы не хотите, чтобы ваш диалог можно было закрыть кнопкой, используйте для вашего диалог-фрагмента метод [setCancelable](http://developer.android.com/reference/android/app/DialogFragment.html#setCancelable(boolean)) с параметром false.





Есть еще один вариант вызова диалога. Это метод [show](http://developer.android.com/reference/android/app/DialogFragment.html#show(android.app.FragmentTransaction, java.lang.String)), но на вход он уже принимает не FragmentManager, а FragmentTransaction. В этом случае система также сама вызовет commit внутри show, но мы можем предварительно поместить в созданную нами транзакцию какие-либо еще операции или отправить ее в BackStack.





Вы можете использовать диалог-фрагменты, как обычные фрагменты и отображать их на Activity, а не в виде диалога. Но при этом будьте аккуратнее с использованием **getDialog**. Я так понял, что он возвращает null в этом случае.





Если **AlertDialog.Builder** вам незнаком, то посмотрите Урок 60 и несколько следующих за ним. Там достаточно подробно описано, как создавать различные диалоги.





Статья из сайта [startandroid.ru](http://startandroid.ru/uroki/vse-uroki-spiskom/180-urok-110-android-3-fragments-dialogfragment-dialog.html)





Полный проект вы можете скачать на [странице с кодами](http://android-helper.com.ua/codes/).





Пишите ваши комментарии.
