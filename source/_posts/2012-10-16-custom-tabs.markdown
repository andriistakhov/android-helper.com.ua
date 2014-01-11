---
author: andrew
comments: true
date: 2012-10-16 20:47:50+00:00
layout: post
slug: custom-tabs
title: Custom Tabs - использование своих табов
wordpress_id: 808
categories:
- Android для начинающих
- Android для профессионалов
- Программирование
---

Добрый день друзья.





По просьбе одного из участников нашего сообщества. Был поднят вопрос:





Как можно достучаться до элементов на табах.





Сегодня я вам покажу на эту тему видео и дам комментарии.


<!-- more -->


Смотрим видео.


{% youtube waUgWu8PV1w %}






#### bottom_selector.xml



	<?xml version="1.0" encoding="utf-8"?>

	<selector xmlns:android="http://schemas.android.com/apk/res/android">



		<item android:drawable="@android:drawable/ic_lock_silent_mode_off" android:state_selected="true"/>

		<item android:drawable="@android:drawable/ic_lock_silent_mode_off" android:state_focused="true"/>

		<item android:drawable="@android:drawable/ic_lock_silent_mode_off" android:state_pressed="true"/>

		<item android:drawable="@android:drawable/ic_lock_silent_mode"/>

	</selector>


	



#### text_colors_selector.xml



	<?xml version="1.0" encoding="utf-8"?>

	<selector xmlns:android="http://schemas.android.com/apk/res/android">



		<item android:state_selected="true" android:color="@android:color/white"/>

		<item android:state_focused="true" android:color="@android:color/white"/>

		<item android:state_pressed="true" android:color="@android:color/white"/>

		<item android:color="@android:color/darker_gray"/>



	</selector>

	



#### tab_host_layout.xml


	<?xml version="1.0" encoding="utf-8"?>

	<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"

		android:layout_width="fill_parent"

		android:layout_height="fill_parent"

		android:background="@android:color/black" >



	<ImageView

		android:id="@+id/tab_image"

		android:layout_width="fill_parent"

		android:layout_height="fill_parent"

		android:layout_above="@+id/tab_text"

		android:cropToPadding="true"

		android:scaleType="center"

		android:src="@android:drawable/ic_menu_add" />



	<TextView

		android:id="@+id/tab_text"

		android:layout_width="fill_parent"

		android:layout_height="wrap_content"

		android:layout_alignParentBottom="true"

		android:layout_marginBottom="5dp"

		android:gravity="center_horizontal"

		android:text="Text"

		android:textColor="@color/text_colors_selector"

		android:textSize="13sp" />



	<TextView

		android:id="@+id/tab_unread_message"

		android:layout_width="wrap_content"

		android:layout_height="wrap_content"

		android:layout_alignParentRight="true"

		android:layout_marginRight="10dp"

		android:layout_marginTop="2dp"

		android:gravity="center"

		android:text="5"

		android:textColor="@android:color/white"

		android:textSize="14sp"

		android:textStyle="bold" />



	</RelativeLayout>
	



#### MainActivity.java


1. Комментируем и добавляем построение табов



		// mTabsAdapter.addTab(mTabHost.newTabSpec("simple").setIndicator("Android"), AndroidFragment.class, null);

		// mTabsAdapter.addTab(mTabHost.newTabSpec("contacts").setIndicator("IOs"), IOsFragment.class, null);

		// mTabsAdapter.addTab(mTabHost.newTabSpec("custom").setIndicator("Windows"), WindowsFragment.class, null);

		// mTabsAdapter.addTab(mTabHost.newTabSpec("throttle").setIndicator("DOS"), DOSFragment.class, null);



		mTabsAdapter.addTab(setupTab(new TextView(this), "Android", R.drawable.bottom_selector, 0),

				AndroidFragment.class, null);

		mTabsAdapter

				.addTab(setupTab(new TextView(this), "IOs", R.drawable.bottom_selector, 0), IOsFragment.class, null);

		mTabsAdapter.addTab(setupTab(new TextView(this), "Windows", R.drawable.bottom_selector, 0),

				WindowsFragment.class, null);

		mTabsAdapter

				.addTab(setupTab(new TextView(this), "DOS", R.drawable.bottom_selector, 0), DOSFragment.class, null);

	



2. Добавляем 2 функции сразу после onCreate





		private TabSpec setupTab(final View view, final String tag, final int imageRes, final int notificationItem) {

		View tabview = createTabView(mTabHost.getContext(), tag, imageRes, notificationItem);

		TabSpec setContent = mTabHost.newTabSpec(tag).setIndicator(tabview).setContent(new TabContentFactory()                         {

			@Override

			public View createTabContent(final String tag) {

				return view;

			}

		});

		// mTabHost.addTab(setContent);

		return setContent;

		}



		private static View createTabView(final Context context, final String text, final int imageRes,

			final int notificationItem) {

		View view = LayoutInflater.from(context).inflate(R.layout.tab_host_layout, null);

		TextView tv = (TextView) view.findViewById(R.id.tab_text);

		tv.setText(text);

		ImageView iv = (ImageView) view.findViewById(R.id.tab_image);

		iv.setImageResource(imageRes);

		tv = (TextView) view.findViewById(R.id.tab_unread_message);

		tv.setText(String.valueOf(notificationItem));

		if (notificationItem == 0) {

			tv.setVisibility(View.GONE);

		} else {

			tv.setVisibility(View.VISIBLE);

		}

		return view;

		}






3. Добавляем функцию нотификации после метода onPageScrollStateChanged

4. Делаем нотификацию

		public void updateNotification(final int notificationItem, final int position) {

			View view = mTabHost.getTabWidget().getChildAt(position);

			TextView tv = (TextView) view.findViewById(R.id.tab_unread_message);

			tv.setText(String.valueOf(notificationItem));

			if (notificationItem == 0) {

				tv.setVisibility(View.GONE);

			} else {

				tv.setVisibility(View.VISIBLE);

			}

		}





P.S. Забыл на видео сказать, что нужно еще модифицировать activity_main.xml





#### activity_main.xml





	<?xml version="1.0" encoding="utf-8"?>

	<TabHost xmlns:android="http://schemas.android.com/apk/res/android"

	android:id="@android:id/tabhost"

	android:layout_width="match_parent"

	android:layout_height="match_parent" >



	<LinearLayout

		android:layout_width="match_parent"

		android:layout_height="match_parent"

		android:orientation="vertical" >



		<FrameLayout

			android:id="@android:id/tabcontent"

			android:layout_width="0dp"

			android:layout_height="0dp"

			android:layout_weight="0" />



		<android.support.v4.view.ViewPager

			android:id="@+id/pager"

			android:layout_width="match_parent"

			android:layout_height="0dp"

			android:layout_weight="1" />



		<TabWidget

			android:id="@android:id/tabs"

			android:layout_width="match_parent"

			android:layout_height="53dp"

			android:layout_weight="0"

			android:orientation="horizontal" />

	</LinearLayout>



	</TabHost>




Если вам понравилась статья, пишите свои комментарии.





Поделись этой статьей с другом.
