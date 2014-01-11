---
author: andrew
comments: true
date: 2013-01-02 21:38:26+00:00
layout: post
slug: fragment-animation
title: Анимация фрагментов в Android
wordpress_id: 1105
categories:
- Android для начинающих
- Android для профессионалов
- Программирование
---

Привет друзья.





Собирался написать статью по анимации фрагментов, но к счастью она уже написана.





Думаю вам понравится.

 <!-- more -->



Всем доброго времени суток. Этот пост хочу посвятить теме фрагментов для Android. На Хабре есть уже переводы и некоторые статьи, которые упоминают о том, как начать работать с фрагментами под Android. Например, статья [Fragments API в Android 3.0](http://habrahabr.ru/post/113196/). В ней находится описание того, что такое фрагменты и в какой версии Android они доступны, поэтому те, кто ещё не добрался до неё могут при желании ознакомиться, я же не буду пересказывать этого в своём посте. Поэтому сразу перейду к делу.   
  
  






#### Начало работы





  
Скажу только кратко, что фрагменты — это компоненты UI пользователя, которые могут использоваться с помощью класса Activity для отображение пользовательских данных, но их жизненный цикл от него не зависит. Функционал, которым наделены фрагменты имеет более широкий функционал для работы с ними, чем Activity, поэтому их использование для разработчиков имеет не малое значение, если они хотят, чтобы их приложение имело более современный по нынешним нормам интерфейс пользователя.  
  
Теперь перейдём к сути поста. Разработчиками Google фрагменты были наделены, на мой взгляд, отличной поддержкой анимации отображения самого фрагмента. Об этом и пойдёт далее речь. Я искал по Хабру посты на данную тематику, но так ничего и не нашёл, поэтому сейчас поделюсь своими знаниями.   
  






#### Создаём проект





  
Давайте создадим небольшой проект. Я создал проект под свой Samsung Nexus S, там у меня стоит версия Android 4.1.2, собственно её я и использовал (Api Level 16). Сам проект я назвал FragmentsAnimationTest.  
  
Для демонстрации нам понадобится главное активити и его лейаут, пара фрагментов, каждый также со своим лейаутом и ещё пара xml-файлов для самой анимации, о которых я расскажу позже.  
  
Приложение будет выглядеть следующим образом: на экране будет отображаться один из фрагментов, переключение между ними будет осуществляться с помощью обычной кнопки, и, соответственно, само переключение фрагментов будет сопровождаться анимационными эффектами.   
  
Сначала расположим элементы главного активити в файле activity_main.xml:







	<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"

    xmlns:tools="http://schemas.android.com/tools"

    android:layout_width="match_parent"

    android:layout_height="match_parent"

    tools:context=".MainActivity" >



    <FrameLayout

        android:id="@+id/fragCont"

        android:layout_width="wrap_content"

        android:layout_height="match_parent"

        android:layout_weight="1" >

    </FrameLayout>

    

    <Button

        android:id="@+id/btn"

        android:layout_width="wrap_content"

        android:layout_height="wrap_content"

        android:text="SWITCH FRAGMENTS" 

        android:layout_toRightOf="@id/fragCont"/>



	</RelativeLayout>





  
  
Из кода видно, что используется главный лейаут — RelativeLayout, достаточно удобный при работе с фрагментами, в него помещаются два стандартных элемента FrameLayout — собственно, он и будет являться контейнером для фрагментов и кнопка, которой будем переключать фрагменты между собой. Пока что всё должно быть предельно просто.  
  
Дальше займёмся нашими фрагментами. Создадим для них разметки и сами классы:  
fragment1.xml 




	<?xml version="1.0" encoding="utf-8"?>

	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

    android:layout_width="match_parent"

    android:layout_height="match_parent"

    android:orientation="vertical" 

    android:background="#5A4">

    

    <TextView 

        android:layout_width="200dp"

        android:layout_height="fill_parent"

        android:text="fragment 1"/>



	</LinearLayout>



  
  
fragment2.xml



	<?xml version="1.0" encoding="utf-8"?>

	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

    android:layout_width="match_parent"

    android:layout_height="match_parent"

    android:orientation="vertical" 

    android:background="#A55">

    

    <TextView 

        android:layout_width="200dp"

        android:layout_height="fill_parent"

        android:text="fragment 2"/>



	</LinearLayout>



  
  
Для обоих фрагментов код практически одинаков, отличаются только текстом, который будет отображаться в самом фрагменте для его идентификации и цветом фона, чтобы хорошо было видно анимацию.  
  
Fragment1.java



	public class Fragment1 extends Fragment {



    @Override

    public View onCreateView(LayoutInflater inflater, ViewGroup container, 

                                Bundle savedInstanceState) {

        return inflater.inflate(R.layout.fragment_1, null);

    }

	}





  
  
Fragment2.java



	public class Fragment2 extends Fragment{



    @Override

    public View onCreateView(LayoutInflater inflater, ViewGroup container, 

                                Bundle savedInstanceState) {

        return inflater.inflate(R.layout.fragment_2, null);

    }

	}




  
  
В классах также должно быть всё понятно, если знакомы с темой фрагментов. В них просто указывается какой именно леаут будет использоваться при отображении конкретного фрагмента и всё.  
  
Теперь приступим к самому вкусному. Поработаем с классом главной активити, вот её код:


	public class MainActivity extends Activity {



    private Fragment fragment2;

    private Fragment fragment1;

    private FragmentTransaction ft;



    @Override

    protected void onCreate(Bundle savedInstanceState) {

    super.onCreate(savedInstanceState);

    setContentView(R.layout.activity_main);

    

    fragment1 = new Fragment1();

    fragment2 = new Fragment2();

    ft = getFragmentManager().beginTransaction();

    ft.setCustomAnimations(R.animator.slide_in_left, R.animator.slide_in_right);

//	ft.setTransition(FragmentTransaction.TRANSIT_FRAGMENT_OPEN);

    

    ft.replace(R.id.fragCont, fragment1);

    ft.addToBackStack(null);

    

    ft.commit();

    

    Button btn = (Button) findViewById(R.id.btn);

    btn.setOnClickListener(new OnClickListener() {

        

        @Override

        public void onClick(View v) {

        ft = getFragmentManager().beginTransaction();

        ft.setCustomAnimations(R.animator.slide_in_left, R.animator.slide_in_right);

//		ft.setTransition(FragmentTransaction.TRANSIT_FRAGMENT_OPEN);

        if(fragment1.isVisible()){

            ft.replace(R.id.fragCont, fragment2);

        }else{

            ft.replace(R.id.fragCont, fragment1);

        }

        ft.commit();

        }

    });

    }

	}




  
  
Разберём что именно происходит в нашей активити. Сначала создаются оба фрагмента, которые, как уже говорилось, будут поочерёдно менять друг друга. Далее указываем строкой ft = getFragmentManager().beginTransaction() получаем FragmentTransaction, с помощью которой мы сможем взаимодействовать с нашими фрагментами, но это всё есть в статье, которую я указывал ранее. Прежде, чем перейти к разбору следующего кода, сделаю небольшое отступление.   
  
Существует две возможности создавать анимацию для отображения фрагментов: 








  * 1ый способ — это подключение стандартной анимации с помощью метода setTransition(int transit). В классе FragmentTransaction есть несколько уже предописанных анимаций.



  * 2ой способ — это именно то, что нас интересует в данной теме, реализация кастомной анимации. Осуществляется с помощью метода setCustomAnimations()






  
Теперь вернёмся к разбору нашего кода. Посмотрим на следующую строку ft.setCustomAnimations(R.animator.slide_in_left, R.animator.slide_in_right), тут происходит подключение анимации ко всем фрагментам, с которыми будет происходить работа до завершения транзакции, до строки ft.commit(). Что же именно происходит? В setCustomAnimations передаётся два параметра. Первый параметр описывает анимацию, для отображения фрагмента, который появляется, а второй — описывает анимацию для фрагмента, который сменяется, т.е. убирается с экрана устройства. Важно упомянуть, что данный метод следует вызвать до появления фрагментов, в противном случае, анимация не будет применена.  
  
Пара сток-комментариев — это для того, чтобы можно было попробовать поиграться с предописанной анимацией, достаточно их раскомментировать и закомментировать предыдущую строку — ft.setCustomAnimations(R.animator.slide_in_left, R.animator.slide_in_right), в обоих случаях, хотя это и необязательно.  
  
Давайте разберём код активити до конца и перейдём к созданию самой анимации.  
  
После установки анимации, происходит показ фрагмента, добавление его в стек и завершение транзакции для отображения всех изменений. Потом инициализируем нашу кнопку и прикрепляем к ней слушатель события нажатия кнопки, внутри которого находится код для смены фрагментов, по нажатию кнопки начинаем транзакцию, подключаем анимацию и меняем фрагмент на противоположный показываемому в настоящий момент. Код прост, поэтому глубокого объяснения не требует.  
  






#### Создаём анимацию





  
Перейдём к главной части нашей темы. Научимся создавать саму анимацию. Способ создания анимации здесь немного отличается от того, как мы привыкли это делать в ранних версиях Android. Реализация проходит следующим образом. Для на чала нужно создать папку animator в папке ресурсов приложения, это будет выглядеть так — res/animator/. Сюда мы должны положить xml-файлы, которые будут описывать как именно должна проигрываться анимация. Поместим их туда:  
slide_in_left.xml



	<?xml version="1.0" encoding="utf-8"?>

	<set xmlns:android="http://schemas.android.com/apk/res/android">

  <objectAnimator xmlns:android="http://schemas.android.com/apk/res/android"

    android:interpolator="@android:anim/accelerate_decelerate_interpolator"

    android:propertyName="y" 

    android:valueType="floatType"

    android:valueTo="0" 

    android:valueFrom="-1280"

    android:duration="1500"/>

	</set>


  
  
и slide_in_right.xml


	<?xml version="1.0" encoding="utf-8"?>

	<set xmlns:android="http://schemas.android.com/apk/res/android"

     android:ordering="together">

  

  	<objectAnimator 

      android:interpolator="@android:anim/accelerate_interpolator"

      android:propertyName="alpha"

      android:valueType="floatType"

      android:valueTo="0"

      android:duration="300"/>

    

  	<objectAnimator xmlns:android="http://schemas.android.com/apk/res/android"

    android:interpolator="@android:anim/accelerate_decelerate_interpolator"

    android:propertyName="x" 

    android:valueType="floatType"

    android:valueTo="1280" 

    android:valueFrom="0"

    android:duration="1500"/>  

  

	</set>




  
  
Теперь подробно их разберём. Элементы самих визуальных эффектов описываются в теге objectAnimator, каждый такой тег говорит об описании нового эффекта анимации. Теперь посмотрим на сами атрибуты. Первый атрибут в файле slide_in_left.xml — это interpolator, у него имеется несколько значений, о них можно более подробно узнать из документации [Property Animation](http://developer.android.com/guide/topics/graphics/prop-animation.html#object-animator). Interpolator отвечает за то, чтобы отобразить определённым способом в течении определённого времени наш фрагмент. Далее у нас следует атрибут propertyName, в нём указывается с какое именно свойство фрагмента мы будем изменять при анимации, в нашем примере первым идёт y, а valueType указывает какого типа у нас именяемый параметр. В книге Pro Android 4 аргументируется эта ситуация тем, что если посмотреть на метод setX() в классе View, то станет понятно, что он принимает значение типа float, с методом setY() дело обстоит также, отсюда и значение floatType.   
  
Далее идут не маловажные атрибуты valueFrom и valueTo, они указывают от какого до какого значания изменять значение указаннное в propertyName, в нашем первом случае это y. Если параметр valueFrom не указан, то значение берётся равное текущему. В нашем случае valueFrom равен -1280, это означает, что движение фрагмента по оси y будет начинаться со значения -1280, это значение было выбрано из-за того, что оно находится за пределами экрана устройства и перемещение будет происходить пока значение y не станет равным 0 для верхнего левого угла нашего фрагмента в течении 1500 миллисекунд. И, наконец, duration — атрибут указывает сколько именно будет длиться наш анимированный эффект в миллисекундах.  
  
И последний нюанс, который я хочу описать. Глядя в какой-либо из файлов описания анимации, можно заметить тэг set, в который помещены все эффекты анимации, он служит для объединения эффектов либо их разделения. В файле slide_in_right.xml используется атрибут ordering в теге set, в нашем случае он имеет значение together, что означает проигрывать эффекты одновременно, в противовес ему существует значение sequentially, которое требует последовательного отображения эффектов в анимации, что очень удобно в некоторых случаях.  
  
Собственно и всё. В файле slide_in_right.xml приведен пример как можно использовать другие свойства для анимирования, например alpha-канал. Надеюсь данная статья пригодится тем, кому не всё равно как будет выглядеть его приложение.  
  
Как вы сами понимаете, уважаемые хабраюзеры, скрины не смогут отобразить результат работы.  
  
Литература и источники, которые использовались при написании поста были упомянуты в ходе самой статьи.





Статья из сайта [habrahabr.ru](http://habrahabr.ru/post/162389/)
