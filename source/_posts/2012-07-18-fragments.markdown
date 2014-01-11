---
author: andrew
comments: true
date: 2012-07-18 13:58:04+00:00
layout: post
slug: fragments
title: Fragments - описание
wordpress_id: 353
categories:
- Android для начинающих
- Android для профессионалов
- Программирование
---

Добрый день друзья.





Как и обещал выкладываю информацию о Fragments.





Пошарил немного по Интернету и нашел увлекательную статью на [Habrahabr.ru](http://habrahabr.ru/post/113196/)





Поэтому было решено вставить ее сюда полностью + со своей доработкой


<!-- more -->


_В Android 3.0 введена новая концепция фрагментов, которая служит для упрощения работы с интерфейсом под разные размеры экранов. Данный топик является переводом статьи с android-developers.blogspot.com. В ней рассмотрены преимущества фрагментов, а также приведен простой, но законченный пример приложения, работающего по этому принципу._





Важная цель Android 3.0 — упростить написание программ, которые должны масштабироваться в зависимости от размеров экрана. Для этого в платформе Android уже доступны следующие средства:








  * С самого начала, UI-фреймворк был спроектирован для использования layout managers, которые позволяют описывать интерфейс так, чтобы он «подгонялся» к размерам. Примером этого является ListView, высота которого изменяется в зависимости от размера экрана, который варьируется между соотношениями QVGA, HVGA, and WVGA.



  * В Android 1.6 была введена новая концепция плотности экрана, позволяющая приложениям легче масштабировать размеры между разными разрешениями, в то время как экраны имеют почти один и тот же физический размер. Разработчики незамедлительно начали использовать эту особенность, когда были введены телефоны с высоким разрешением, такие как Droid.



  * Также в Android 1.6 разработчикам стали доступны размеры экрана, при помощи их классификации: «small» для соотношения QVGA, «normal» для HVGA и WVGA, и «large» для более больших экранов. Разработчики могут использовать систему ресурсов для выбора между различными layout'ами, в зависимости от размера экрана






Комбинация layout managers и выбора различных ресурсов представляет долгий путь для создания масштабируемого интерфейса. В итоге многие приложения для хэндсетов не используют специальный интерфейс для планшетов, работающих под Honeycomb.





#### Введение во фрагменты





В Android 3.0 введена поддержка новых классов, которые помогают приложениям масштабировать их интерфейс, называемых фрагментами (Fragment). Фрагмент — это независимый компонент со своим интерфейсом и жизненным циклом; он может быть неоднократно использован в разных частях пользовательского интерфейса, в зависимости от нужного UI потока для определенного устройства или экрана.





В некотором смысле можно рассматривать фрагмент как мини-Activity, хотя он не может запускаться независимо и должен размещаться внутри Activity. На самом деле введение Fragment API дало нам возможность решать многие проблемы, с которыми сталкивались разработчики, при работе с Activity, так как полезность фрагмента простирается далеко за пределы обычных настроек для разных разрешений экрана:








  * Activity, вложенные через ActivityGroup были хорошей идеей, однако с ними было не так просто работать, так как Activity изначально разработан как независимый компонент. Fragment API — намного лучшее решение для этого, его стоит рассматривать как замену для вложенных Activity.



  * Сохранение данных для экземпляров Activity может быть выполнено через Activity.onRetainNonConfigurationInstance(), но это неуклюже само по себе и не очевидно. Фрагменты заменяют этот механизм, позволяя сохранять целый экземпляр фрагмента простой установкой флага.



  * Одна из конкретизаций фрагмента — DialogFragment, она упрощает отображение диалога, который управляется как часть жизненного цикла Activity. Он заменяет API Activity, используемые для управления диалогами.



  * Еще одна конкретизация фрагмента — ListFragment, она упрощает отображение списка данных. ListFragment похож на ListActivity (с несколькими дополнительными особенностями), его использование упростит отображение данных в списке.



  * Информация по всем фрагментам, прикрепленным к Activity, сохраняется фреймворком в сохраненное состояние той же Activity и восстанавливается при рестарте. Таким образом уменьшается количество сохраняемых состояний.



  * У фреймворка есть встроенная поддержка для управления стеком фрагментов, она упрощает поддержку поведения кнопки BACK внутри Activity, причем это поведение интегрируется с существующим стеком Activities.






#### Приступая к работе





Вот простой, но полный пример реализации множественных потоков UI, с помощью фрагментов. Начнем с проектирования ландшафтного layout'a, содержащего список элементов слева и данные по ним справа. Так выглядит нужный нам layout:





![](http://android-helper.com.ua/images/uploads/2012/07/device-fragment-land_new.png)




Код для Activity не особенно интересен, он просто вызывает setContentView() со следующим макетом:







	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

    android:layout_width="match_parent"

    android:layout_height="match_parent"

    android:orientation="horizontal" >



    <fragment

        android:id="@+id/titles"

        android:layout_width="0px"

        android:layout_height="match_parent"

        android:layout_weight="1"

        class="com.example.androidhelper.fragments.TitlesFragment" />



    <FrameLayout

        android:id="@+id/details"

        android:layout_width="0px"

        android:layout_height="match_parent"

        android:layout_weight="1" />



	</LinearLayout>


Вы можете заметить первую особенность: тег

	<fragment>

позволяет устанавливать фрагмент в вашу иерархию интерфейса. Вышеописанный фрагмент наследует свойства ListFragment, который отображает данные по элементу в текущем месте, или в отдельном Activity, в зависимости от layout'a. Обратите внимание на то, как сохраняются изменения в состоянии фрагмента.

	public class TitlesFragment extends ListFragment {

	boolean mDualPane;

	int mCurCheckPosition = 0;



	@Override

	public void onActivityCreated(Bundle savedState) {

		super.onActivityCreated(savedState);



		// Populate list with our static array of titles.

		setListAdapter(new ArrayAdapter<String>(getActivity(), R.layout.list_item, Shakespeare.TITLES));



		// Check to see if we have a frame in which to embed the details

		// fragment directly in the containing UI.

		View detailsFrame = getActivity().findViewById(R.id.details);

		mDualPane = detailsFrame != null && detailsFrame.getVisibility() == View.VISIBLE;



		if (savedState != null) {

			// Restore last state for checked position.

			mCurCheckPosition = savedState.getInt("curChoice", 0);

		}



		if (mDualPane) {

			// In dual-pane mode, list view highlights selected item.

			getListView().setChoiceMode(ListView.CHOICE_MODE_SINGLE);

			// Make sure our UI is in the correct state.

			showDetails(mCurCheckPosition);

		}

	}



	@Override

	public void onSaveInstanceState(Bundle outState) {

		super.onSaveInstanceState(outState);

		outState.putInt("curChoice", mCurCheckPosition);

	}



	@Override

	public void onListItemClick(ListView l, View v, int pos, long id) {

		showDetails(pos);

	}



	/**

	 * Helper function to show the details of a selected item, either by displaying a fragment in-place in the current

	 * UI, or starting a whole new activity in which it is displayed.

	 */

	void showDetails(int index) {

		mCurCheckPosition = index;



		if (mDualPane) {

			// We can display everything in-place with fragments.

			// Have the list highlight this item and show the data.

			getListView().setItemChecked(index, true);



			// Check what fragment is shown, replace if needed.

			DetailsFragment details = (DetailsFragment) getFragmentManager().findFragmentById(R.id.details);

			if (details == null || details.getShownIndex() != index) {

				// Make new fragment to show this selection.

				details = DetailsFragment.newInstance(index);



				// Execute a transaction, replacing any existing

				// fragment with this one inside the frame.

				FragmentTransaction ft = getFragmentManager().beginTransaction();

				ft.replace(R.id.details, details);

				ft.setTransition(FragmentTransaction.TRANSIT_FRAGMENT_FADE);

				ft.commit();

			}



		} else {

			// Otherwise we need to launch a new activity to display

			// the dialog fragment with selected text.

			Intent intent = new Intent();

			intent.setClass(getActivity(), DetailsActivity.class);

			intent.putExtra("index", index);

			startActivity(intent);

		}

	}

	}


Также необходимо реализовать DetailsFragment, который будет отображать данные по элементу в обычном TextView.

	public class DetailsFragment extends Fragment {

	/**

	 * Create a new instance of DetailsFragment, initialized to show the text at 'index'.

	 */

	public static DetailsFragment newInstance(int index) {

		DetailsFragment f = new DetailsFragment();



		// Supply index input as an argument.

		Bundle args = new Bundle();

		args.putInt("index", index);

		f.setArguments(args);



		return f;

	}



	public int getShownIndex() {

		return getArguments().getInt("index", 0);

	}



	@Override

	public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {

		if (container == null) {

			// Currently in a layout without a container, so no

			// reason to create our view.

			return null;

		}



		ScrollView scroller = new ScrollView(getActivity());

		TextView text = new TextView(getActivity());

		int padding = (int) TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, 4, getActivity().getResources()

				.getDisplayMetrics());

		text.setPadding(padding, padding, padding, padding);

		scroller.addView(text);

		text.setText(Shakespeare.DIALOGUE[getShownIndex()]);

		return scroller;

	}

	}

Настало время для добавления еще одного потока UI в наше приложение. Когда экран находится в портретном режиме, то недостаточно места для отображения двух фрагментов бок о бок. То есть мы хотим, чтобы отображался только список:





![](http://android-helper.com.ua/images/uploads/2012/07/device-fragment-port1_new.png)





Напишем новый layout для портретной ориентации:


	<?xml version="1.0" encoding="utf-8"?>

	<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"

    android:layout_width="match_parent"

    android:layout_height="match_parent" >



    <fragment

        android:id="@+id/titles"

        android:layout_width="match_parent"

        android:layout_height="match_parent"

        class="com.example.androidhelper.fragments.TitlesFragment" />



	</FrameLayout>



Теперь у нас нет контейнера для отображения данных для TitlesFragment, отображается только список. При нажатии на элемент списка теперь нужно вызывать отдельное Activity, в котором и будут отображаться данные:





![](http://android-helper.com.ua/images/uploads/2012/07/device-fragment-port2_new.png)





Теперь, всё что от нас требуется — использовать уже готовый DetailsFragment:
	
	public class DetailsActivity extends FragmentActivity {



	@Override

	protected void onCreate(Bundle savedInstanceState) {

		super.onCreate(savedInstanceState);



		if (getResources().getConfiguration().orientation == Configuration.ORIENTATION_LANDSCAPE) {

			// If the screen is now in landscape mode, we can show the

			// dialog in-line so we don't need this activity.

			finish();

			return;

		}



		if (savedInstanceState == null) {

			// During initial setup, plug in the details fragment.

			DetailsFragment details = new DetailsFragment();

			details.setArguments(getIntent().getExtras());

			getSupportFragmentManager().beginTransaction().add(android.R.id.content, details).commit();

		}

	}



	}



Соединив Activity, мы получаем полностью работающий пример приложения, использующего радикальное изменение UI потока, основанное на том, какая конфигурация используется в данный момент. Также приложение автоматически подгоняется под требования размеров экрана при смене конфигурации.





Этот пример иллюстрирует всего лишь один способ использования фрагментов для подгонки вашего UI. В зависимости от дизайна, вы можете предпочесть другие. Например, вы можете поместить всё ваше приложение в одно Activity, в котором будет изменяться структура фрагментов.





Как обычно, больше информации можно найти в документации по SDK. Также можно найти примеры в ApiDemos.





#### Фрагментация для всех





Fragment API будет полезен для разработчиков, начинающих работать с приложениями, ориентированными на планшеты, которые спроектированы под Android 3.0, во многих случаях, связанных с большим экраном. Также использование фрагментов должно сделать проще настройку UI для приложений под новые устройства на Android, например, телевизоры.





Однако, сейчас Fragment API будет наиболее востребован для улучшения интерфейса существующих приложений для телефонов под планшеты.





Также планируется сделать статическую библиотеку для использования Fragment API (_Ура!!!_), чтобы использовать вышеописанный метод на ранних версиях Android. На самом деле, весь код в примере использует как раз статическую библиотеку классов, причем выполняется на Android 2.3 (можете сравнить с примерами в Android 3.0 SDK, они почти не отличаются). Наша цель — сделать эти API как можно более похожими, чтобы вы могли начать работать с ними сейчас, независимо от того, когда вы перейдете на Android 3.0.





Пока нет точной даты, когда библиотека будет доступна, но она точно будет скоро. Пока вы можете начать работать с фрагментами на Android 3.0.





#### Дополнения





Это была первая статья которая описывала фрагменты. На данный момент выпущена библиотека с обратной совместимостью (см. мои предыдущие посты) до версии андроид 1.6.





Для подключения фрагментов в приложения с обратной совместимостью вам нужно сделать следующее:





Наследоватся не от Activity, а от FragmentActivity. Это самое главное требование.





Пример как всегда вы сможете скачать [тут](http://android-helper.com.ua/forms/).
