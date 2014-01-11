---
author: andrew
comments: true
date: 2013-04-28 05:57:24+00:00
layout: post
slug: splitacivity
title: Красочный переход между Activity
wordpress_id: 1434
categories:
- Android для начинающих
- Android для профессионалов
- Программирование
---

### Задача





Красочный переход между Activity.


 <!-- more -->


### Краткое описание





Многие из вас видели как красиво в некоторых приложениях идут переходы между Activity. Если взять простые примеры от Google, то вы увидите только плавное смещение с лева на право. А у нас с вами задача сделать так, что бы из первого Activity появлялось второе.





### Видео урок



{% youtube Bfh0cJBOASQ %}





### Коды





**Activity1.java**




    
    	SplitAnimation.startActivity(Activity1.this, new Intent(Activity1.this, Activity2.class));





**Activity2.java**




    
    
		@Override
    
    protected void onCreate(Bundle savedInstanceState) {
    
        super.onCreate(savedInstanceState);
    
    
    
        // Preparing the 2 images to be split
    
        SplitAnimation.prepareAnimation(this);
    
    
    
        setContentView(R.layout.act_two);
    
    
    
        // Animating the items to be open, revealing the new activity
    
        SplitAnimation.animate(this, 1000);
    
    }
    
    
    
    @Override
    
    protected void onStop() {
    
        // If we're currently running the entrance animation - cancel it
    
        SplitAnimation.cancel();
    
    
    
        super.onStop();    //To change body of overridden methods use File | Settings | File Templates.
    
    }
    
    



**SplitAnimation.java**




    
    	public static void startActivity(Activity currActivity, Intent intent) {
    
        // Подготовка acticity
    
        prepare(currActivity);
    
    
    
        currActivity.startActivity(intent);
    
        // Чистка анимаций текущей activity
    
        currActivity.overridePendingTransition(0, 0);
    
    }
    
    
    
    private static void prepare(Activity currActivity) {
    
    
    
        // Получаем content activity и делаем из него bitmap
    
        View root = currActivity.getWindow().getDecorView().findViewById(android.R.id.content);
    
        root.setDrawingCacheEnabled(true);
    
        Bitmap bmp = root.getDrawingCache();
    
    
    
        // Указываем в какой части экрана будет делится activity
    
        splitYCoord = bmp.getHeight() / 2;
    
    
    
        // Разделяем экран
    
        mBmp1 = Bitmap.createBitmap(bmp, 0, 0, bmp.getWidth(), splitYCoord);
    
        mBmp2 = Bitmap.createBitmap(bmp, 0, splitYCoord, bmp.getWidth(), bmp.getHeight() - splitYCoord);
    
    
    
        // Указываем координаты положения частей activity
    
        mLoc1 = new int[]{0, root.getTop()};
    
        mLoc2 = new int[]{0, root.getTop() + splitYCoord};
    
    }
    
    
    
    public static void prepareAnimation(final Activity destActivity) {
    
        mTopImage = createImageView(destActivity, mBmp1, mLoc1);
    
        mBottomImage = createImageView(destActivity, mBmp2, mLoc2);
    
    }
    
    
    
    private static ImageView createImageView(Activity destActivity, Bitmap bmp, int loc[]) {
    
        ImageView imageView = new ImageView(destActivity);
    
        imageView.setImageBitmap(bmp);
    
    
    
        WindowManager.LayoutParams windowParams = new WindowManager.LayoutParams();
    
        windowParams.gravity = Gravity.TOP;
    
        windowParams.x = loc[0];
    
        windowParams.y = loc[1];
    
        windowParams.height = ViewGroup.LayoutParams.WRAP_CONTENT;
    
        windowParams.width = ViewGroup.LayoutParams.WRAP_CONTENT;
    
        windowParams.flags =
    
                WindowManager.LayoutParams.FLAG_LAYOUT_IN_SCREEN
    
        ;
    
        windowParams.format = PixelFormat.TRANSLUCENT;
    
        windowParams.windowAnimations = 0;
    
        destActivity.getWindowManager().addView(imageView, windowParams);
    
    
    
        return imageView;
    
    }
    
    
    
    public static void animate(final Activity destActivity, final int duration) {
    
        animate(destActivity, duration, new DecelerateInterpolator());
    
    }
    
    
    
    public static void animate(final Activity destActivity, final int duration, final TimeInterpolator interpolator) {
    
    
    
        // Post this on the UI thread's message queue. It's needed for the items to be already measured
    
        new Handler().post(new Runnable() {
    
    
    
            @Override
    
            public void run() {
    
                mSetAnim = new AnimatorSet();
    
                mTopImage.setLayerType(View.LAYER_TYPE_HARDWARE, null);
    
                mBottomImage.setLayerType(View.LAYER_TYPE_HARDWARE, null);
    
                mSetAnim.addListener(new Animator.AnimatorListener() {
    
                    @Override
    
                    public void onAnimationStart(Animator animation) {
    
                    }
    
    
    
                    @Override
    
                    public void onAnimationEnd(Animator animation) {
    
                        clean(destActivity);
    
                    }
    
    
    
                    @Override
    
                    public void onAnimationCancel(Animator animation) {
    
                        clean(destActivity);
    
                    }
    
    
    
                    @Override
    
                    public void onAnimationRepeat(Animator animation) {
    
    
    
                    }
    
                });
    
    
    
                // Animating the 2 parts away from each other
    
                Animator anim1 = ObjectAnimator.ofFloat(mTopImage, "translationY", mTopImage.getHeight() * -1);
    
                Animator anim2 = ObjectAnimator.ofFloat(mBottomImage, "translationY", mBottomImage.getHeight());
    
    
    
                if (interpolator != null) {
    
                    anim1.setInterpolator(interpolator);
    
                    anim2.setInterpolator(interpolator);
    
                }
    
    
    
                mSetAnim.setDuration(duration);
    
                mSetAnim.playTogether(anim1, anim2);
    
                mSetAnim.start();
    
            }
    
        });
    
    }
    


### Скачать коды





Скачать коды программы вы можете на нашей [странице](http://android-helper.com.ua/codes/)





### Скачать приложение





Также мы выложили на [Play Market](https://play.google.com/store/apps/details?id=com.android_helper.SplitActivityAnimation) наше приложение. Установив его, вы сможете посмотреть как оно работает. (Не забудьте поставить +1 и написать позитивный комментарий :) ).





[![Android app on Google Play](https://developer.android.com/images/brand/en_app_rgb_wo_45.png)](https://play.google.com/store/apps/details?id=com.android_helper.SplitActivityAnimation)





### Оценка статьи





Надеюсь, что вам понравилась статья. Жду ваши комментарии и несколько лайков.




