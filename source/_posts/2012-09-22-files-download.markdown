---
author: andrew
comments: true
date: 2012-09-22 10:48:40+00:00
layout: post
slug: files-download
title: Загрузка файла из сети с отображением прогресса
wordpress_id: 634
categories:
- Android для начинающих
- Android для профессионалов
- Программирование
---

Добрый день.





Сегодня нашел статью, как можно закачать файлы с сервера.





Оригинал статьи [тут](http://findevelop.blogspot.com/2012/01/android_19.html).


 <!-- more -->


Хорошо сделанное Android-приложение (кроме всего прочего) не заставляет клиента угадывать что в данный момент происходит "по ту сторону экрана". Приятное и аккуратное приложение показывает при всех продолжительных операциях прогресс-бар, который реализует, как правило, с помощью класса AsyncTask. Давайте посмотрим как правильно использовать этот замечательный инструмент на примере загрузки файла из сети:





	import java.io.File;

	import java.io.FileOutputStream;

	import java.io.IOException;

	import java.io.InputStream;

	import java.net.HttpURLConnection;

	import java.net.MalformedURLException;

	import java.net.URL;

	import android.app.Act	ivity;

	import android.app.ProgressDialog;

	import android.os.AsyncTask;

	import android.os.Bundle;

	import android.view.View;

	import android.view.ViewGroup.LayoutParams;

	import android.widget.Button;

	import android.widget.TextView;

	public class BackFLoaderActivity extends Activity {

 

 	@Override

 	public void onCreate(Bundle savedInstanceState) {

  	super.onCreate(savedInstanceState);

  	Button load = new Button(this);

  	load.setText("Load file");

  	load.setOnClickListener(new View.OnClickListener() {

   	@Override

   	public void onClick(View v) {

    	downloadFile("http://anjedi.com/api_lib/2.2_level8.jar");

   	}

  	});

  	setContentView(load, new LayoutParams(LayoutParams.WRAP_CONTENT,

   	LayoutParams.WRAP_CONTENT));

 	}

 



 	private void downloadFile(String url) {

  	final ProgressDialog progressDialog = new ProgressDialog(this);

 

  	new AsyncTask<String, Integer, File>() {

   	private Exception m_error = null;

 

   	@Override

   	protected void onPreExecute() {

    progressDialog.setMessage("Downloading ...");

    progressDialog.setCancelable(false);

    progressDialog.setMax(100);

    progressDialog

      .setProgressStyle(ProgressDialog.STYLE_HORIZONTAL);

 

    progressDialog.show();

   	}

 

   	@Override

   	protected File doInBackground(String... params) {

    URL url;

    HttpURLConnection urlConnection;

    InputStream inputStream;

    int totalSize;

    int downloadedSize;

    byte[] buffer;

    int bufferLength;

 

    File file = null;

    FileOutputStream fos = null;

 

    try {

     url = new URL(params[0]);

     urlConnection = (HttpURLConnection) url.openConnection();

 

     urlConnection.setRequestMethod("GET");

     urlConnection.setDoOutput(true);

     urlConnection.connect();

 

     file = File.createTempFile("Mustachify", "download");

     fos = new FileOutputStream(file);

     inputStream = urlConnection.getInputStream();

 

     totalSize = urlConnection.getContentLength();

     downloadedSize = 0;

 

     buffer = new byte[1024];

     bufferLength = 0;

 

     // читаем со входа и пишем в выход, 

     // с каждой итерацией публикуем прогресс

     while ((bufferLength = inputStream.read(buffer)) > 0) {

      fos.write(buffer, 0, bufferLength);

      downloadedSize += bufferLength;

      publishProgress(downloadedSize, totalSize);

     }

 

     fos.close();

     inputStream.close();

 

     return file;

    } catch (MalformedURLException e) {

     e.printStackTrace();

     m_error = e;

    } catch (IOException e) {

     e.printStackTrace();

     m_error = e;

    }

 

    return null;

   	}

 

   	// обновляем progressDialog

   	protected void onProgressUpdate(Integer... values) {

    progressDialog

      .setProgress((int) ((values[0] / (float) values[1]) * 100));

   	};

 

   	@Override

   	protected void onPostExecute(File file) {

    // отображаем сообщение, если возникла ошибка

    if (m_error != null) {

     m_error.printStackTrace();

     return;

    }

    // закрываем прогресс и удаляем временный файл

    progressDialog.hide();

    file.delete();

   	}

  	}.execute(url);

 		}

	}






В этом маленьком приложении при нажатии на кнопку запускается загрузка файла, при этом пользователь наблюдает прогресс-бар. Не забудьте добавить в манифест запрос разрешений на доступ к интернет и файловой системе.  
Основой примера является метод, который я взял [отсюда](http://twigstechtips.blogspot.com/2011/10/android-download-file-with-progress.html) (и поправил пару ошибок).     
Этот метод принимает url файла, который нужно загрузить, загружает файл, отображая при этом горизонатальный прогресс-бар. При этом прогресс-бар реально показывает какая часть файла в данный момент загружена. По окончании загрузки файл удаляется.  
  
**Разберём работу метода подробнее:**  
  
Главная часть метода - создание AcyncTask-а и переопределение его методов.  
В методе _onPreExecute_ мы запускаем progressDialog, установив предварительно текст сообщения и максимальное значение прогресса: 100%.   
В методе _doInBackground_ - выполняем собственно загрузку файла. Файл читаем  из_urlConnection_ порциями по 1024 байт, каждый раз прибавляя размер полученной порции к общему счётчику. Счётчик и общий размер файла передаём при каждой итерации в метод _publishProgress_, благодаря чему в методе _onProgressUpdate_ мы получаем эти данные и обновляем текущий статус progressDialog-а.   
И, наконец, в методе _onPostExecute_ мы прячем диалог и удаляем временный файл.   
Особенностью использования AsyncTask-a является способ, как он объявляется и как в него передаются параметры. Типы, которыми параметризуется экземпляр AsyncTask-a определяю по порядку: тип входящего значения, тип параметра, опреляющего прогресс опреации и тип результата фоновой операции (то что возвращает _doInBackground_ и принимает _onPostExecute_). Кроме того конструктор и чаcть методов AsyncTask-а принимает _varargs, _т.е. произвольное число параметров, что весьма удобно в некоторых случаях.
