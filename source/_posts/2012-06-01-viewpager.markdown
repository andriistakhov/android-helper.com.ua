---
author: andrew
comments: true
date: 2012-06-01 07:48:06+00:00
layout: post
slug: '%d1%81%d0%bb%d0%b0%d0%b9%d0%b4%d0%b8%d0%bd%d0%b3-%d1%8d%d0%ba%d1%80%d0%b0%d0%bd%d0%be%d0%b2-%d1%81-%d0%bf%d0%be%d0%bc%d0%be%d1%89%d1%8c%d1%8e-viewpager'
title: Слайдинг экранов с помощью ViewPager
wordpress_id: 300
categories:
- Android для профессионалов
- Программирование
---

Всем привет.





Я долго думал как же мне поскорее помочь вам разобраться в android. И решил, что я буду собирать очень интересные статьи на своем сайте.





Сегодня речь пойдет о горизонтальной прокрутке экранов. (статья из [Хабрахабр](http://habrahabr.ru/post/131889/))

<!-- more -->



##### Вступление





Горизонтальная прокрутка экранов — удобная и модная вещь. На Хабре уже был [топик ](http://habrahabr.ru/blogs/android_development/118482/), посвященный данной теме. Однако использование ViewFlipper не позволяет с легкостью добиться эффекта привязки, когда экраны двигаются вместе с пальцем. Также нужен механизм автоматической доводки экранов в ту или иную сторону. Примера, где это было бы хорошо разъяснено и реализовано, так и не нашел. В комментариях [предлагали](http://habrahabr.ru/blogs/android_development/118482/#comment_3863174) посмотреть исходники гугловского кода, где это реализовано. Однако удобнее было бы воспользоваться готовым решением. Такой механизм реализован в Android [Support Package ](http://developer.android.com/sdk/compatibility-library.html). Использовать эту библиотеку можно для версий Android 1.6 и старше. Конкретно нам понадобятся классы ViewPager и PagerAdapter.





##### Предварительная настройка





Чтобы подключить библиотеку к проекту, выполняем следующие действия.  
Идем в директорию, куда установлен Android SDK, запускаем SDK Manager и в списке доступных к установке компонентов выбираем Android Support package. Устанавливаем его. Все что нужно будет находиться в следующей директории: <директория с sdk>/extras/android/support/. В папке v4 будет лежать .jar файл, который и нужно подключить к проекту. Если вы работаете в Eclipse, то в Package Explorer щелкаем правым кликом по проекту, выбираем Build Path -> Add External Archives. В появившемся диалоговом окне переходим к скачанному .jar файлу, выбираем его и щелкаем OK. Всё, библиотека подключена к проекту и готова к использованию.  
Хочется заметить, что могут возникнуть проблемы, если вы используете старую версию SDK Manager. У меня указанный Support Package не появлялся в списке доступных загрузок. После обновления SDK проблема решилась.





##### Разработка





Выполнив инструкции, приведенные по указанной выше ссылке, и установив библиотеку, можно приступать к реализации поставленной задачи.  
ViewPager по принципу работы похож на ListView. Данные для отображения он берет из PagerAdapter. Нам потребуется унаследовать свой класс от PagerAdapter и реализовать в нем некоторые методы. Добавление и удаление экранов реализуется с помощью методов instantiateItem() и destroyItem() соответственно. View для отображения можно создавать прямо в адаптере. Такой подход хорош тем, что ViewPager можно настраивать так, чтобы в адаптере не хранились все экраны сразу. По умолчанию адаптер хранит текущий экран, и по одному слева и справа от него. Это может сэкономить память, если содержание экранов слишком сложное. Однако в данном примере создадим все экраны в activity приложения, добавим их в список, и передадим затем этот список адаптеру. Адаптер же не будет сам создавать view, а будет брать их их списка.  
Код адаптера получился следующим. 








	public class SamplePagerAdapter extends PagerAdapter{

    

    List<View> pages = null;

    

    public SamplePagerAdapter(List<View> pages){

        this.pages = pages;

    }

    

    @Override

    public Object instantiateItem(View collection, int position){

        View v = pages.get(position);

        ((ViewPager) collection).addView(v, 0);

        return v;

    }

    

    @Override

    public void destroyItem(View collection, int position, Object view){

        ((ViewPager) collection).removeView((View) view);

    }

    

    @Override

    public int getCount(){

        return pages.size();

    }

    

    @Override

    public boolean isViewFromObject(View view, Object object){

        return view.equals(object);

    }



    @Override

    public void finishUpdate(View arg0){

    }



    @Override

    public void restoreState(Parcelable arg0, ClassLoader arg1){

    }



    @Override

    public Parcelable saveState(){

        return null;

    }



    @Override

    public void startUpdate(View arg0){

    }

	}









В адаптере нам потребовалось определить свою реализацию для методов instantiateItem(), destroyItem(), getCount() и isViewFromObject(). Для остальных методов тела можно оставить пустыми.  
  
Создадим файл разметки page.xml и опишем в нем содержимое экрана следующим образом:









	<?xml version="1.0" encoding="utf-8"?>

	<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"

    android:layout_width="fill_parent"

    android:layout_height="fill_parent"

    android:padding="10dip"

    android:background="#ddd">



    <TextView

        android:id="@+id/text_view"

        android:layout_width="fill_parent"

        android:layout_height="fill_parent"

        android:layout_centerInParent="true"

        android:gravity="center"

        android:background="#888"

        android:textColor="#fff"

        android:textSize="30dip"/>

	</RelativeLayout>












В activity приложения, как было указано выше, создадим несколько View, которые и будут нашими экранами, добавим их в список и передадим список адаптеру. Затем создадим ViewPager и установим для него полученный адаптер.  
  
Код activity:












	public class ViewPagerSampleActivity extends Activity {

    /** Called when the activity is first created. */

    @Override

    public void onCreate(Bundle savedInstanceState) {

        super.onCreate(savedInstanceState);

        

        LayoutInflater inflater = LayoutInflater.from(this);

        List<View> pages = new ArrayList<View>();

        

        View page = inflater.inflate(R.layout.page, null);

        TextView textView = (TextView) page.findViewById(R.id.text_view);

        textView.setText("Страница 1");

        pages.add(page);

        

        page = inflater.inflate(R.layout.page, null);

        textView = (TextView) page.findViewById(R.id.text_view);

        textView.setText("Страница 2");

        pages.add(page);

        

        page = inflater.inflate(R.layout.page, null);

        textView = (TextView) page.findViewById(R.id.text_view);

        textView.setText("Страница 3");

        pages.add(page);

        

        SamplePagerAdapter pagerAdapter = new SamplePagerAdapter(pages);

        ViewPager viewPager = new ViewPager(this);

        viewPager.setAdapter(pagerAdapter);

        viewPager.setCurrentItem(1);     

        

        setContentView(viewPager);

    }

	}













Как видно из кода, мы создали три экрана. Для каждого из них мы указали какой он по счету. Установив адаптер для ViewPager, укажем что сейчас будет показываться экран с индексом 1, т.е. на котором написано «Страница 2».   
Скриншот приложения, во время прокручивания экрана:









![](http://android-helper.com.ua/images/uploads/2012/06/Image.png)












##### Заключение



Как видно из примера, использовать ViewPager и PagerAdapter достаточно легко. При слайдинге экранов, изображения привязывается к положению пальца. Работает автоматическая доводка в какую либо из сторон. Нажатия на дочерние элементы (различные кнопки, элементы списка, если таковой присутствует) обрабатываются корректно.  
  
И в заключение, ссылка на [статью ](http://android-developers.blogspot.com/2011/08/horizontal-view-swiping-with-viewpager.html), в которой рассказывается о ViewPager.




