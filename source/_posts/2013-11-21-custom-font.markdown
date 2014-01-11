---
author: andrew
comments: true
date: 2013-11-21 12:47:23+00:00
layout: post
slug: custom-font
title: Custom Font - свои шрифты в android
wordpress_id: 1725
categories:
- Android для начинающих
- Android для профессионалов
- Памятка разработчику
- Программирование
- Что такое android
---

Друзья всем привет.

Я очень рад, что некоторые из ребят в коучинге пишут замечательные топики!

Вот с какой проблемой мы столкнулись в коучинге и как ее решали!

<!-- more -->


В процессе коучинга, создавая своё первое приложение - калькулятор, научился использовать кастомные шрифты. Спешу безвозмездно поделиться своим опытом!

**Какие шрифты подходят?**

Можно использовать шрифты TrueType (TTF)

**Куда положить шрифт?**

В корневой директории (на одном уровне с директориями java и res и файлом AndroidManifest.xml) создать папку assets, а внутри неё можно создать отдельную папку для шрифтов fonts
В эту папку бросить свой файл со шрифтом Custom.ttf

**Как Android узнает, где находятся кастомные шрифты?**

В файле **<project_name>.iml** в разделе configuration необходимо добавить строчку

	<configuration>
	...
	<option name="ASSETS_FOLDER_RELATIVE_PATH" value="/src/	main/assets" />
	</configuration>

а в файле **res/values/strings.xml** - строчку

	<string name="digit_keyboard_font">fonts/Custom.ttf</string>

**Как назначить шрифт своим View-элементам?**

Теперь в коде, в методе onCreate можно использовать свой шрифт. Обратите внимание на код, в котором скрывается первая секретная фишка!

	Typeface keys = Typeface.createFromAsset(getAssets(), 	getString(R.string.digit_keyboard_font));
	TextView key = (TextView)findViewById(R.id.button_about_ok);
	key.setTypeface(keys);


Как видите, таким способом можно применить шрифт к надписям на кнопках и других **View**, которые можно явно привести типу **TextView**.


Ещё одна супер-полезная и очень секретная фишка
Допустим, требуется применить шрифт к группе элементов, которые располагаются в layout-файле внутри корневого элемента типа **ViewGroup** с присвоенным ему уникальным **id**


	<LinearLayout
    android:id="@+id/digit_keyboard_layout"
    android:layout_width="wrap_content"
    android:layout_height="match_parent"
    android:layout_gravity="center_horizontal"
    android:orientation="vertical">

    <TableLayout
        android:id="@+id/table1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content">

        <TableRow
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="3">

            <Button
                android:id="@+id/button_one"
                style="@style/calc_button_style"
                android:layout_width="@dimen/button_width"
                android:text="@string/one"
                android:layout_alignParentStart="false"
                android:layout_alignParentEnd="false"
                android:layout_weight="1"/>

            <Button
                android:id="@+id/button_two"
                style="@style/calc_button_style"
                android:layout_width="@dimen/button_width"
                android:text="@string/two"
                android:layout_weight="1"/>

            <Button
                android:id="@+id/button_three"
                android:text="@string/three"
                style="@style/calc_button_style"
                android:layout_width="@dimen/button_width"
                android:layout_weight="1"
                />
        </TableRow>
    </TableLayout>
	</LinearLayout>


Используйте в коде приложения вот такой метод


	// Sets the font on all TextViews in the ViewGroup.
    public void setFont(ViewGroup group, Typeface font) {
        int count = group.getChildCount();
        View v;
        for(int i = 0; i < count; i++) {
            v = group.getChildAt(i);
            if(v instanceof TextView) {
                ((TextView)v).setTypeface(font);
            } else if(v instanceof ViewGroup) {
                setFont((ViewGroup) v, font);
            }
        }
    }

Метод рекурсивно вызывает сам себя пока не доберётся до нужного элемента в любой сложной xml-структуре **layout**. 

Вызывая этот метод устанавливаем шрифт группе элементов.

	Typeface keys = Typeface.createFromAsset(getAssets(), 	getString(R.string.digit_keyboard_font));
	ViewGroup keyboardArea = (ViewGroup)findViewById(R.id.digit_keyboard_layout);
	setFont(keyboardArea, keys);

У кого есть какие то наработки или идеи, пишите в комментариях. Мы обязательно добавим статью на блог!

Жду ваших комментариев!