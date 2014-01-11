---
author: andrew
comments: true
date: 2012-07-17 04:30:02+00:00
layout: post
slug: swipe-tabs
title: Swipe + Tabs - прокрутка экранов и свои tabs
wordpress_id: 317
categories:
- Android для начинающих
- Android для профессионалов
- Программирование
---

Добрый день друзья.





В предыдущих статьях я писал о использовании [Swipe](http://android-helper.com.ua/%D1%81%D0%BB%D0%B0%D0%B9%D0%B4%D0%B8%D0%BD%D0%B3-%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%BE%D0%B2-%D1%81-%D0%BF%D0%BE%D0%BC%D0%BE%D1%89%D1%8C%D1%8E-viewpager/) и своих [Tabs](http://android-helper.com.ua/%D0%BA%D0%B0%D0%BA-%D1%81%D0%B4%D0%B5%D0%BB%D0%B0%D1%82%D1%8C-%D1%81%D0%B2%D0%BE%D0%B8-tabs-%D0%B2-android/)





Тогда у меня возник вопрос, как же их можно объединить. 





![](http://android-helper.com.ua/images/uploads/2012/07/device-2012-07-17-084758.png)


<!-- more -->


### 1. Подготовка проекта





Перед тем как приступить к реализации этой задачи, нам нужно подключить библиотеку обратной совместимости.





Как это сделать, вы можете прочесть [тут](http://android-helper.com.ua/%D1%81%D0%BB%D0%B0%D0%B9%D0%B4%D0%B8%D0%BD%D0%B3-%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%BE%D0%B2-%D1%81-%D0%BF%D0%BE%D0%BC%D0%BE%D1%89%D1%8C%D1%8E-viewpager/) (предварительная настройка).





### 2. Подготовка ресурсов





Откройте файл main.xml и пропишите туда следующий код:

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

			android:layout_height="wrap_content"

			android:layout_weight="0"

			android:orientation="horizontal" />

	</LinearLayout>



	</TabHost>


### 3. Подготовка Activity





Для реализации данного примера мы будем наследоваться не от Activity, а от **FragmentActivity** (о фрагментах мы поговорим в следующей статье):



	public class MainActivity extends FragmentActivity 
  




В метод onCreate пишем следующее:



	private TabHost mTabHost;

	private ViewPager mViewPager;

	private TabsAdapter mTabsAdapter;



	@Override

	public void onCreate(final Bundle savedInstanceState) {

		super.onCreate(savedInstanceState);

		setContentView(R.layout.activity_main);

		mTabHost = (TabHost) findViewById(android.R.id.tabhost);

		mTabHost.setup();



		mViewPager = (ViewPager) findViewById(R.id.pager);



		mTabsAdapter = new TabsAdapter(this, mTabHost, mViewPager);



		mTabsAdapter.addTab(mTabHost.newTabSpec("simple").setIndicator("Android"), AndroidFragment.class, null);

		mTabsAdapter.addTab(mTabHost.newTabSpec("contacts").setIndicator("IOs"), IOsFragment.class, null);

		mTabsAdapter.addTab(mTabHost.newTabSpec("custom").setIndicator("Windows"), WindowsFragment.class, null);

		mTabsAdapter.addTab(mTabHost.newTabSpec("throttle").setIndicator("DOS"), DOSFragment.class, null);



		if (savedInstanceState != null) {

			mTabHost.setCurrentTabByTag(savedInstanceState.getString("tab"));

		}

	}


  




### 4. Реализация Adapter





Вот тут начинается самое интересное.





Создаем adapter который у нас будет унаследован от **FragmentPagerAdapter**, а также для обработки нажатий на Tabs мы подпишемся на обработчик событий **TabHost.OnTabChangeListener** и для скролинга страниц также подписываемся на **ViewPager.OnPageChangeListener**





Полный код вы можете увидеть ниже:



	public static class TabsAdapter extends FragmentPagerAdapter implements TabHost.OnTabChangeListener,

			ViewPager.OnPageChangeListener {

		private final Context mContext;

		private final TabHost mTabHost;

		private final ViewPager mViewPager;

		private final ArrayList<TabInfo> mTabs = new ArrayList<TabInfo>();



		static final class TabInfo {

			private final String tag;

			private final Class<?> clss;

			private final Bundle args;



			TabInfo(final String _tag, final Class<?> _class, final Bundle _args) {

				tag = _tag;

				clss = _class;

				args = _args;

			}

		}



		static class DummyTabFactory implements TabHost.TabContentFactory {

			private final Context mContext;



			public DummyTabFactory(final Context context) {

				mContext = context;

			}



			@Override

			public View createTabContent(final String tag) {

				View v = new View(mContext);

				v.setMinimumWidth(0);

				v.setMinimumHeight(0);

				return v;

			}

		}



		public TabsAdapter(final FragmentActivity activity, final TabHost tabHost, final ViewPager pager) {

			super(activity.getSupportFragmentManager());

			mContext = activity;

			mTabHost = tabHost;

			mViewPager = pager;

			mTabHost.setOnTabChangedListener(this);

			mViewPager.setAdapter(this);

			mViewPager.setOnPageChangeListener(this);

		}



		public void addTab(final TabHost.TabSpec tabSpec, final Class<?> clss, final Bundle args) {

			tabSpec.setContent(new DummyTabFactory(mContext));

			String tag = tabSpec.getTag();



			TabInfo info = new TabInfo(tag, clss, args);

			mTabs.add(info);

			mTabHost.addTab(tabSpec);

			notifyDataSetChanged();

		}



		@Override

		public int getCount() {

			return mTabs.size();

		}



		@Override

		public Fragment getItem(final int position) {

			TabInfo info = mTabs.get(position);

			return Fragment.instantiate(mContext, info.clss.getName(), info.args);

		}



		@Override

		public void onTabChanged(final String tabId) {

			int position = mTabHost.getCurrentTab();

			mViewPager.setCurrentItem(position);

		}



		@Override

		public void onPageScrolled(final int position, final float positionOffset, final int positionOffsetPixels) {

		}



		@Override

		public void onPageSelected(final int position) {

			// Unfortunately when TabHost changes the current tab, it kindly

			// also takes care of putting focus on it when not in touch mode.

			// The jerk.

			// This hack tries to prevent this from pulling focus out of our

			// ViewPager.

			TabWidget widget = mTabHost.getTabWidget();

			int oldFocusability = widget.getDescendantFocusability();

			widget.setDescendantFocusability(ViewGroup.FOCUS_BLOCK_DESCENDANTS);

			mTabHost.setCurrentTab(position);

			widget.setDescendantFocusability(oldFocusability);

		}



		@Override

		public void onPageScrollStateChanged(final int state) {

		}

	}
 
  




### 5. Финальная стадия





Из пункта 2 видно что у нас есть 4 новых фрагмента. Так давайте их создадим.





**AndroidFragment**:




	import android.os.Bundle;

	import android.support.v4.app.Fragment;

	import android.view.LayoutInflater;

	import android.view.View;

	import android.view.ViewGroup;



	public class AndroidFragment extends Fragment {



	@Override

	public View onCreateView(final LayoutInflater inflater, final ViewGroup container, final Bundle savedInstanceState) {

		View view = inflater.inflate(R.layout.android_fragment, container, false);

		return view;

	}



	}




**IOsFragment**:



	public class IOsFragment extends Fragment {



	@Override

	public View onCreateView(final LayoutInflater inflater, final ViewGroup container, final Bundle savedInstanceState) {

		View view = inflater.inflate(R.layout.ios_fragment, container, false);

		return view;

	}



	}

  




**WindowsFragment:**



	public class WindowsFragment extends Fragment {



	@Override

	public View onCreateView(final LayoutInflater inflater, final ViewGroup container, final Bundle savedInstanceState) {

		View view = inflater.inflate(R.layout.windows_fragment, container, false);

		return view;

	}



	}

 
  




**DOSFragment:**



	public class DOSFragment extends Fragment {



	@Override

	public View onCreateView(final LayoutInflater inflater, final ViewGroup container, final Bundle savedInstanceState) {

		View view = inflater.inflate(R.layout.dos_fragment, container, false);

		return view;

	}



	}
  
  




Теперь нам нужно создать еще 4 xml файла которые мы используем в фрагментах





**android_fragment.xml**




	<?xml version="1.0" encoding="utf-8"?>
	
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

	android:layout_width="match_parent"

	android:layout_height="match_parent"

	android:orientation="vertical" >



	<TextView

		android:id="@+id/textView1"

		android:layout_width="wrap_content"

		android:layout_height="wrap_content"

		android:text="Android" />



	</LinearLayout>

  
  




**dos_fragment.xml**



	<?xml version="1.0" encoding="utf-8"?>

	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

	android:layout_width="match_parent"

	android:layout_height="match_parent"

	android:orientation="vertical" >



	<TextView

		android:id="@+id/textView1"

		android:layout_width="wrap_content"

		android:layout_height="wrap_content"

		android:text="DOS" />



	</LinearLayout>


  




**ios_fragment.xml**




	<?xml version="1.0" encoding="utf-8"?>

	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

	android:layout_width="match_parent"

	android:layout_height="match_parent"

	android:orientation="vertical" >



	<TextView

		android:id="@+id/textView1"

		android:layout_width="wrap_content"

		android:layout_height="wrap_content"

		android:text="IOs" />



	</LinearLayout>
  
  




**windows_fragment.xml**



	<?xml version="1.0" encoding="utf-8"?>

	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

	android:layout_width="match_parent"

	android:layout_height="match_parent"

	android:orientation="vertical" >



	<TextView

		android:id="@+id/textView1"

		android:layout_width="wrap_content"

		android:layout_height="wrap_content"

		android:text="Windows" />



	</LinearLayout>

  




Ну вот и все. 





Коды можно скачать [тут](http://android-helper.com.ua/forms/).
