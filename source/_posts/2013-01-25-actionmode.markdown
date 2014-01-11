---
author: andrew
comments: true
date: 2013-01-25 07:37:38+00:00
layout: post
slug: actionmode
title: ActionMode. А ты заменил контекстное меню?
wordpress_id: 1159
categories:
- Android для начинающих
- Android для профессионалов
- Программирование
---

Друзья всем привет.





Сегодня я бы хотел поведать вам о замечательном поведении в ActionBar.





C Android 3.0 в ActionBar добавили классное поведение для выбранных элементов.





Но давайте это посмотрим на примере.

 <!-- more -->



#### Задача





Дать возможность в ListView выбрать несколько элементов и например скопировать их названия. И эти названия вывести в диалог.





#### Результат урока





[![device-2013-01-25-092939](http://android-helper.com.ua/images/uploads/2013/01/device-2013-01-25-092939-180x300.png)](http://android-helper.com.ua/images/uploads/2013/01/device-2013-01-25-092939.png)[![device-2013-01-25-093344](http://android-helper.com.ua/images/uploads/2013/01/device-2013-01-25-093344-180x300.png)](http://android-helper.com.ua/images/uploads/2013/01/device-2013-01-25-093344.png)





#### [![device-2013-01-25-093353](http://android-helper.com.ua/images/uploads/2013/01/device-2013-01-25-093353-180x300.png)](http://android-helper.com.ua/images/uploads/2013/01/device-2013-01-25-093353.png)





####  





####  





####  





####  





####  





####  





####  





####  





####  





#### [![device-2013-01-25-093405](http://android-helper.com.ua/images/uploads/2013/01/device-2013-01-25-093405-180x300.png)](http://android-helper.com.ua/images/uploads/2013/01/device-2013-01-25-093405.png)





#### [![device-2013-01-25-093431](http://android-helper.com.ua/images/uploads/2013/01/device-2013-01-25-093431-180x300.png)](http://android-helper.com.ua/images/uploads/2013/01/device-2013-01-25-093431.png)





####  





####  





####  





####  





####  





####  





####  





####  





####  





#### Реализация





Создаем проект с минимальным значением android - 11.





Activity наследуем от ListActivity.



[java]public class MainActivity extends ListActivity {



    String[] data = { "one", "two", "three", "four", "five" };



    @Override

    protected void onCreate(Bundle savedInstanceState) {

        super.onCreate(savedInstanceState);

        prepareListView();

    }

[/java]  
  




Заполняем ListView



[java] private void prepareListView() {



        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this, android.R.layout.simple_list_item_activated_1,

                data);

        getListView().setAdapter(adapter);[/java]  
  




Создаем menu для action mode



[xml]<?xml version="1.0" encoding="utf-8"?>

<menu xmlns:android="http://schemas.android.com/apk/res/android" >



	<item

		android:id="@+id/copy"

		android:icon="@android:drawable/ic_menu_save"

		android:showAsAction="always|withText"

		android:title="@string/copy">

	</item>



</menu>[/xml]  
  




Добавляем обработчики для action mode и показываем диалог



[java]private final MultiChoiceModeListener callback = new MultiChoiceModeListener() {



        private final StringBuilder message = new StringBuilder();



        @Override

        public boolean onPrepareActionMode(ActionMode mode, Menu menu) {

            return false;

        }



        @Override

        public void onDestroyActionMode(ActionMode mode) {



        }



        @Override

        public boolean onCreateActionMode(ActionMode mode, Menu menu) {

            mode.getMenuInflater().inflate(R.menu.context, menu);

            return true;

        }



        @Override

        public boolean onActionItemClicked(ActionMode mode, MenuItem item) {

            if (item.getItemId() == R.id.copy) {

                AlertDialog.Builder builder = new AlertDialog.Builder(MainActivity.this);

                builder.setTitle("Action Mode").setMessage(message.toString()).create().show();

            }

            mode.finish();

            return false;

        }



        @Override

        public void onItemCheckedStateChanged(ActionMode mode, int position, long id, boolean checked) {

            if (checked) {

                String title = data[position];

                message.append("\n" + title);

            }

        }

    };[/java]  
  




Добавляем возможность вызова action mode из ListView и множественный выбор.



[java]//

        getListView().setChoiceMode(ListView.CHOICE_MODE_MULTIPLE_MODAL);

        //

        getListView().setMultiChoiceModeListener(callback);[/java]  
  




Все готово.





Для того что бы скачать исходники вам необходимо перейти на [страницу с исходниками](http://android-helper.com.ua/codes/)
