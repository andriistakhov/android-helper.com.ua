---
author: andrew
comments: true
date: 2012-07-15 06:10:33+00:00
layout: post
slug: '%d0%ba%d0%b0%d0%ba-%d1%81%d0%b4%d0%b5%d0%bb%d0%b0%d1%82%d1%8c-%d1%81%d0%b2%d0%be%d0%b8-tabs-%d0%b2-android'
title: Как сделать свои Tabs в Android
wordpress_id: 304
categories:
- Android для начинающих
- Программирование
---

Добрый день друзья.





Сегодня я бы хотел поделится с вами информацией о создании и использовании своих Tabs.





![](http://android-helper.com.ua/images/uploads/2012/07/custom_tabs_view.png)




<!-- more -->
  






### 1. Создадим layout





  






Самое первое что нужно сделать, это открыть main.xml и добавить туда TabHost и TabWidget







	<?xml version="1.0" encoding="utf-8"?>

	<TabHost xmlns:android="http://schemas.android.com/apk/res/android"

	android:id="@android:id/tabhost" android:layout_width="fill_parent"

	android:layout_height="fill_parent">

	<LinearLayout android:orientation="vertical"

		android:layout_width="fill_parent" android:layout_height="fill_parent">

		<TabWidget android:id="@android:id/tabs"

			android:layout_width="fill_parent" android:layout_height="wrap_content" />

		<FrameLayout android:id="@android:id/tabcontent"

			android:layout_width="fill_parent" android:layout_height="fill_parent">

		</FrameLayout>

	</LinearLayout>

	</TabHost>








### 2. Пишем код в Activity





  






У нас есть main.xml , теперь нам нужно добавить код для привязки нашего activity с нашим файлом







	setContentView(R.layout.main);

	mTabHost = (TabHost) findViewById(android.R.id.tabhost);









Ну вот после этого мы можем добавлять tabs на наше activty







	@Override

	public void onCreate(Bundle savedInstanceState) {

	super.onCreate(savedInstanceState);

	setContentView(R.layout.main);

	mTabHost = (TabHost) findViewById(android.R.id.tabhost);

	setupTab(new TextView(this), "Tab 1");

	setupTab(new TextView(this), "Tab 2");

	setupTab(new TextView(this), "Tab 3");

	}

	private void setupTab(final View view, final String tag) {

	View tabview = createTabView(mTabHost.getContext(), tag);

        TabSpec setContent = mTabHost.newTabSpec(tag).setIndicator(tabview).setContent(new TabContentFactory() {

		public View createTabContent(String tag) {return view;}

	});

	mTabHost.addTab(setContent);

	}



	private static View createTabView(final Context context, final String text) {

	View view = LayoutInflater.from(context).inflate(R.layout.tabs_bg, null);

	TextView tv = (TextView) view.findViewById(R.id.tabsText);

	tv.setText(text);

	return view;

	}








### 3. Создаем свой tabs layout





Создадим новый xml файл и назовем его tabs_bg.xml. Этот файл как раз и будет служить заполнением нашего tab.





На него можно добавлять любые элементы.








	<?xml version="1.0" encoding="utf-8"?>

	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

	android:id="@+id/tabsLayout" android:layout_width="fill_parent"

	android:layout_height="fill_parent"

	android:padding="10dip" android:gravity="center" android:orientation="vertical">



	<TextView android:id="@+id/tabsText" android:layout_width="wrap_content"

		android:layout_height="wrap_content" android:text="Title"

		android:textSize="15dip" />

	</LinearLayout>








### 4. Меняем вид и поведение





Создадим еще несколько файлов tab_text_selector.xml и tab_bg_selector.xml (их нужно создать в папке layout), а также tab_bg_selected.xml и tab_bg_unselected.xml (эти 2 файла создаем в папке drawable). Эти файлы служат для того что при изменении статуса tab с активного на неактивный соответственно меняется его внешний вид.





**tab_bg_selected.xml:**







	<?xml version="1.0" encoding="utf-8"?>

	<shape xmlns:android="http://schemas.android.com/apk/res/android"

	android:shape="rectangle">

	<gradient android:startColor="#A8A8A8" android:centerColor="#7F7F7F"

		android:endColor="#696969" android:angle="-90" />

	</shape>







**tab_bg_unselected.xml:**







	<?xml version="1.0" encoding="utf-8"?>

	<shape xmlns:android="http://schemas.android.com/apk/res/android"

	android:shape="rectangle">

	<gradient android:startColor="#5C5C5C" android:centerColor="#424242"

		android:endColor="#222222" android:angle="-90" />

	</shape>







 **tab_text_selector.xml:**







	<?xml version="1.0" encoding="utf-8"?>

	<selector xmlns:android="http://schemas.android.com/apk/res/android">

    <item android:state_selected="true" android:color="@android:color/white" />

    <item android:state_focused="true" android:color="@android:color/white" />

    <item android:state_pressed="true" android:color="@android:color/white" />

    <item android:color="#f8f8f8" />

	</selector>








**tab_bg_selector.xml:**







	<?xml version="1.0" encoding="utf-8"?>

	<selector xmlns:android="http://schemas.android.com/apk/res/android">

    <!--  Active tab -->

	<item android:state_selected="true" android:state_focused="false"

		android:state_pressed="false" android:drawable="@drawable/tab_bg_selected" />

	<!--  Inactive tab -->

	<item android:state_selected="false" android:state_focused="false"

		android:state_pressed="false" android:drawable="@drawable/tab_bg_unselected" />

	<!--  Pressed tab -->

	<item android:state_pressed="true" android:drawable="@android:color/transparent" />

	<!--  Selected tab (using d-pad) -->

	<item android:state_focused="true" android:state_selected="true"

		android:state_pressed="false" android:drawable="@android:color/transparent" />

	</selector>







И теперь поменяем внешний вид нашего tabs_bg.xml









	<?xml version="1.0" encoding="utf-8"?>

	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

	android:id="@+id/tabsLayout" android:layout_width="fill_parent"

	android:layout_height="fill_parent" android:background="@drawable/tab_bg_selector"

	android:padding="10dip" android:gravity="center" android:orientation="vertical">



	<TextView android:id="@+id/tabsText" android:layout_width="wrap_content"

		android:layout_height="wrap_content" android:text="Title"

		android:textSize="15dip" android:textColor="@drawable/tab_text_selector" />

	</LinearLayout>









### 5. Заключительная стадия 





  






В заключительной стадии нам нужно добавить разделитель между tabs.





Этот код нужно добавить пере вызовом функции setupTab.








	mTabHost.getTabWidget().setDividerDrawable(R.drawable.tab_divider);









Ну вот и все.





В следующей статье я продолжу тему своих tabs. И с нее вы сможете узнать как скомпоновать tabs и swipe экранов.




