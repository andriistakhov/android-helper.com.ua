---
author: andrew
comments: true
date: 2012-10-10 19:52:24+00:00
layout: post
slug: speak-android
title: Распознавание и генерация речи в Android
wordpress_id: 721
categories:
- Android для начинающих
- Android для профессионалов
- Программирование
---

Столкнулся с необходимостью распознавать речь на android устройстве.





Долго собирал крупицы информации, пока не нашел хорошею и полною статью





Прошу к прочтению.


<!-- more -->


Последнее время большой интерес у пользователей вызывает возможность распознавания речи в телефонах. Огромная заслуга в популяризации этого направления принадлежит компании Aple, однако Google также располагает подобными технологиями. Собственно этой теме и будет посвящена данная статья. Мы разработаем приложение, которое будет распознавать речь пользователя и воспроизводить результат с помощью голосового движка "Text To Speech" (TTS). Отметим, что распознавание происходит на серверах Google, поэтому для работы приложению необходимо разрешить использовать коммуникационные возможности. Кроме того, распознавание речи не работает на эмуляторе. Тестировать программу необходимо на реальном устройстве.





На самом деле работать с распознаванием и синтезом речи в Android очень просто. Все сложные вычисления скрыты от нас в довольно элегантную библиотеку с простым API. Вы сможете осилить этот урок, даже если имеете весьма поверхностные знания о программировании для Android.





Давайте создадим новый проект в Eclipse. Для наших нужд понадобится версия SDK не меньше 8. Опишем в общих чертах создаваемую программу. При запуске приложения пользователю будет показана кнопка, после нажатия на которую пользователю будет предложено надиктовать фразу. Затем будет осуществлено распознавание и будет показан список возможных вариантов. Поскольку технологии распознавания речи далеки от совершенства, программа не может ручаться за точность результата, именно поэтому будет предложено несколько вариантов. После того, как пользователь выберет один из них, будет запущен генератор голоса, который воспроизведет выбранную фразу. 





Нам понадобится несколько текстовых строк, объявим их в фале "res/values/strings.xml"





	<resources>

    <string name="intro">Press the button to speak!</string>

    <string name="app_name">SpeechRepeat</string>

    <string name="speech">Speak now!</string>

    <string name="word_intro">Suggested words&#8230;</string>

	</resources>







Откроем файл "res/layout/main.xml" и зададим шаблон дизайна приложения. Для этого переключимся из графического в XML редактор и изменим содержимое файла





	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

    android:layout_width="fill_parent"

    android:layout_height="fill_parent"

    android:orientation="vertical"

    android:background="#ff330066"

    android:paddingBottom="5dp" >

 

	</LinearLayout>







Добавим в Linear Layout элемент Text View






	<TextView android:layout_width="fill_parent"

    android:layout_height="wrap_content"

    android:text="@string/intro"

    android:padding="5dp"

    android:textStyle="bold"

    android:textSize="16dp"

    android:gravity="center"

    android:textColor="#ffffff33" />







обратите внимание, TextView ссылается на строку intro, которую мы задали в файле strings.xml. 





После Text View добавим кнопку





	<Button android:id="@+id/speech_btn"

    android:layout_width="match_parent"

    android:layout_height="wrap_content"

    android:text="@string/speech" />








Пользователь будет нажимать эту кнопку, чтобы начать говорить. Кнопка имеет параметр id, через который ее можно вызвать из Java кода. После нажатия на кнопку пользователю показывается сообщение. Нам также понадобится TextView для вывода слов с предложениями






	<TextView android:layout_width="fill_parent"

    android:layout_height="wrap_content"

    android:padding="5dp"

    android:text="@string/word_intro"

    android:textStyle="italic" />






TextView будет использовать строковый ресурс. Нам также понадобится список для вариантов слов







	<ListView android:id="@+id/word_list"

    android:layout_width="fill_parent"

    android:layout_height="0dip"

    android:layout_weight="1"

    android:paddingLeft="10dp"

    android:paddingTop="3dp"

    android:paddingRight="10dp"

    android:paddingBottom="3dp"

    android:layout_marginLeft="20dp"

    android:layout_marginRight="20dp"

    android:layout_marginTop="5dp"

    android:layout_marginBottom="5dp"

    android:background="@drawable/words_bg" />







ListView будет заполняться данными в процессе работы программы, поэтому для доступа к этому компоненту также требуется ID. Обратите также внимание на наличие ресурса drawable. Вы должны сохранить файл words_bg.xml в папке res




	<shape xmlns:android="http://schemas.android.com/apk/res/android"

    android:dither="true">

    <gradient

    android:startColor="#ff000000"

    android:endColor="#ff000000"

    android:centerColor="#00000000"

    android:angle="180" />

    <corners android:radius="10dp" />

    <stroke

    android:width="2dp"

    android:color="#66ffffff" />

	</shape> 







Ничего особенного. Вы можете настроить дизайн ListView по своему усмотрению. Нам осталось задать еще один элемент пользовательского интерфейса - шаблон для элемента ListView. Создайте новый файл res/layout/word.xml со следующим содержанием





	<TextView xmlns:android="http://schemas.android.com/apk/res/android"

    android:layout_width="fill_parent"

    android:layout_height="fill_parent"

    android:gravity="center"

    android:padding="5dp"

    android:textColor="#ffffffff"

    android:textSize="16dp" >

	</TextView>







Таким образом, каждый элемент списка представляет собой просто Text View.





Если Вы все сделали правильно, то при запуске должно получиться следующее





![](http://android-helper.com.ua/images/uploads/2012/10/speechsample1.jpg)




### Программируем распознавание речи в Android





После того, как шаблон будущего приложения создан, можно перейти к кодированию. Откройте java файл главной Activity и добавьте в начало файла





	import java.util.ArrayList;

	import java.util.List;

	import java.util.Locale;

 

	import android.app.Activity;

	import android.content.Intent;

	import android.content.pm.PackageManager;

	import android.content.pm.ResolveInfo;

	import android.os.Bundle;

	import android.speech.RecognizerIntent;

	import android.speech.tts.TextToSpeech.OnInitListener;

	import android.speech.tts.TextToSpeech;

	import android.util.Log;

	import android.view.View;

	import android.view.View.OnClickListener;

	import android.widget.AdapterView;

	import android.widget.AdapterView.OnItemClickListener;

	import android.widget.ArrayAdapter;

	import android.widget.Button;

	import android.widget.ListView;

	import android.widget.Toast;

	import android.widget.TextView;






Изменим немного декларацию главного класса






	public class SpeechRepeatActivity extends Activity implements OnClickListener, OnInitListener {





OnInitListener необходим для работы TTS движка. Внутри класса добавим объявления переменных перед методом onCreate







	//переменная для проверки возможности

	//распознавания голоса в телефоне

	private static final int VR_REQUEST = 999;

 

	//ListView для отображения распознанных слов

	private ListView wordList;

 

	//Log для вывода вспомогательной информации

	private final String LOG_TAG = "SpeechRepeatActivity";

	//***здесь можно использовать собственный тег***

 

	//переменные для работы TTS 

 

	//переменная для проверки данных для TTS 

	private int MY_DATA_CHECK_CODE = 0;

 

	//Text To Speech интерфейс

	private TextToSpeech repeatTTS;



Внутри метода onCreate автоматически сгенерирован код, вызывающий метод родительского класса и устанавливающий главный контекст вывода.




	//вызов суперкласса

	super.onCreate(savedInstanceState);

	//установка контекста вывода

	setContentView(R.layout.main);








Cоздадим переменные для работы с кнопкой и списком распознанных слов



	Button speechBtn = (Button) findViewById(R.id.speech_btn);

	wordList = (ListView) findViewById(R.id.word_list);








Далее необходимо проверить поддерживается ли возможность распознавания голоса телефоном


	//проверяем, поддерживается ли распознование речи

	PackageManager packManager = getPackageManager();

	List<ResolveInfo> intActivities = packManager.queryIntentActivities(new 

        Intent(RecognizerIntent.ACTION_RECOGNIZE_SPEECH), 0);

	if (intActivities.size() != 0) {

    // распознавание поддерживается, будем отслеживать событие щелчка по кнопке

    speechBtn.setOnClickListener(this);

	}

	else

	{

    // распознавание не работает. Заблокируем 

    // кнопку и выведем соответствующее 

    // предупреждение.

    speechBtn.setEnabled(false);

    Toast.makeText(this, "Oops - Speech recognition not supported!", Toast.LENGTH_LONG).show();

	}








Мы запрашиваем среду, поддерживается ли Recognizer Intent. Если поддерживается, мы говорим приложению, что нужно отслеживать щелчок пользователя по кнопке. Если интент не поддерживается, мы блокируем кнопку и выводим соответствующее сообщение пользователю.





Напишем код, обрабатывающий нажатие на кнопку. Внутри класса после метода OnCreate добавим метод OnClick.






	public void onClick(View v) {

    if (v.getId() == R.id.speech_btn) {

        // отслеживаем результат

        listenToSpeech();

    }

	}







Как видите, при нажатии на кнопку мы вызываем метод listenToSpeech().



	private void listenToSpeech() {

 

    //запускаем интент, распознающий речь и передаем ему требуемые данные

    Intent listenIntent = new Intent(RecognizerIntent.ACTION_RECOGNIZE_SPEECH);

    //указываем пакет

 	listenIntent.putExtra(RecognizerIntent.EXTRA_CALLING_PACKAGE, 

           getClass().getPackage().getName());

    //В процессе распознования выводим сообщение

    listenIntent.putExtra(RecognizerIntent.EXTRA_PROMPT, "Say a word!");

    //устанавливаем модель речи

    	listenIntent.putExtra(RecognizerIntent.EXTRA_LANGUAGE_MODEL, 

            RecognizerIntent.LANGUAGE_MODEL_FREE_FORM);

    //указываем число результатов, которые могут быть получены

    listenIntent.putExtra(RecognizerIntent.EXTRA_MAX_RESULTS, 10);

 

    //начинаем прослушивание

    startActivityForResult(listenIntent, VR_REQUEST);

	}





Большая часть приведенного кода стандартна для программ, использующих распознавание голоса. Обратите внимание на параметр EXTRA_PROMPT. Он задает строку-приглашение для пользователя. Параметр EXTRA_MAX_RESULTS определяет максимальное число вариантов распознавания. В конце концов, мы вызываем startActivityForResult. Результат его работы будет передан в метод onActivityResult.





На следующем скриншоте показан экран в момент распознавания речи.





![](http://android-helper.com.ua/images/uploads/2012/10/speechsample2.jpg)





Определим метод onActivityResult



	@Override

	protected void onActivityResult(int requestCode, int resultCode, Intent data) {

    //проверяем результат распознавания речи

    if (requestCode == VR_REQUEST && resultCode == RESULT_OK)

    {

        //Добавляем распознанные слова в список результатов

        ArrayList<String> suggestedWords = 

                 data.getStringArrayListExtra(RecognizerIntent.EXTRA_RESULTS);

        //Передаем список возможных слов через ArrayAdapter компоненту ListView  

        wordList.setAdapter(new ArrayAdapter<String> (this, R.layout.word, suggestedWords));

    }

 

    //tss код здесь

 

    //вызываем метод родительского класса

    super.onActivityResult(requestCode, resultCode, data);

	}





Обратите внимание, при проверке результата мы сравниваем переменную requestCode с константой VR_REQUEST, которую использовали ранее при вызове метода startActivityForResult. Таким образом, мы рассматриваем только результаты от нашего запроса. В метод возвращается 10 вариантов распознанных слов, которые мы записываем в список ArrayList. Этот список мы используем в ArrayAdapter компонента List View. 





Если приложение справилось с задачей и смогло что-то распознать, вы увидите похожий н показанный на левом скриншоте результат. Если приложению не удалось распознать фразу, будет показано сообщение, как на правом скриншоте





![](http://android-helper.com.ua/images/uploads/2012/10/speechsample3.jpg)
![](http://android-helper.com.ua/images/uploads/2012/10/speechsample4.jpg)



Вот, собственно и все. Распознавание голоса в Android - довольно простая задача. Мы вызываем интент RecognizerIntent с требуемыми нами параметрами. Результат  возвращается в onActivityResult.





### Генерация речи в Android





Перейдем ко второй части нашего приложения, связанного с генерацией речи. Мы хотим, чтобы телефон проговаривал фразу из списка результатов. Мы должны определить строку, на которую щелкнул пользователь. Вернемся к методу onCreate и добавим в конец этого метода код




	//засекаем щелчок пользователя по слову из списка

	wordList.setOnItemClickListener(new OnItemClickListener() {

 

    //метод вызывается в ответ на щелчок по слову

    public void onItemClick(AdapterView<?> parent, View view, int position, long id)

    {

        //записываем в переменную TextView строки

        TextView wordView = (TextView)view;

        //получаем строку с текстом

        String wordChosen = (String) wordView.getText();

        //выводим ее в лог для отладки

        Log.v(LOG_TAG, "chosen: "+wordChosen);

        //выводим Toast сообщение

        Toast.makeText(SpeechRepeatActivity.this, "You said: "+wordChosen, 

                Toast.LENGTH_SHORT).show();

    }

	});





Мы используем метод setOnItemClickListener чтобы установить отслеживание щелчков для каждой строки. Внутри нового объекта OnItemClickListener мы описали метод onItemClick, который вызывается в ответ на щелчок по строке списка. Выбранная строка передается, как View в этот метод. Поскольку при проектировании шаблона приложения мы указали, что наш список состоит из TextView, мы преобразуем полученное значение в объект TextView и достаем из него строковое значение. Мы записываем это слово в лог и показываем пользователю Toast сообщение.





Если Вас не интересует процесс генерации речи, Вы можете остановиться и протестировать приложение. 





Для генерации речи необходимо настроить движок TTS. Добавим код в конец метода onCreate



	//подготовка движка TTS для проговаривания слов

	Intent checkTTSIntent = new Intent(); 

	//проверка наличия TTS 

	checkTTSIntent.setAction(TextToSpeech.Engine.ACTION_CHECK_TTS_DATA); 

	//запуск checkTTSIntent интента

	startActivityForResult(checkTTSIntent, MY_DATA_CHECK_CODE); 





Как и в случае распознавания, результат интента возвращается в метод onActivityResult. В этом методе перед строкой super.onActivityResult(requestCode, resultCode, data); добавьте





	//returned from TTS data check

	if (requestCode == MY_DATA_CHECK_CODE)

	{ 

    //все необходимые приложения установлены, создаем TTS 

    if (resultCode == TextToSpeech.Engine.CHECK_VOICE_DATA_PASS) 

        repeatTTS = new TextToSpeech(this, this); 

    //движок не установлен, предположим пользователю установить его

    else

    { 

        //интент, перебрасывающий пользователя на страницу TSS в Google Play

        Intent installTTSIntent = new Intent(); 

        installTTSIntent.setAction(TextToSpeech.Engine.ACTION_INSTALL_TTS_DATA); 

        startActivity(installTTSIntent); 

    } 

	}






Таким образом, мы проверяем наличие TTS движка, и если он не установлен - предлагаем пользователю установить соответствующую программу. 





Чтобы завершить настройку TTS, добавим метод onInit, который вызывается при успешной инициализации TTS.







	public void onInit(int initStatus) {

    if (initStatus == TextToSpeech.SUCCESS)  

        repeatTTS.setLanguage(Locale.UK); //Язык

	}







Здесь мы устанавливаем язык генератора речи.





Для того, чтобы заставить движок проговорить строку, нужно вызвать метод repeatTTS.speak. Вернемся к методу onCreate. Внутри метода onItemClick после строки Toast.makeText(SpeechRepeatActivity.this, "You said: "+wordChosen, Toast.LENGTH_SHORT).show(); добавьте следующий код






	repeatTTS.speak("You said: "+wordChosen, TextToSpeech.QUEUE_FLUSH, null);




Таким образом, одновременно с Toast сообщением пользователь услышит сгенерированную речь. Отметим еще раз, что эмулятор не поддерживает распознавание речи, поэтому тестировать программу необходимо на телефоне.





Полный проект вы можете скачать на [странице с кодами](http://android-helper.com.ua/codes/).





Статья взята из сайта [mobilab.ru](http://www.mobilab.ru/androiddev/androidspeechrecognition.html)
