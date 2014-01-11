---
author: andrew
comments: true
date: 2012-09-15 20:04:12+00:00
layout: post
slug: android-html
title: Android HTML - отображение в TextView
wordpress_id: 613
categories:
- Android для начинающих
- Android для профессионалов
- Памятка разработчику
- Программирование
---

Всем привет.





С вами снова я. И сегодня мы поговорим, как показывать HTML код в TextView.





Если все готовы. Тогда приступим.


<!-- more -->


Вам необходимо вызвать функцию:







	TextView txtView = new TextView(this);

	Spanned s = Html.fromHtml("Android - это супер <font color=\"red\">android-helper.com.ua</font>");

	txtView.setText(s);









Вот полный код:






	public class TestActivity extends Activity {

 

    @Override

    public void onCreate(Bundle savedInstanceState) {

        super.onCreate(savedInstanceState);

        TextView txtView = new TextView(this);

        Spanned s = Html.fromHtml("Android - это супер <font color=\"red\">android-helper.com.ua</font>");

        txtView.setText(s);

 

        setContentView(txtView);

    }

	}







Все готово. Теперь можете наслаждаться.





Но нужно помнить, что так можно отформатировать не все HTML теги.





Вот список тегов, которые поддерживает класс **Html**.








  * a href="...",



  * b,



  * big,



  * blockquote,



  * br,



  * cite,



  * dfn,



  * div align="...",



  * em,



  * font size="..." color="..." face="...",



  * h1-h6,



  * i,



  * img src="...",



  * p,



  * small,



  * strike,



  * strong,



  * sub,



  * sup,



  * tt,



  * u



