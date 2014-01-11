---
author: andrew
comments: true
date: 2012-11-18 22:23:51+00:00
layout: post
slug: pagertabstrip
title: PagerTabStrip - замена Tabs или новые возможности android
wordpress_id: 937
categories:
- Android для начинающих
- Android для профессионалов
- Программирование
---

Всем привет.





Как много мы с вами видим разного рода красивых дизайнов, где с помощью пальца, можно менять не только экран но и Tabs.





Более подробно мы с вами остановимся на реализации данной задачи.


  <!-- more -->


Как вы поняли из названия статьи, мы будем использовать **PagerTabStrip**





Решил долго ничего не придумывать и записал видео как это сделать:









Теперь сам код:





**activity_main.xml**



	<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"

		xmlns:tools="http://schemas.android.com/tools"

		android:layout_width="fill_parent"

		android:layout_height="fill_parent"

		android:orientation="vertical" >



		<android.support.v4.view.ViewPager

			android:id="@+id/viewpager"

			android:layout_width="fill_parent"

			android:layout_height="fill_parent" >



			<android.support.v4.view.PagerTabStrip

				android:id="@+id/pagerTabStrip"

				android:layout_width="wrap_content"

				android:layout_height="wrap_content"

				android:layout_gravity="top"

				android:background="#a22c2e" />

		</android.support.v4.view.ViewPager>



	</RelativeLayout>


**fragment_default.xml**


	<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"

	android:layout_width="fill_parent"

	android:layout_height="fill_parent" >



	<TextView

		android:id="@+id/text"

		android:layout_width="wrap_content"

		android:layout_height="wrap_content"

		android:layout_gravity="center"

		android:text="Test" />



	</FrameLayout>



**MainActivity**



	public class MainActivity extends FragmentActivity {



		private ViewPager mViewPager;



		@Override

		public void onCreate(Bundle savedInstanceState) {

			super.onCreate(savedInstanceState);

			setContentView(R.layout.activity_main);



			mViewPager = (ViewPager) findViewById(R.id.viewpager);

			PagerTabStrip pagerTabStrip = (PagerTabStrip) findViewById(R.id.pagerTabStrip);

			// pagerTabStrip.setTextColor(color.white);

			// pagerTabStrip.setTabIndicatorColor(color.holo_red_dark);



			TitleAdapter titleAdapter = new TitleAdapter(getSupportFragmentManager());

			mViewPager.setAdapter(titleAdapter);

			mViewPager.setCurrentItem(0);

		}



		@Override

		public boolean onCreateOptionsMenu(Menu menu) {

			getMenuInflater().inflate(R.menu.activity_main, menu);

			return true;

		}

	}







**TitleAdapter**


	public class TitleAdapter extends FragmentPagerAdapter {

	private final String titles[] = new String[] { "View1", "View2", "View3" };

	private final Fragment frags[] = new Fragment[titles.length];



	public TitleAdapter(FragmentManager fm) {

		super(fm);

		frags[0] = new FragmentView1();

		frags[1] = new FragmentView2();

		frags[2] = new FragmentView3();

	}



	@Override

	public CharSequence getPageTitle(int position) {

		Log.v("TitleAdapter - getPageTitle=", titles[position]);

		return titles[position];

	}



	@Override

	public Fragment getItem(int position) {

		Log.v("TitleAdapter - getItem=", String.valueOf(position));

		return frags[position];

	}



	@Override

	public int getCount() {

		return frags.length;

	}

	}


**FragmentView1**


	public class FragmentView1 extends Fragment {

	@Override

	public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {

		View myView = inflater.inflate(R.layout.fragment_default, container, false);

		TextView text = (TextView) myView.findViewById(R.id.text);

		text.setText("Fragment View 1\n - Пример для android-helper.com.ua");

		return myView;

	}



}


**FragmentView2**


	public class FragmentView2 extends Fragment {

	@Override

	public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {

		View myView = inflater.inflate(R.layout.fragment_default, container, false);

		TextView text = (TextView) myView.findViewById(R.id.text);

		text.setText("Fragment View 2\n - Пример для android-helper.com.ua");

		return myView;

	}



	}




**FragmentView3**




	public class FragmentView2 extends Fragment {

	@Override

	public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {

		View myView = inflater.inflate(R.layout.fragment_default, container, false);

		TextView text = (TextView) myView.findViewById(R.id.text);

		text.setText("Fragment View 3\n - Пример для android-helper.com.ua");

		return myView;

	}



	}


Полный проект вы можете скачать на [странице с кодами](http://android-helper.com.ua/codes/).





Надеюсь вам понравилась статья.





Пишите ваши комментарии и подписывайтесь на обновления.
