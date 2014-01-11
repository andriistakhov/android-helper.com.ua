---
author: andrew
comments: true
date: 2013-01-02 21:48:05+00:00
layout: post
slug: '%d0%bf%d0%b8%d1%88%d0%b5%d0%bc-%d1%81%d0%b2%d0%be%d0%b9-android-market'
title: Пишем свой Android Market
wordpress_id: 1108
categories:
- Android для начинающих
- Android для профессионалов
- Программирование
---

Сегодня что то мне не спится.





Хочу побольше дать интересных тем.





Не так много разработчиков выкладывают свои приложения на маркет.


<!-- more -->


Много приложений остается закрытыми в офисной рутине.





Но вот нашел статью, как можно и в офисе организовать свой маркет. Может кому будет полезной.





В рамках работы над большим количеством android приложений появилось желание создать свой Android Market на локальном сервере (с шахматами и поэтессами).  
Свой маркет должен решать две простые задачи:








  * Распространение и обновление корпоративных приложений. У нас есть набор внутри корпоративных приложений, которые нельзя публиковать на Google Play. Сейчас пользователи оповещаются о новых версиях по емайлу, что не удобно.



  * Бета тестирование заказных приложений на большой группе коллег, с обратной связью.






  
  
И так, нам необходимо реализовать небольшое Android приложение, которое будет скачивать список доступных приложений, проверять наличие обновлений для уже установленных, устанавливать/удалять приложения.  






#### Серверная часть





  
Серверной частью приложения может быть как сервис с широким функционалом (регистрация пользователей, отчеты, разграничение доступа к приложениям), так и просто xml файл:



	<MyMarket>

    <application name="TestApp1" 

    package="com.example.testapp1"

    versionCode="1"

    versionName="1.0" 

    url="http://mobile...../android/download/TestApp1.apk"/>

		.....

	</MyMarket>





  
В моем случае серверной частью фактически выступает http шара.  
Данный xml файл в приложении трансформируется в список приложений, доступных для установки.  
  






#### Установка





  
Пользователь выбирает нужное ему приложение и устанавливает,   
Маркет скачивает apk файл на флешку устройства и инициирует установку приложения, конечно, должна быть разрешена установка из сторонних источников.  
Установить незаметно в фоне мы не можем, поэтому после выполнения данного кода:




	URL url = new URL(apkurl);

            HttpURLConnection c = (HttpURLConnection) url.openConnecвленtion();

            c.setRequestMethod("GET");

            c.setDoOutput(true);

            c.connect();



            File file = this.getExternalFilesDir("download");

            File outputFile = new File(file, "app.apk");

            FileOutputStream fos = new FileOutputStream(outputFile);

            InputStream is = c.getInputStream();



            byte[] buffer = new byte[1024];

            int len1 = 0;

            while ((len1 = is.read(buffer)) != -1) {

                fos.write(buffer, 0, len1);

            }

            fos.close();

            is.close();

            

            Intent intent = new Intent(Intent.ACTION_VIEW);

            intent.setDataAndType(Uri.fromFile(outputFile),

                    "application/vnd.android.package-archive");

            startActivity(intent);





  
Пользователь увидит стандартное окно установки приложений:  
![image](http://img-fotki.yandex.ru/get/4117/1154545.a5/0_7d3c3_bd08d241_L)  
  






#### Обновление





  
Приложение Market может по определенному интервалу проверять наличие новых версий, делается это достаточно просто, так как доступен список всех установленных приложений:




	private boolean checkNewVersion(String packageName, int versionCodeNew) {

        List<ApplicationInfo> apps = getPackageManager()

                .getInstalledApplications(0);

        for (int i = 0; i < apps.size(); i++) {

            ApplicationInfo app = apps.get(i);

            if (packageName.equals(app.packageName)) {

                PackageManager manager = getPackageManager();

                PackageInfo info;

                try {

                    info = manager.getPackageInfo(app.packageName, 0);

                    int versionCode = info.versionCode;

                    if (versionCodeNew > versionCode) {

                        Toast.makeText(this, "New Version!", Toast.LENGTH_LONG)

                                .show();

                        return true;

                    }

                } catch (NameNotFoundException e) {

                    e.printStackTrace();

                }

            }

        }

        return false;

    }



  
В случае необходимости обновления приложения, повторяем процесс Установки, приложение будет обновлено.  
  






#### Удаление





  
Удалить программу лучше из интерфейса нашего маркета, чтобы не заставлять пользователя выискивать тестируемое приложение среди всех его программ, для этого достаточно вызвать этот код:



	 Uri packageURI = Uri.parse("package:"+packageName);

        Intent intent = new Intent(Intent.ACTION_DELETE, packageURI);

        startActivity(intent);





  
![image](http://img-fotki.yandex.ru/get/4134/1154545.a5/0_7d3c5_830496a8_L)  
  






#### Обратная связь





  
Получение сообщений об ошибках, вместе с логом ошибки, очень важно для разработчика, особенно на этапе тестирования приложения, добавим функцию автоматического сбора этой информации через Маркет.  
  
Для этого в каждое приложение, распространяемое через наш Маркет добавляем следующий класс




	public class CustomExceptionHandler implements UncaughtExceptionHandler {

    private File logsFolder = null;

    public static final String ERROR_INTENT = "com.example.markettestapp1.SEND_ERROR";

    public CustomExceptionHandler(File logsFolder) {

        this.logsFolder = logsFolder;

    }

    @Override

    public void uncaughtException(Thread thread, Throwable ex) {

        final Writer result = new StringWriter();

        final PrintWriter printWriter = new PrintWriter(result);

        ex.printStackTrace(printWriter);

        String stacktrace = result.toString();

        printWriter.close();

        try {

            if (!logsFolder.exists()) {

                logsFolder.createNewFile();

            }

            BufferedWriter writer = new BufferedWriter(new FileWriter(logsFolder, true));

            writer.write(""+new Date()+"\n"+stacktrace);

            writer.close();

        } catch (IOException e) {

            e.printStackTrace();

        }

        

        Intent intent = new Intent();

        intent.setAction(ERROR_INTENT);

        intent.putExtra("packageName", Test1Application.getApplication().getPackageName());

        intent.putExtra("stacktrace", stacktrace);

        Test1Application.getInstanceApplication().sendBroadcast(intent);

        

        android.os.Process.killProcess(android.os.Process.myPid());

    }

	}


  
Как видно из кода, это хендлер, который получает ошибки (Exception), генерит строку со стеком, сохраняет ее в локальный файл и отправляет ошибку в виде броадкаст сообщения.  
  
А в Application классе включаем переадресацию всех сообщений об ошибках в этот хендлер



	public class Test1Application extends Application {

    @Override

    public void onCreate() {

        super.onCreate();

        application = this;

        Thread.setDefaultUncaughtExceptionHandler(new CustomExceptionHandler(new File(this.getApplicationContext().getExternalFilesDir(null),"exceptions.log")));

    }




  
Таким образом, если в тестируемои приложении происходит ошибка, то перед смертью пишется сообщение в лог и посылается броадкаст сообщение в маркет.,  
Перед передачей приложения заказчику, этот код удаляется (комментируется).  
  
В самом Маркете мы просто отлавливаем данное сообщение:


	public class SendErrorReceiver  extends BroadcastReceiver {

    public static final String ERROR_INTENT = "com.example.markettestapp1.SEND_ERROR";

    @Override

    public void onReceive(Context context, Intent intent) {

        Intent i = new Intent(context, SendErrorActivity.class);

        i.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);

        i.putExtra("stacktrace", intent.getStringExtra("stacktrace"));

        i.putExtra("packageName", intent.getStringExtra("packageName"));

        context.startActivity(i);

    }

	}




  
AndroidManifest.xml:


	<receiver android:name="com.example.markettestapp1.SendErrorReceiver" android:enabled="true" >

            <intent-filter>

                <action android:name="com.example.markettestapp1.SEND_ERROR" >

                </action>

            </intent-filter>

        </receiver>





  
  
И дальше маркет уже может переслать это сообщение на почту разработчику, загрузить в какой нибудь веб-сервис или попросить пользователя добавить комментарий к ошибке  
![image](http://img-fotki.yandex.ru/get/4121/1154545.a5/0_7d3c4_5b504bb_L)  
  
Я надеюсь, данный пример поможет многим начинающим разработчикам, разобраться, как написать свой универсальный велосипед для бета тестирования приложений и централизованному сбору статистики ошибок.  
  
ps: В статье использован исходный код и картинки взятые из прототипа приложения.




