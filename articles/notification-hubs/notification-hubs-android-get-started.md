<properties
	pageTitle="Приступая к работе с Центрами уведомлений Azure для приложений Android | Microsoft Azure"
	description="Из этого учебника вы узнаете, как использовать Центры уведомлений Azure для отправки push-уведомлений на устройства Android."
	services="notification-hubs"
	documentationCenter="android"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>
<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.date="12/15/2015"
	ms.author="wesmc"/>

# Приступая к работе с Центрами уведомлений для приложений Android

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Обзор

В этом учебнике показано, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение на платформе Android. Вы создадите пустое приложение Android, которое получает push-уведомления с помощью Google Cloud Messaging (GCM). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью центра уведомлений.

В этом учебнике описывается простой сценарий вещания с использованием Центров уведомлений. Рекомендуем вам изучить следующий учебник, чтобы узнать об использовании Центров уведомлений для охвата определенных пользователей и групп устройств.


## Перед началом работы

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Полный код для этого учебника можно найти на портале [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStarted).


##Предварительные требования

Для работы с данным учебником требуется следующее:

+ ПО Android Studio, которое можно загрузить с <a href="http://go.microsoft.com/fwlink/?LinkId=389797">сайта Android</a>.
+ Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-RU%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started%2F).


Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными Центрам уведомлений для приложений Android.


##Создание проекта с поддержкой Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


##Настройка нового центра уведомлений


[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;7. Перейдите на расположенную сверху вкладку **Настройка**, введите значение **Ключ API**, полученное в предыдущем разделе, а затем нажмите кнопку **Сохранить**.

&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hub-configure-android.png)

Концентратор уведомлений теперь настроен для работы с GCM. Также у вас есть строки подключения, с помощью которых вы можете зарегистрировать приложение для получения уведомлений и отправки push-уведомлений.

##<a id="connecting-app"></a>Подключение приложения к центру уведомлений

###Создание нового проекта Android

1. В Android Studio создайте новый проект Android Studio.

   	![][13]

2. Выберите форм-фактор **Phone and Tablet** (Телефон и планшет) и минимальную версию пакета SDK (с помощью параметра **Minimum SDK**), которые нужно поддерживать. Нажмите кнопку **Далее**.

   	![][14]

3. Выберите для основного действия значение **Blank Activity** (Пустое действие), щелкните **Next** (Далее), а затем нажмите кнопку **Finish** (Готово).

###Добавление служб Google Play в проект

[AZURE.INCLUDE [Добавление служб Play](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

###Добавление кода

1. На странице [Notification-Hubs-Android-SDK сайта Bintray](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4) на вкладке **Files** (Файлы) скачайте файл notification-hubs-0.4.jar. Перетащите файлы прямо в папку **libs** в окне представления проекта Android Studio. Затем щелкните файл правой кнопкой мыши и выберите пункт **Add as Library** (Добавить как библиотеку).
  
2. В файле Build.Gradle **приложения** добавьте следующую строку в раздел **dependencies**.

	    compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'

	После раздела **dependencies** добавьте следующий репозиторий:

		repositories {
		    maven {
		        url "http://dl.bintray.com/microsoftazuremobile/SDK"
		    }
		}

3. Настройте приложение для получения registrationId из GCM и используйте его, чтобы зарегистрировать экземпляр приложения в центре уведомлений.

	Добавьте следующие разрешения в файл AndroidManifest.xml прямо под тегом `</application>`. Обязательно замените `<your package>` именем пакета, отображенным в верхней части файла AndroidManifest.xml (в этом примере — `com.example.testnotificationhubs`).

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

		<permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
		<uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

3. В класс **MainActivity** над объявлением класса добавьте следующие операторы `import`:

		import android.app.AlertDialog;
		import android.content.DialogInterface;
		import android.os.AsyncTask;
		import com.google.android.gms.gcm.*;
		import com.microsoft.windowsazure.messaging.*;
		import com.microsoft.windowsazure.notifications.NotificationsManager;
		import android.widget.Toast;



4. В верхней части класса добавьте следующие частные члены.

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;
    	private String HubName = "<Enter Your Hub Name>";
		private String HubListenConnectionString = "<Your default listen connection string>";
	    private static Boolean isVisible = false;


	Обязательно обновите три заполнителя: 
	* **SENDER\_ID** — укажите для `SENDER_ID` номер проекта, полученный ранее из проекта, созданного в [консоли Google Cloud](http://cloud.google.com/console). 
	* **HubListenConnectionString** — укажите для `HubListenConnectionString` следующую строку подключения к вашему центру: **DefaultListenAccessSignature**. Вы можете скопировать эту строку подключения: на [классическом портале Azure] на вкладке **Панель мониторинга** центра щелкните элемент **Просмотреть строку подключения**. 
	* **HubName** — имя центра уведомлений, которое отображается в верхней части страницы в Azure для вашего центра (**не** полный URL-адрес). Например, можно использовать `"myhub"`.



5. В метод **OnCreate** класса **MainActivity** добавьте следующий код для регистрации в центре уведомлений в момент создания действия.

        MyHandler.mainActivity = this;
        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
        gcm = GoogleCloudMessaging.getInstance(this);
        hub = new NotificationHub(HubName, HubListenConnectionString, this);
        registerWithNotificationHubs();

6. В **MainActivity.java** добавьте приведенный ниже код для метода **registerWithNotificationHubs()**. Этот метод сообщает об успешном выполнении после регистрации в Google Cloud Messaging и центре уведомлений.

    	@SuppressWarnings("unchecked")
    	private void registerWithNotificationHubs() {
        	new AsyncTask() {
            	@Override
            	protected Object doInBackground(Object... params) {
                	try {
                    	String regid = gcm.register(SENDER_ID);
                    ToastNotify("Registered Successfully - RegId : " +
						hub.register(regid).getRegistrationId());
                	} catch (Exception e) {
                    	ToastNotify("Registration Exception Message - " + e.getMessage());
                    	return e;
                	}
                	return null;
            	}
        	}.execute(null, null, null);
    	}


7. Добавьте к действию метод `ToastNotify`, чтобы отобразить уведомление, когда приложение запущено и отображается. Кроме того, переопределите параметры `onStart`, `onPause`, `onResume` и `onStop`. Это позволит определить, будет ли действие видимым для отображения всплывающего уведомления.

	    @Override
	    protected void onStart() {
	        super.onStart();
	        isVisible = true;
	    }
	
	    @Override
	    protected void onPause() {
	        super.onPause();
	        isVisible = false;
	    }
	
	    @Override
	    protected void onResume() {
	        super.onResume();
	        isVisible = true;
	    }
	
	    @Override
	    protected void onStop() {
	        super.onStop();
	        isVisible = false;
	    }

	    public void ToastNotify(final String notificationMessage)
	    {
	        if (isVisible == true)
	            runOnUiThread(new Runnable() {
	                @Override
	                public void run() {
	                    Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
	                }
	            });
	    }

8. Поскольку Android не отображает уведомлений, необходимо написать собственный приемник. В файле **AndroidManifest.xml** добавьте следующий элемент в середину элемента `<application>`.

	> [AZURE.NOTE] Замените заполнитель на имя своего пакета.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>


9. В представлении проекта разверните узел **app** > **src** > **main** > **java**. Щелкните правой кнопкой мыши папку пакета в **java**, щелкните **New** (Создать) и выберите **Java Class** (Класс Java).

	![][6]

10. В поле **Name** (Имя) для нового класса введите **MyHandler**, затем нажмите кнопку **ОК**.


11. Добавьте следующие операторы import в начало файла **MyHandler.java**:

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		import com.microsoft.windowsazure.notifications.NotificationsHandler;


12. Обновите объявление класса следующим образом, чтобы сделать `MyHandler` подклассом `com.microsoft.windowsazure.notifications.NotificationsHandler`, как показано ниже.

		public class MyHandler extends NotificationsHandler {


13. Добавьте в класс `MyHandler` следующий код:

	Этот код переопределяет метод `OnReceive` так, чтобы обработчик отображал полученные уведомления в виде всплывающего уведомления. Кроме того, обработчик отправляет уведомление в диспетчер уведомлений Android с помощью метода `sendNotification()`.

    	public static final int NOTIFICATION_ID = 1;
    	private NotificationManager mNotificationManager;
    	NotificationCompat.Builder builder;
    	Context ctx;

    	static public MainActivity mainActivity;

    	@Override
    	public void onReceive(Context context, Bundle bundle) {
        	ctx = context;
        	String nhMessage = bundle.getString("message");

        	sendNotification(nhMessage);
	        mainActivity.ToastNotify(nhMessage);
    	}

    	private void sendNotification(String msg) {
        	mNotificationManager = (NotificationManager)
                ctx.getSystemService(Context.NOTIFICATION_SERVICE);

        	PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);

			NotificationCompat.Builder mBuilder =
				new NotificationCompat.Builder(ctx)
					.setSmallIcon(R.mipmap.ic_launcher)
					.setContentTitle("Notification Hub Demo")
					.setStyle(new NotificationCompat.BigTextStyle()
					.bigText(msg))
					.setContentText(msg);

			mBuilder.setContentIntent(contentIntent);
			mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		}

14. В Android Studio в строке меню щелкните **Build** (Сборка) > **Rebuild Project** (Повторить сборку проекта), чтобы убедиться в отсутствии ошибок.

##Отправка уведомлений



Получение уведомлений в приложении можно проверить, отправив уведомления на [классическом портале Azure] на вкладке отладки центра уведомлений, как показано на следующем снимке экрана.

![][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## (Необязательно) Отправка уведомлений из приложения


1. В представлении проекта Android Studio разверните узел **App** > **src** > **main** > **res** > **layout**. Откройте файл макета **activity\_main.xml** и щелкните вкладку **Text** (Текст), чтобы обновить текстовое содержимое файла. Добавьте в него приведенный ниже код, который добавляет новые элементы управления `Button` и `EditText` для отправки уведомлений в центр уведомлений. Добавьте этот код в конец, непосредственно перед `</RelativeLayout>`.

	    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true"
        android:onClick="sendNotificationButtonOnClick" />

	    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_above="@+id/sendbutton"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="42dp"
        android:hint="@string/notification_message_hint" />

2. Добавьте следующую строку в раздел `android` файла **build.gradle**.

		useLibrary 'org.apache.http.legacy'

3. В представлении проекта в Android Studio разверните узел **app** > **src** > **main** > **res** > **values**. Откройте файл **strings.xml** и добавьте строковые параметры, на которые ссылаются новые элементы управления `Button` и `EditText`. Добавьте их в конец файла, непосредственно перед `</resources>`.

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


4. В файле **MainActivity.java** над объявлением класса `MainActivity` добавьте следующие операторы `import`:

		import java.net.URLEncoder;
		import javax.crypto.Mac;
		import javax.crypto.spec.SecretKeySpec;

		import android.util.Base64;
		import android.view.View;
		import android.widget.EditText;

		import org.apache.http.HttpResponse;
		import org.apache.http.client.HttpClient;
		import org.apache.http.client.methods.HttpPost;
		import org.apache.http.entity.StringEntity;
		import org.apache.http.impl.client.DefaultHttpClient;


5. В файле **MainActivity.java** в начало класса `MainActivity` добавьте следующие члены:

	Добавьте в `HubFullAccess` следующую строку подключения к центру: **DefaultFullSharedAccessSignature**. Чтобы скопировать эту строку подключения на [классическом портале Azure], на вкладке **Панель мониторинга** центра уведомлений щелкните **Просмотреть строку подключения**.

	    private String HubEndpoint = null;
	    private String HubSasKeyName = null;
	    private String HubSasKeyValue = null;
		private String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";

6. Действие содержит имя центра и строку подключения с полным общим доступом к центру. Необходимо создать токен подписи программного доступа (SaS) для аутентификации запроса POST для отправки сообщений в центр уведомлений. Чтобы это сделать, проанализируйте данные ключа из строки подключения и создайте токен SaS, как упоминалось в справочнике по [основным понятиям](http://msdn.microsoft.com/library/azure/dn495627.aspx) REST API.

	В **MainActivity.java** добавьте в класс `MainActivity` следующий метод для анализа строки подключения.

	    /**
    	 * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
    	 * to parse the connection string so a SaS authentication token can be
    	 * constructed.
    	 *
    	 * @param connectionString This must be the DefaultFullSharedAccess connection
    	 *                         string for this example.
	     */
	    private void ParseConnectionString(String connectionString)
	    {
	        String[] parts = connectionString.split(";");
	        if (parts.length != 3)
	            throw new RuntimeException("Error parsing connection string: "
	                    + connectionString);

	        for (int i = 0; i < parts.length; i++) {
	            if (parts[i].startsWith("Endpoint")) {
	                this.HubEndpoint = "https" + parts[i].substring(11);
	            } else if (parts[i].startsWith("SharedAccessKeyName")) {
	                this.HubSasKeyName = parts[i].substring(20);
	            } else if (parts[i].startsWith("SharedAccessKey")) {
	                this.HubSasKeyValue = parts[i].substring(16);
	            }
	        }
	    }

7. В **MainActivity.java** добавьте в класс `MainActivity` следующий метод для создания токена аутентификации SaS.

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
         * construct a SaS token from the access key to authenticate a request.
         *
         * @param uri The unencoded resource URI string for this operation. The resource
         *            URI is the full URI of the Service Bus resource to which access is
         *            claimed. For example,
         *            "http://<namespace>.servicebus.windows.net/<hubName>"
         */
        private String generateSasToken(String uri) {

            String targetUri;
            try {
                targetUri = URLEncoder
                        .encode(uri.toString().toLowerCase(), "UTF-8")
                        .toLowerCase();

                long expiresOnDate = System.currentTimeMillis();
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60 * 1000;
                long expires = expiresOnDate / 1000;
                String toSign = targetUri + "\n" + expires;

                // Get an hmac_sha1 key from the raw key bytes
                byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
                SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");

                // Get an hmac_sha1 Mac instance and initialize with the signing key
                Mac mac = Mac.getInstance("HmacSHA256");
                mac.init(signingKey);

                // Compute the hmac on input data bytes
                byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));

            	// Using android.util.Base64 for Android Studio instead of
	            // Apache commons codec
                String signature = URLEncoder.encode(
                        Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");

                // Construct authorization string
                String token = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
                return token;
            } catch (Exception e) {
                DialogNotify("Exception Generating SaS",e.getMessage().toString());
            }

            return null;
        }


8. В **MainActivity.java** добавьте в класс `MainActivity` следующий метод для обработки нажатия кнопки **Send Notification** и отправки уведомления в центр с помощью API REST.

        /**
         * Send Notification button click handler. This method parses the
         * DefaultFullSharedAccess connection string and generates a SaS token. The
         * token is added to the Authorization header on the POST request to the
         * notification hub. The text in the editTextNotificationMessage control
         * is added as the JSON body for the request to add a GCM message to the hub.
         *
         * @param v
         */
        public void sendNotificationButtonOnClick(View v) {
            EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
            final String json = "{"data":{"message":"" + notificationText.getText().toString() + ""}}";

            new Thread()
            {
                public void run()
                {
                    try
                    {
                        HttpClient client = new DefaultHttpClient();

                        // Based on reference documentation...
                        // http://msdn.microsoft.com/library/azure/dn223273.aspx
                        ParseConnectionString(HubFullAccess);
                        String url = HubEndpoint + HubName + "/messages/?api-version=2015-01";
                        HttpPost post = new HttpPost(url);

                        // Authenticate the POST request with the SaS token
                        post.setHeader("Authorization", generateSasToken(url));

                        // JSON content for GCM
                        post.setHeader("Content-Type", "application/json;charset=utf-8");

                        // Notification format should be GCM
                        post.setHeader("ServiceBusNotification-Format", "gcm");
                        post.setEntity(new StringEntity(json));

                        HttpResponse response = client.execute(post);
                    }
                    catch(Exception e)
                    {
                        DialogNotify("Exception",e.getMessage().toString());
                    }
                }
            }.start();
        }



##Тестирование приложения

####Тестирование в эмуляторе

Если требуется выполнить тестирование в эмуляторе, убедитесь, что образ эмулятора поддерживает уровень API Google, выбранный для приложения. Если образ не поддерживает API-интерфейсы Google, будет создано исключение **SERVICE\_NOT\_AVAILABLE**.

Кроме того, убедитесь, что вы добавили учетную запись Google в запущенный эмулятор. Для этого щелкните **Параметры** > **Учетные записи**. В противном случае попытки регистрации в GCM могут привести к порождению исключения **AUTHENTICATION\_FAILED**.

####Тестирование приложения

1. Запустите приложение и убедитесь, что для успешной регистрации сообщается идентификатор регистрации.

   	![][18]

2. Введите сообщение уведомления для отправки на все устройства Android, которые зарегистрированы в центре.

   	![][19]

3. Нажмите кнопку **Send Notification** (Отправить уведомление). На всех устройствах, на которых запущено приложение, будет показано `AlertDialog` с сообщением уведомления. На устройствах, на которых приложение не запущено, но которые были ранее зарегистрированы для получения уведомлений, полученное уведомление будет добавлено в диспетчер уведомлений. Уведомления можно просматривать, проводя пальцем вниз от левого верхнего угла.

   	![][21]

##Дальнейшие действия

В этом простом примере осуществляется рассылка уведомлений на все устройства Windows через портал или консольное приложение. На следующем этапе мы рекомендуем ознакомиться с руководством [Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET]. В нем описана процедура отправки уведомлений с сервера ASP.NET определенным пользователям с использованием тегов.

Если необходимо разделить пользователей по группам интересов, см. раздел [Использование концентраторов уведомлений для передачи экстренных новостей].

Дополнительные сведения о Центрах уведомлений см. в статье [Общие сведения о Центрах уведомлений].




<!-- Images. -->
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png

[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png

[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png


<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[классическом портале Azure]: https://manage.windowsazure.com/
[Общие сведения о Центрах уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET]: notification-hubs-aspnet-backend-android-notify-users.md
[Использование концентраторов уведомлений для передачи экстренных новостей]: notification-hubs-aspnet-backend-android-breaking-news.md

<!----HONumber=AcomDC_0128_2016-->