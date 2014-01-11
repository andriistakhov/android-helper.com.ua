---
author: andrew
comments: true
date: 2013-01-24 05:42:06+00:00
layout: post
slug: textview-html-%d1%80%d0%b0%d0%b7%d0%bc%d0%b5%d1%82%d0%ba%d0%b0
title: TextView + HTML разметка
wordpress_id: 1120
categories:
- Android для начинающих
- Android для профессионалов
- Программирование
---

Всем привет.





Прошу простить меня, что очень долго не радовал своих читателей блога, хорошими примерами.





Вот недавно я лазил по просторах инета и нашел замечательную [статью](http://habrahabr.ru/post/166351/).





О том как можно сделать разметку html в обычном TextView.

 <!-- more -->



Автор довольно хорошо высветел эту тему. Хотел переработать для вас, но все что тут написано, действительно нужно.





Ну что ж господа. Прошу к прочтению.





Недавно мне понадобилось сделать довольно хитроумный чат в приложении под Android. Помимо собственно информации требовалось передавать пользователям дополнительную функциональность в контексте определенного сообщения: имя автора сообщения по нажатию на него должно вставляться в текстовое поле ответа, а если это сообщение о только что созданном игровом сеансе, пользователи должны иметь возможность присоединиться к игре по клику и так далее. Одним из главных требований была возможность создавать сообщение, содержащее несколько ссылок, что и задало направление исследований.  
  
**WebView**, обладая нужной функциональностью, был отвергнут по причине тяжести решения: я даже не стал создавать 100 или сколько-нибудь там экземпляров в тестовых целях, по одному на каждое сообщение, поскольку сразу было понятно, что это расточительство нормально работать не будет.





К счастью, самый обычный [**TextView**](http://developer.android.com/reference/android/widget/TextView.html) обладает неожиданно потрясающей функциональностью по разметке текста и может использоваться как в качестве отдельного элемента, так и служить целой страницей, будучи несравненно легковеснее, чем **WebView**.





Я реализовал весь, необходимый мне функционал и выяснил ещё несколько довольно интересных вещей, столкнувшись с некоторым количеством подводных камней (впрочем, не очень острых). Можно сказать, всё нижеописанное — руководство по созданию достаточно мощной справочной системы в своём приложении практически даром.





### Задачи





В данном примере мы создадим приложение с двумя **Activity**, одна из которых содержит **TextView**, исполняющий роль браузера, из которого, в частности, можно вызвать вторую **Activity**, демонстрирующую работу с параметрами вызова. Мы выясним, каким образом можно создавать страницы текста с разметкой и изображениями и связывать их ссылками. 





Содержимое страниц берётся из строк в ресурсах приложения, а изображения являются drawable-ресурсами. Небольшие изменения в коде позволят использовать другие расположения.





![](http://habrastorage.org/storage2/a04/b2e/d4b/a04b2ed4b25633dfdc13fe3927e3c859.jpg)





### Создание приложения





Любым удобным нам способом создаём обычное приложение:









**AndroidManifest.xml**



	<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="com.example.markup.tutorial" android:versionCode="1" android:versionName="1.0">

 	<uses-sdk android:minSdkVersion="7" android:targetSdkVersion="15" /> 

 	<application android:icon="@drawable/ic_launcher" android:label="@string/app_name" android:theme="@style/AppTheme">

  	<activity android:name=".MainActivity" android:label="@string/title_activity_main">

   	<intent-filter>

    	<action android:name="android.intent.action.MAIN" /> 

    	<category android:name="android.intent.category.LAUNCHER" /> 

   	</intent-filter>

  	</activity>

  	<activity android:name=".AnotherActivity" android:exported="false">

   	<intent-filter>

    <data android:scheme="activity-run" android:host="AnotherActivityHost" /> 

    <action android:name="android.intent.action.VIEW" /> 

    <category android:name="android.intent.category.DEFAULT" /> 

   	</intent-filter>

  	</activity>

 	</application>

	</manifest> 
  








Немного пояснений к манифесту. Если с первой **Activity** всё понятно, вторая (**AnotherActivity**) содержит некие дополнительные описатели.





**android:exported=«false»** необходимо для того, чтобы компилятор не выдавал предупреждения о том, что мы забыли что-то прописать в экспортируемом компоненте. На мой взгляд, чисто декоративный момент, но чем меньше жёлтых треугольничков — тем спокойнее.





Раздел **intent-filter** содержит описатели того, каким образом и при каких обстоятельствах будет происходить запуск **Activity**.





 означает, что можно запустить **Activity** ссылкой вида **activity-run://AnotherActivityHost?params...**





Значения **action** и **category** необходимы системе для того чтобы обнаружить и запустить **Activity**.









**MainActivity.java**

	package com.example.markup.tutorial;

	import org.xml.sax.XMLReader;

	import android.os.Bundle;

	import android.app.Activity;

	import android.graphics.drawable.Drawable;

	import android.text.Editable;

	import android.text.Html;

	import android.text.Spannable;

	import android.text.Spanned;

	import android.text.method.LinkMovementMethod;

	import android.widget.TextView;



	public class MainActivity extends Activity {



    TextView tvContent;

    

    @Override

    public void onCreate(Bundle savedInstanceState) {

        super.onCreate(savedInstanceState);

        setContentView(R.layout.activity_main);

        

        tvContent = (TextView)findViewById(R.id.tvContent);

        tvContent.setLinksClickable(true);

        tvContent.setMovementMethod(new LinkMovementMethod());

        

        setArticle("article_main");

    }



    void setArticle(String strArticleResId) {

    	int articleResId = getResources().getIdentifier(strArticleResId, "string", getPackageName());

    	String text = getString(articleResId);

    	if (text == null) text = "Article not found";

    	

    	Spanned spannedText = Html.fromHtml(text, htmlImageGetter, htmlTagHandler);

    	Spannable reversedText = revertSpanned(spannedText);

    	

    	tvContent.setText(reversedText);

    }

    

    final Spannable revertSpanned(Spanned stext) {

        Object[] spans = stext.getSpans(0, stext.length(), Object.class);

        Spannable ret = Spannable.Factory.getInstance().newSpannable(stext.toString());

        if (spans != null && spans.length > 0) {

            for(int i = spans.length - 1; i >= 0; --i) {

                ret.setSpan(spans[i], stext.getSpanStart(spans[i]), stext.getSpanEnd(spans[i]), stext.getSpanFlags(spans[i]));

            }

        }



        return ret;

    }

    

    Html.ImageGetter htmlImageGetter = new Html.ImageGetter() {

        public Drawable getDrawable(String source) {

            int resId = getResources().getIdentifier(source, "drawable", getPackageName());

            Drawable ret = MainActivity.this.getResources().getDrawable(resId);

            ret.setBounds(0, 0, ret.getIntrinsicWidth(), ret.getIntrinsicHeight());

            return ret;

        }

    };



    Html.TagHandler htmlTagHandler = new Html.TagHandler() {

        public void handleTag(boolean opening, String tag, Editable output,	XMLReader xmlReader) {

            Object span = null;

            if (tag.startsWith("article_")) span = new ArticleSpan(MainActivity.this, tag);

            else if ("title".equalsIgnoreCase(tag)) span = new AppearanceSpan(0xffff2020, AppearanceSpan.NONE, 20, true, true, false, false);

            else if (tag.startsWith("color_")) span = new ParameterizedSpan(tag.substring(6));

            if (span != null) processSpan(opening, output, span);

        }

    };

    

    void processSpan(boolean opening, Editable output, Object span) {

        int len = output.length();

        if (opening) {

            output.setSpan(span, len, len, Spannable.SPAN_MARK_MARK);

        } else {

            Object[] objs = output.getSpans(0, len, span.getClass());

            int where = len;

            if (objs.length > 0) {

                for(int i = objs.length - 1; i >= 0; --i) {

                    if (output.getSpanFlags(objs[i]) == Spannable.SPAN_MARK_MARK) {

                        where = output.getSpanStart(objs[i]);

                        output.removeSpan(objs[i]);

                        break;

                    }

                }

            }

            

            if (where != len) {

                output.setSpan(span, where, len, Spannable.SPAN_EXCLUSIVE_EXCLUSIVE);

            }

        }

    }

    

	}   








**AnotherActivity.java**



	package com.example.markup.tutorial;



	import android.app.Activity;

	import android.app.AlertDialog;

	import android.content.DialogInterface;

	import android.net.Uri;

	import android.os.Bundle;



	public class AnotherActivity extends Activity {



    @Override

    public void onCreate(Bundle savedInstanceState) {

        super.onCreate(savedInstanceState);

    

        Uri uri = getIntent().getData();

        

        String caption = uri.getQueryParameter("caption");

        String text = uri.getQueryParameter("text");

        

        new AlertDialog.Builder(this)

        	.setTitle(caption)

        	.setMessage(text)

        	.setPositiveButton("OK", dioclOK)

        	.setCancelable(false)

        	.create().show();

    }

    

    DialogInterface.OnClickListener dioclOK = new DialogInterface.OnClickListener() {

        

        public void onClick(DialogInterface dialog, int which) {

            dialog.dismiss();

            finish();

        }

    };

    

	}  












**AppearanceSpan.java**



	package com.example.markup.tutorial;



	import android.text.TextPaint;

	import android.text.style.CharacterStyle;



	public class AppearanceSpan extends CharacterStyle {

    

    public static final int NONE = -1;

    

    final int color, bgColor, textSize;

    final boolean boldText, italicText, strikeThruText, underlineText;

    

    public AppearanceSpan(int color, int bgColor, int textSize, boolean boldText, boolean italicText, 

            boolean strikeThruText, boolean underlineText) {

        this.color = color;

        this.bgColor = bgColor;

        this.textSize = textSize;

        this.boldText = boldText;

        this.italicText = italicText;

        this.strikeThruText = strikeThruText;

        this.underlineText = underlineText;

    }

    

    @Override

    public void updateDrawState(TextPaint tp) {

        if (color != NONE) tp.setColor(color);

        if (bgColor != NONE) tp.bgColor = bgColor;

        tp.setFakeBoldText(boldText);

        tp.setStrikeThruText(strikeThruText);

        if (textSize != NONE) tp.setTextSize(textSize);

        tp.setUnderlineText(underlineText);

        tp.setTextSkewX(italicText ? -0.25f : 0);

    }



	} 
  












**ArticleSpan.java**


	package com.example.markup.tutorial;



	import android.text.style.ClickableSpan;

	import android.view.View;



	public class ArticleSpan extends ClickableSpan {



    final MainActivity activity;

    final String articleId;

    

    public ArticleSpan(MainActivity activity, String articleId) {

        super();

        this.activity = activity;

        this.articleId = articleId;

    }

    

    @Override

    public void onClick(View arg0) {

        activity.setArticle(articleId);

    }



	}
  












**ParameterizedSpan.java**


	package com.example.markup.tutorial;



	import android.graphics.Color;

	import android.text.TextPaint;

	import android.text.style.CharacterStyle;



	public class ParameterizedSpan extends CharacterStyle {



    int color = 0;

    

    public ParameterizedSpan(String param) {

        try {

            color = Color.parseColor("#" + param);

        } catch(Exception ex) { }

    }



    @Override

    public void updateDrawState(TextPaint tp) {

        tp.setColor(color);

    }



	}
  








### Подготовка ресурсов









**layout/activity_main.xml"**

	<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"

    xmlns:tools="http://schemas.android.com/tools"

    android:layout_width="fill_parent"

    android:layout_height="fill_parent" >



    <ScrollView 

        android:id="@+id/sv"

        android:layout_width="fill_parent"

        android:layout_height="fill_parent"

        >

        <TextView

            android:id="@+id/tvContent"

            android:layout_width="wrap_content"

            android:layout_height="wrap_content"

            android:text="Content" />

    </ScrollView>

	</RelativeLayout>  
  












**values/strings.xml"**


	<resources>

    <string name="app_name">MarkupTutor</string>

    <string name="hello_world">Hello world!</string>

    <string name="menu_settings">Settings</string>

    <string name="title_activity_main">MainActivity</string>

    

    <string name="article_main" formatted="false"><![CDATA[

		<title>Главная страница</title><br/>

		<br/>

		<img src="res_pushkin_little"> <article_pushkin_stih>А.С. 	Пушкин "Как пить мы станем"</article_pushkin_stih><br/>

		<img src="res_activity_little"> <a href="activity-run://	AnotherActivityHost?caption=Another%20Activity&text=Hello%20from%20markup!">Вызвать другую Activity</a><br/>

		<br/>

		<color_ff00ff00>Тест параметризированного тэга <color_ffff00ff>и вложенных спанов</color_ffff00ff>.</color_ff00ff00><br/>

		Тест анимированного GIF-а:<br/>

		<img src="res_alien_anim">

    ]]></string>

        	

    <string name="article_pushkin_stih" formatted="false"><![CDATA[

		<br/><article_main>На главную</article_main><br/><br/>

		<img src="res_pushkin" /><br/><br/>

		Сват Иван, как пить мы станем,<br/>

		Непременно уж помянем<br/>

		Трех Матрен, Луку с Петром,<br/>

		Да Пахомовну потом.<br/>

		Мы живали с ними дружно,<br/>

		Уж как хочешь - будь что будь -<br/>

		Этих надо помянуть,<br/><br/>



		Помянуть нам этих нужно.<br/>

		Поминать, так поминать,<br/>

		Начинать, так начинать,<br/>

		Лить, так лить, разлив разливом.<br/>

		Начинай-ка, сват, пора.<br/>

		Трех Матрен, Луку, Петра<br/>

		В первый раз помянем пивом,<br/>

		А Пахомовну потом<br/>

		Пирогами да вином,<br/>

		Да еще ее помянем:<br/>

		Сказки сказывать мы станем -<br/>

		Мастерица ведь была<br/>

		И откуда что брала.<br/>

		А куды разумны шутки,<br/>

		Приговорки, прибаутки,<br/>

		Небылицы, былины<br/>

		Православной старины!..<br/>

		Слушать, так душе отрадно.<br/>

		И не пил бы и не ел,<br/>

		Всё бы слушал да сидел.<br/>

		Кто придумал их так ладно?<br/>

		Стариков когда-нибудь<br/>

		(Жаль, теперь нам не досужно)<br/>

		Надо будет помянуть -<br/>

		Помянуть и этих нужно...<br/>

		Слушай, сват, начну первой,<br/>

		Сказка будет за тобой.<br/>

    ]]></string>

	</resources>
  








Строки, содержащие разметку, должны иметь аттрибут **formatted** со значением **false**, а содержимое должно передаваться в блоке **CDATA**, чтобы у компилятора не было претензий к разметке и специальным символам. В данном примере признаком статьи будет префикс **article_** в названии строки.





Также замечен странный глюк, проявляющийся в том, что если текст начинается с тега, то заканчивается он этим же тегом. Если у вас в начале статьи ссылка, советую ставить перед ней либо пробел, либо`  
`.





Изображения могут быть формата jpg, png или gif без анимации. Анимированный gif отображается статичной картинкой. Расположение стандартное для ресурсов, для дисплеев разной плотности можно подготовить свой вариант картинки. В данном примере все изображения находятся в **drawable-nodpi**





![](http://habrastorage.org/storage2/a2c/313/a58/a2c313a588ebcbd9f6d257576518d942.png)





### Как всё работает





Рассмотрим некоторые части кода подробно.

	public void onCreate(Bundle savedInstanceState) {

    super.onCreate(savedInstanceState);

    setContentView(R.layout.activity_main);

    

    tvContent = (TextView)findViewById(R.id.tvContent);

    tvContent.setLinksClickable(true);

    tvContent.setMovementMethod(new LinkMovementMethod());

    

    setArticle("article_main");

	} 
  




**TextView** используемый нами в качестве браузера, требует особой инициализации:





**tvContent.setLinksClickable(true);** указывает на то, что ссылки в данном элементе реагируют на нажатие.





**tvContent.setMovementMethod(new LinkMovementMethod());** назначает способ навигации по элементу. Использованный нами [**LinkMovementMethod**](http://developer.android.com/reference/android/text/method/LinkMovementMethod.html) интересен сам по себе и, возможно, заслуживает отдельной статьи. Я лишь скажу, что при необходимости более полного контроля можно создать его наследника, переопределенные методы которого позволят отслеживать все действия со ссылками в элементе.



	void setArticle(String strArticleResId) {

    int articleResId = getResources().getIdentifier(strArticleResId, "string", getPackageName());

    String text = getString(articleResId);

    if (text == null) text = "Article not found";

    

    Spanned spannedText = Html.fromHtml(text, htmlImageGetter, htmlTagHandler);

    Spannable reversedText = revertSpanned(spannedText);

    

    tvContent.setText(reversedText);

	} 
  




В данном методе происходит получение строки по идентификатору из строковых ресурсов, её преобразование из HTML в специальный объект **Spanned**, затем ещё одно преобразование в **Spannable** и установка в **TextView** в качестве содержимого. Всё это кажется довольно громоздким, но тому есть причины.





В **TextView**, на мой взгляд, странный порядок обработки спанов — с конца списка. При естественном расположении спанов после преобразования строки из HTML, изменения внешнего вида вложенных спанов перекрываются свойствами спанов, их содержащих. Для нормального отображения приходится буквально выворачивать маркировку наизнанку с помощью метода **revertSpanned**:



	final Spannable revertSpanned(Spanned stext) {

    Object[] spans = stext.getSpans(0, stext.length(), Object.class);

    Spannable ret = Spannable.Factory.getInstance().newSpannable(stext.toString());

    if (spans != null && spans.length > 0) {

        for(int i = spans.length - 1; i >= 0; --i) {

            ret.setSpan(spans[i], stext.getSpanStart(spans[i]), stext.getSpanEnd(spans[i]), stext.getSpanFlags(spans[i]));

        }

    }



    return ret;

	}  
  




Определение обработчика ссылок на изображения минималистично и призвано загружать только картинки из ресурсов. Поскольку мы рассматриваем вариант справочной системы, я посчитал, что этого будет достаточно. С вашего позволения, я не буду цитировать его. Если вы хотите большего, можно обратиться, например, к [данной статье](http://habrahabr.ru/post/155879/).





Более интересен нам будет **Html.TagHadler**:



	Html.TagHandler htmlTagHandler = new Html.TagHandler() {

    public void handleTag(boolean opening, String tag, Editable output,	XMLReader xmlReader) {

        Object span = null;

        if (tag.startsWith("article_")) span = new ArticleSpan(MainActivity.this, tag);

        else if ("title".equalsIgnoreCase(tag)) span = new AppearanceSpan(0xffff2020, AppearanceSpan.NONE, 20, true, true, false, false);

        else if (tag.startsWith("color_")) span = new ParameterizedSpan(tag.substring(6));

        if (span != null) processSpan(opening, output, span);

    }

	};
  




Здесь у нас происходит несколько интересных вещей.





При преобразовании из **HTML** в **Spanned** методом **Html.fromHtml**, обрабатываются тэги 





`br`, `p`, `div`, `em`, `b`,`strong`, `cite`, `dfn`, `i`, `big`, `small`, 





`font`, `blockquote`, `tt`, `a`, `u`, `sup`, `sub`, `h1...h6` и `img`.





В случае, если тэг не опознан, вызывается Html.TagHandler (если, конечно, он передан в вызов).





Мы проверяем, не является ли переданный тэг «нашим» и если это так, создаём соответствующий **Span** — элемент разметки, а затем накладываем его на текст. Я создал несколько собственных **Span**-ов, они будут рассмотрены далее. Как правило, **Span**-ы наследуются от [**android.text.style.CharacterStyle**](http://developer.android.com/reference/android/text/style/CharacterStyle.html).





К сожалению, у меня не получилось малой кровью добиться центрования отдельных строк или абзацев, а встроенной возможности для этого не существует. Также, нельзя прочесть атрибуты тэга из **xmlReader**, поскольку он реализован не полностью. По этой причине пришлось изобретать свой способ передачи параметров: значение является частью тега. В нашем примере таким образом передаётся значение цвета в тэге **color**, преобразовываемом в **ParameterizedSpan**. Получается что-то вроде``





<color_ffff0000>красный. Это достаточно ограниченный и не очень удобный способ, но иногда лучше такой, чем никакого.



	void processSpan(boolean opening, Editable output, Object span) {

        int len = output.length();

        if (opening) {

            output.setSpan(span, len, len, Spannable.SPAN_MARK_MARK);

        } else {

            Object[] objs = output.getSpans(0, len, span.getClass());

            int where = len;

            if (objs.length > 0) {

                for(int i = objs.length - 1; i >= 0; --i) {

                    if (output.getSpanFlags(objs[i]) == Spannable.SPAN_MARK_MARK) {

                        where = output.getSpanStart(objs[i]);

                        output.removeSpan(objs[i]);

                        break;

                    }

                }

            }

            

            if (where != len) {

                output.setSpan(span, where, len, Spannable.SPAN_EXCLUSIVE_EXCLUSIVE);

            }

        }

    } 
  




Этот код делает следующее: В случае, если передан открывающий **Span**, он добавляется к концу строки в текущем её виде. В случае, если **Span** закрывающий, мы находим в строке его открывающий аналог, запоминаем его положение, затем удаляем и добавляем новый, но уже с информацией о начальном положении и длине. 





Мы завершили рассмотрение класса Activity, являющегося основным модулем нашего приложения. Теперь рассмотрим вспомогательные классы.



	package com.example.markup.tutorial;



	import android.text.TextPaint;

	import android.text.style.CharacterStyle;



	public class AppearanceSpan extends CharacterStyle {

    

    public static final int NONE = -1;

    

    final int color, bgColor, textSize;

    final boolean boldText, italicText, strikeThruText, underlineText;

    

    public AppearanceSpan(int color, int bgColor, int textSize, boolean boldText, boolean italicText, 

            boolean strikeThruText, boolean underlineText) {

        this.color = color;

        this.bgColor = bgColor;

        this.textSize = textSize;

        this.boldText = boldText;

        this.italicText = italicText;

        this.strikeThruText = strikeThruText;

        this.underlineText = underlineText;

    }

    

    @Override

    public void updateDrawState(TextPaint tp) {

        if (color != NONE) tp.setColor(color);

        if (bgColor != NONE) tp.bgColor = bgColor;

        tp.setFakeBoldText(boldText);

        tp.setStrikeThruText(strikeThruText);

        if (textSize != NONE) tp.setTextSize(textSize);

        tp.setUnderlineText(underlineText);

        tp.setTextSkewX(italicText ? -0.25f : 0);

    }

	}
  




Это Span общего назначения и с его помощью можно задать большинство параметров стиля текста. Его можно использовать как базу для создания стилей текста из собственных тэгов.







	package com.example.markup.tutorial;

	import android.text.style.ClickableSpan;

	import android.view.View;



	public class ArticleSpan extends ClickableSpan {



    final MainActivity activity;

    final String articleId;

    

    public ArticleSpan(MainActivity activity, String articleId) {

        super();

        this.activity = activity;

        this.articleId = articleId;

    }

    

    @Override

    public void onClick(View arg0) {

        activity.setArticle(articleId);

    }

	}







Этот класс описывает элемент, который по нажатию на него обеспечивает переход к статье, чей идентификатор является его параметром. Здесь я применил производное от способа, описанного мной ранее: сам тэг является собственным параметром, а его класс определяется префиксом article_. Поднимемся выше, к описанию **Html.TagHandler**:








	if (tag.startsWith("article_")) span = new ArticleSpan(MainActivity.this, tag);






Обработчик тэгов, увидев тэг, начинающийся на article_, создаёт ArticleSpan, задавая ему в качестве параметра название тэга. Элемент, при нажатии на него, вызывает метод MainActivity.setArticle, после чего в TextView устанавливается новый текст.







	package com.example.markup.tutorial;


	import android.graphics.Color;

	import android.text.TextPaint;

	import android.text.style.CharacterStyle;



	public class ParameterizedSpan extends CharacterStyle {



    int color = 0;

    

    public ParameterizedSpan(String param) {

        try {

            color = Color.parseColor("#" + param);

        } catch(Exception ex) { }

    }



    @Override

    public void updateDrawState(TextPaint tp) {

        tp.setColor(color);

    }

	}




Здесь реализован элемент, получающий параметр явно и отдельно от своего имени. Претензия на своего рода стандарт именования тэгов, раз уж нельзя передавать атрибуты. 





Конечно, всё описанное является вариациями одного принципа, каждый выберёт то, что ему удобнее.





### Вызов Activity





Здесь всё очень просто. Вызов осуществляется посредством использования обычного тэга `` с заданием схемы и хоста, которые описаны в **AndroidManifest.xml** для вызываемой **Activity**.





В HTML мы видим следующее:

	<a href="activity-run://AnotherActivityHost?caption=Another%20Activity&text=Hello%20from%20markup!">Вызвать другую Activity</a> 
  




При нажатии на ссылку, происходит вызов AnotherActivity с передачей параметров в Intent. Эти параметры можно получить и использовать:

	Uri uri = getIntent().getData();
	String caption = uri.getQueryParameter("caption");
	String text = uri.getQueryParameter("text");
  




### Использованные материалы





Следующие материалы очень ускорили создание данной статьи, да и, чего уж там, сделали его вообще возможным:





[www.sherif.mobi/2011/09/html-and-activity-links-in-textview.html](http://www.sherif.mobi/2011/09/html-and-activity-links-in-textview.html)  
[stackoverflow.com/questions/3874999/alignment-in-html-fromhtml](http://stackoverflow.com/questions/3874999/alignment-in-html-fromhtml)  
[stackoverflow.com/questions/11865334/how-to-use-xmlreader-in-taghandler-handletag](http://stackoverflow.com/questions/11865334/how-to-use-xmlreader-in-taghandler-handletag)  
[stackoverflow.com/questions/4044509/android-how-to-use-the-html-taghandler](http://stackoverflow.com/questions/4044509/android-how-to-use-the-html-taghandler)  
[stackoverflow.com/questions/1792604/html-imagegetter](http://stackoverflow.com/questions/1792604/html-imagegetter)





Я очень рад, что существует на свете [StackOverflow.com](http://stackoverflow.com/).





Надеюсь статья вам пригодится и вы теперь не будете плодить много елементов.





Рабочий код на странице с [кодами](http://android-helper.com.ua/codes/).




