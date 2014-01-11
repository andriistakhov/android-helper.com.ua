---
author: andrew
comments: true
date: 2012-08-17 12:37:17+00:00
layout: post
slug: send_get_sms
title: Отправка и получение SMS
wordpress_id: 421
categories:
- Android для начинающих
- Android для профессионалов
- Программирование
---

Добрый день друзья.





В предыдущих записях я писал как можно отправлять сообщения с помощью встроенной системы.





Сегодня же я решил поделится опытом отправки и приема сообщений без участия стандартных программ.





Приступим.


<!-- more -->


### Отправка сообщений





## 1. Дадим права отправки сообщений



	<uses-permission android:name="android.permission.SEND_SMS"></uses-permission>


## 2. Базовая функция отправки сообщений


	SmsManager sms = SmsManager.getDefault();

	sms.sendTextMessage("1-212-555-1212", null, "Привет, Android-helper.com.ua", null, null);








## 3. Опишем Activity для отправки.





Хочу отметить, что для получения статуса сообщение мы создадим BroadcastReceiver и зарегистрируем его.




	import android.app.Activity;

	import android.app.PendingIntent;

	import android.content.BroadcastReceiver;

	import android.content.Context;

	import android.content.Intent;

	import android.content.IntentFilter;

	import android.os.Bundle;

	import android.telephony.SmsManager;

	import android.util.Log;

	import android.view.View;

	import android.view.View.OnClickListener;

	import android.widget.Button;

	import android.widget.Toast;

	public class SMSFun extends Activity {

	// Button to trigger sending the SMS

	Button aButton;

	// PendingIntent to tell the SMS app to notify us

	PendingIntent sentPI;

	// The intent action we are using

    String SENT = "SMS_SENT";

    // The BroadcastReceiver that we use to listen for the notification back

    BroadcastReceiver br;

    @Override

	public void onCreate(Bundle savedInstanceState) {

        super.onCreate(savedInstanceState);

        setContentView(R.layout.main);

        // Create the Pending Intent

	sentPI = PendingIntent.getBroadcast(this, 0,

                new Intent(SENT), 0);

	aButton = (Button) this.findViewById(R.id.Button01);

        aButton.setOnClickListener(new OnClickListener() {

	public void onClick(View v) {

	SmsManager sms = SmsManager.getDefault();

	// send the message, passing in the pending intent, sentPI

        sms.sendTextMessage("1-212-555-1212", null, "Привет android-helper.com.ua", sentPI, null);  

        registerReceiver(br, new IntentFilter(SENT));       

	}});  

          

        // In order to receive the results via the pending intent we need

	// to register a new BroadcastReceiver and pay attention to the various

	// values we could get back

	br = new BroadcastReceiver(){

                @Override

	public void onReceive(Context ctx, Intent intent) {

                    switch (getResultCode())

                    {

                        case Activity.RESULT_OK:

                            Toast.makeText(getBaseContext(), "SMS sent", 

                                    Toast.LENGTH_SHORT).show();

                            break;

                        case SmsManager.RESULT_ERROR_GENERIC_FAILURE:

                            Toast.makeText(getBaseContext(), "SMS: Generic failure", 

                                    Toast.LENGTH_SHORT).show();

                            break;

                        case SmsManager.RESULT_ERROR_NO_SERVICE:

                            Toast.makeText(getBaseContext(), "SMS: No service", 

                                    Toast.LENGTH_SHORT).show();

                            break;

                        case SmsManager.RESULT_ERROR_NULL_PDU:

                            Toast.makeText(getBaseContext(), "SMS: Null PDU", 

                                    Toast.LENGTH_SHORT).show();

                            break;

                        case SmsManager.RESULT_ERROR_RADIO_OFF:

                            Toast.makeText(getBaseContext(), "SMS: Radio off", 

                                    Toast.LENGTH_SHORT).show();

                            break;

                    }

                    unregisterReceiver(br);

                }

            };    }

	}








###  Получение сообщений 





## 1. Права доступа







	<uses-permission android:name="android.permission.RECEIVE_SMS"></uses-permission>








## 2. Для того что бы получать сообщения, нужно создать BroadcastReceiver







	<receiver android:name=".SMSFunReceive">

     <intent-filter>

                <action android:name=

                   "android.provider.Telephony.SMS_RECEIVED" />

     </intent-filter>

	</receiver>

**Java**

	import android.content.BroadcastReceiver;

	import android.content.Context;

	import android.content.Intent;

	import android.os.Bundle;

	import android.telephony.SmsMessage;

	import android.util.Log;

	// This will run when an SMS message comes in.

	// We can see if we want to do something based upon the message

	// Perhaps launch an activity

	public class SMSFunReceive extends BroadcastReceiver 

	{

	@Override

	public void onReceive(Context ctx, Intent intent) 

	{

	Bundle bundle = intent.getExtras();        

	Object[] pdus = (Object[]) bundle.get("pdus");

	SmsMessage[] messages = new SmsMessage[pdus.length];

        

	for (int i = 0; i < messages.length; i++)
	{

	messages[i] = SmsMessage.createFromPdu((byte[])pdus[i]);

        

	Log.v("SMSFun","Body: " + messages[i].getDisplayMessageBody());

	Log.v("SMSFun","Address: " + messages[i].getDisplayOriginatingAddress());

	//If say we wanted to do something based on who sent it

	if (messages[i].getDisplayOriginatingAddress().contains("2125551212"))

         {

	// we could launch an activity and pass the data

	Intent newintent = new Intent(ctx, SecretMessage.class);
	newintent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);         // Pass in data

	newintent.putExtra("address", messages[i].getDisplayOriginatingAddress());

	newintent.putExtra("message", messages[i].getDisplayMessageBody());

	ctx.startActivity(newintent);         

	}

	}

	}

	}








## 5. Финальный аккорд. Создадим Activity для отображения сообщения.


	import android.app.Activity;

	import android.os.Bundle;

	import android.widget.TextView;

	public class SecretMessage extends Activity {

    public void onCreate(Bundle savedInstanceState) {

        super.onCreate(savedInstanceState);

        setContentView(R.layout.secret);

        // Get the extra data

        Bundle extras = getIntent().getExtras(); 

        String address = extras.getString("address");

        String message = extras.getString("message");

        

        TextView addresstv = (TextView) findViewById(R.id.addresstv);

        TextView messagetv = (TextView) findViewById(R.id.messagetv);

        

        messagetv.setText(message);

        addresstv.setText(address);

    }

	}



**xml**

	<?xml version="1.0" encoding="utf-8"?>

	<LinearLayout 
		xmlns:android="http://schemas.android.com/apk/res/android" 
		android:layout_width="wrap_content" 
		android:layout_height="wrap_content">

		<TextView 
			android:layout_height="wrap_content" 
			android:id="@+id/addresstv" 
			android:text="address" 
			android:layout_width="fill_parent"
			android:layout_gravity="fill_horizontal" />

		<TextView 
			android:layout_height="wrap_content" 
			android:id="@+id/messagetv" 
			android:text="message" 
			android:layout_width="fill_parent" 
			android:layout_gravity="fill_horizontal" />

	</LinearLayout>

Если понравилась статья, нажми рассказать друзьям.


Полный код программы [тут](http://android-helper.com.ua/forms).


