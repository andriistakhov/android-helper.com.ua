---
author: andrew
comments: true
date: 2012-12-03 08:55:23+00:00
layout: post
slug: header-separator-%d0%b2-listview
title: Header (separator) в ListView
wordpress_id: 974
categories:
- Android для начинающих
- Android для профессионалов
- Программирование
---

Всем привет.





Когда разрабатываешь хорошее приложение. Часто бывает нужно сделать красивые отделения между элементами.





Что бы долго не говорить посмотрите ниже:





![](http://android-helper.com.ua/images/uploads/2012/12/img-1024x564.jpg)


 <!-- more -->
 





Каждый программист ищет свои пути решения данной задачи.





Я же хочу показать вам как это делать с помощью обычного adapter.





Смотрим результат:



{% youtube GHpLuRLujOk %}





Приступим:





**MultipleItemsList**:

		@Override

    public void onCreate(Bundle savedInstanceState) {

        super.onCreate(savedInstanceState);

        mAdapter = new MyCustomAdapter();

        for (int i = 1; i < 50; i++) {

            mAdapter.addItem("item " + i);

            if (i % 4 == 0) {

                mAdapter.addSeparatorItem("separator " + i);

            }

        }

        setListAdapter(mAdapter);

    }




Сам адаптер **MyCustomAdapter:**

	private class MyCustomAdapter extends BaseAdapter {



        private static final int TYPE_ITEM = 0;

        private static final int TYPE_SEPARATOR = 1;

        private static final int TYPE_MAX_COUNT = TYPE_SEPARATOR + 1;



        private ArrayList<String> mData = new ArrayList<String>();

        private LayoutInflater mInflater;



        private TreeSet<Integer> mSeparatorsSet = new TreeSet<Integer>();



        public MyCustomAdapter() {

            mInflater = (LayoutInflater)getSystemService(Context.LAYOUT_INFLATER_SERVICE);

        }



        public void addItem(final String item) {

            mData.add(item);

            notifyDataSetChanged();

        }



        public void addSeparatorItem(final String item) {

            mData.add(item);

            // save separator position

            mSeparatorsSet.add(mData.size() - 1);

            notifyDataSetChanged();

        }



        @Override

        public int getItemViewType(int position) {

            return mSeparatorsSet.contains(position) ? TYPE_SEPARATOR : TYPE_ITEM;

        }



        @Override

        public int getViewTypeCount() {

            return TYPE_MAX_COUNT;

        }



        @Override

        public int getCount() {

            return mData.size();

        }



        @Override

        public String getItem(int position) {

            return mData.get(position);

        }



        @Override

        public long getItemId(int position) {

            return position;

        }



        @Override

        public View getView(int position, View convertView, ViewGroup parent) {

            ViewHolder holder = null;

            int type = getItemViewType(position);

            System.out.println("getView " + position + " " + convertView + " type = " + type);

            if (convertView == null) {

                holder = new ViewHolder();

                switch (type) {

                    case TYPE_ITEM:

                        convertView = mInflater.inflate(R.layout.item1, null);

                        holder.textView = (TextView)convertView.findViewById(R.id.text);

                        break;

                    case TYPE_SEPARATOR:

                        convertView = mInflater.inflate(R.layout.item2, null);

                        holder.textView = (TextView)convertView.findViewById(R.id.textSeparator);

                        break;

                }

                convertView.setTag(holder);

            } else {

                holder = (ViewHolder)convertView.getTag();

            }

            holder.textView.setText(mData.get(position));

            return convertView;

        }



    }



    public static class ViewHolder {

        public TextView textView;

    }


Вот и все.





Понравилось? Расскажи своим друзьям.





Рабочий код на странице с [кодами](http://android-helper.com.ua/codes/).
