---
author: andrew
comments: true
date: 2012-09-22 10:55:09+00:00
layout: post
slug: change-size
title: Изменение размера BitmapDrawable
wordpress_id: 637
categories:
- Android для начинающих
- Программирование
---

Нашел неплохую [статью](http://findevelop.blogspot.com/2011/10/bitmapdrawable.html) как изменить размер в BitmapDrawable.





Недавно мне понадобилось получить из внешнего потока (InputStream) картинку и вывести ее на экран, но картинка была в одном размере и ее нужно было под разные экраны и условия уменьшить или увеличить.  
  

<!-- more -->

Для начала получим из потока Drawable. Например с assets каталога:







	Drawable drawable = 	Drawable.createFromStream(act.getAssets().open(fileName), null);







И для того, чтобы изменить размер, я использую следующий метод:




	public static Drawable resizeDrawable(Drawable drawable, int newWidth, int newHeight) {

    Bitmap bitmap = ((BitmapDrawable) drawable).getBitmap();

    int width = bitmap.getWidth();

    int height = bitmap.getHeight();

    float scaleWidth = ((float) newWidth) / width;

    float scaleHeight = ((float) newHeight) / height;

    Matrix matrix = new Matrix();

    matrix.postScale(scaleWidth, scaleHeight);

    Bitmap resizedBitmap = Bitmap.createBitmap(bitmap, 0, 0,

        width, height, matrix, true);

    return new BitmapDrawable(resizedBitmap);

  	}






Этот метод я запостил скорей чтобы не забыть, потому как в проекте он уже не нужен, и дабы помочь ищущему. Но я думаю есть другие варианты решения, если кто наткнулся на пост и знает, то плиз в комментарии.
