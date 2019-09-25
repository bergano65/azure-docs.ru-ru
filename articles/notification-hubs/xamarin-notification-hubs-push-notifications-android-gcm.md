---
title: Отправка push-уведомлений в приложения Xamarin.Android с помощью Центров уведомлений Azure | Документация Майкрософт
description: Из этого учебника вы узнаете, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение Xamarin.Android.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/01/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 08/01/2019
ms.openlocfilehash: cba84b0f07db3a69cc964e47657a3f6b64659e6a
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213564"
---
# <a name="tutorial-push-notifications-to-xamarinandroid-apps-using-azure-notification-hubs"></a>Руководство по Отправка push-уведомлений в приложения Xamarin.Android с помощью Центров уведомлений Azure

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Обзор

В этом учебнике показано, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение Xamarin.Android. Вы создадите пустое приложение Xamarin.Android, которое получает push-уведомления с помощью Firebase Cloud Messaging. Вы сможете рассылать через Центр уведомлений push-уведомления на все устройства, где запущено ваше приложение. Готовый код доступен в примере [приложения NotificationHubs](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/Xamarin/GetStartedXamarinAndroid).

При работе с этим руководством вы выполните следующие задачи:

> [!div class="checklist"]
> * создадите проект Firebase и включите Firebase Cloud Messaging;
> * Создание концентратора уведомлений
> * создадите приложение Xamarin.Android и подключите его к центру уведомлений;
> * отправите тестовые уведомления с портала Azure.

## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure**. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) Azure, прежде чем начинать работу.
* [Visual Studio с Xamarin] на компьютере Windows или [Visual Studio для Mac] на компьютере OS X.
* Активная учетная запись Google

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>создадите проект Firebase и включите Firebase Cloud Messaging;

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging-xamarin.md)]

## <a name="create-a-notification-hub"></a>Создание концентратора уведомлений

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcmfcm-settings-for-the-notification-hub"></a>Настройка параметров GCM/FCM для центра уведомлений

1. Выберите **Google (GCM/FCM)/** в разделе **Параметры** в меню слева.
2. Введите **ключ сервера**, записанный c консоли Google Firebase.
3. На панели инструментов щелкните **Сохранить**.

    ![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Концентратор уведомлений настроен для работы с GCM. Также у вас есть строки подключения, с помощью которых вы можете зарегистрировать приложение для получения уведомлений и отправки push-уведомлений.

## <a name="create-a-xamarinandroid-app-and-connect-it-to-notification-hub"></a>Создание приложения Xamarin.Android и его подключение к концентратору уведомлений

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Создание проекта Visual Studio и добавление пакетов NuGet

> [!NOTE]
> Действия, описанные в этом руководстве, предназначены для Visual Studio 2017. 

1. В Visual Studio откройте меню **Файл** и выберите **Создать**, а затем — **Проект**. В окне **Новый проект** сделайте следующее.
    1. Разверните **Установлено**, **Visual C#** , а затем нажмите кнопку **Android**.
    2. Из списка выберите **приложение Android (Xamarin)** .
    3. Введите **имя** проекта.
    4. Выберите **расположение** для проекта.
    5. Нажмите кнопку **ОК**.

        ![Диалоговое окно "Новый проект"](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog-new.png)
2. В диалоговом окне **нового приложения Android** выберите **Пустое приложение**и щелкните **ОК**.

    ![Диалоговое окно "Новый проект"](./media/partner-xamarin-notification-hubs-android-get-started/new-android-app-dialog.png)
3. В окне **обозревателя решений** разверните раздел **Свойства** и выберите **AndroidManifest.xml**. Обновите имя пакета в соответствии с именем пакета, которое вы указали при добавлении Firebase Cloud Messaging в проект в консоли Google Firebase.

    ![Имя пакета в GCM](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
4. Установите в качестве целевой версии Android для проекта **Android 9.0 (pie)** , выполнив следующие действия. 
    1. Щелкните проект правой кнопкой мыши и выберите пункт **Свойства**. 
    1. В поле **Compile using Android version: (Target framework)** (Компиляции с использованием версии Android: целевая платформа) выберите **Android 9.0 (Pie)** . 
    1. Выберите **Да** в окне сообщения, чтобы продолжить изменение целевой платформы.
1. Добавьте необходимые пакеты NuGet в проект, выполнив следующие действия.
    1. Щелкните проект правой кнопкой мыши и выберите **Управление пакетами NuGet**.
    1. Перейдите на вкладку **Установленные**, выберите **Xamarin.Android.Support.Design** и щелкните **Обновить** в правой панели, чтобы обновить пакет до последней версии.
    1. Перейдите на вкладку **Обзор**. Найдите **Xamarin.GooglePlayServices.Base**. Выберите **Xamarin.GooglePlayServices.Base** в списке результатов. Щелкните **Установить**.

        ![Пакет NuGet для сервисов Google Play](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
    6. В окне **диспетчера пакетов NuGet** выполните поиск пакета **Xamarin.Firebase.Messaging**. В списке результатов выберите **Xamarin.Firebase.Messaging**. Щелкните **Установить**.
    7. Затем найдите пакет **Xamarin.Azure.NotificationHubs.Android**. В списке результатов выберите **Xamarin.Azure.NotificationHubs.Android**. Щелкните **Установить**.

### <a name="add-the-google-services-json-file"></a>Добавление JSON-файла сервисов Google

1. Скопируйте файл `google-services.json`, который вы скачали с консоли Google Firebase в папку проекта.
2. Добавьте `google-services.json` в проект.
3. Выберите `google-services.json` в окне **обозревателя решений**.
4. В области **свойств** для действия сборки выберите **GoogleServicesJson**. Если вы не видите **GoogleServicesJson**, закройте Visual Studio, перезапустите ее, затем повторно откройте проект и повторите попытку.

    ![Действие сборки GoogleServicesJson](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Настройка центров уведомлений в проекте

#### <a name="registering-with-firebase-cloud-messaging"></a>Регистрация в Firebase Cloud Messaging

1. Откройте файл `AndroidManifest.xml` и вставьте следующие элементы `<receiver>` в элемент `<application>`:

    ```xml
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

2. Добавьте следующие операторы **перед приложением**.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    ```

3. Подготовьте следующую информацию для вашего приложения Android и концентратора уведомлений.

   * **Строка подключения для ожидания передачи данных**. На [портале Azure] на панели мониторинга выберите **Просмотреть строки подключения**. Скопируйте строку подключения для `DefaultListenSharedAccessSignature` для этого значения.
   * **Имя центра** — это имя вашего центра на [портале Azure]. Например, *mynotificationhub2*.
4. В окне **обозревателя решений** щелкните **проект** правой кнопкой мыши, выберите **Добавить**, а затем — **Класс**.
5. Создайте класс `Constants.cs` для вашего проекта Xamarin и определите в классе следующие постоянные значения. Замените значения заполнителей на собственные значения.

    ```csharp
    public static class Constants
    {
        public const string ListenConnectionString = "<Listen connection string>";
        public const string NotificationHubName = "<hub name>";
    }
    ```

6. Добавьте указанные ниже операторы using в `MainActivity.cs`.

    ```csharp
    using Android.Util;
    using Android.Gms.Common;
    ```

7. Добавьте следующие свойства в класс MainActivity. Переменная TAG будет использоваться для отображение диалогового окна оповещения при выполнении приложения.

    ```csharp
    public const string TAG = "MainActivity";
    internal static readonly string CHANNEL_ID = "my_notification_channel";
    ```

8. Добавьте следующий метод в класс MainActivity. Он проверяет, доступны ли **Сервисы Google Play** на устройстве.

    ```csharp
    public bool IsPlayServicesAvailable()
    {
        int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.Success)
        {
            if (GoogleApiAvailability.Instance.IsUserResolvableError(resultCode))
                Log.Debug(TAG, GoogleApiAvailability.Instance.GetErrorString(resultCode));
            else
            {
                Log.Debug(TAG, "This device is not supported");
                Finish();
            }
            return false;
        }

        Log.Debug(TAG, "Google Play Services is available.");
        return true;
    }
    ```

9. В класс MainActivity добавьте следующий метод, создающий канал уведомлений.

    ```csharp
    private void CreateNotificationChannel()
    {
        if (Build.VERSION.SdkInt < BuildVersionCodes.O)
        {
            // Notification channels are new in API 26 (and not a part of the
            // support library). There is no need to create a notification
            // channel on older versions of Android.
            return;
        }

        var channelName = CHANNEL_ID;
        var channelDescription = string.Empty;
        var channel = new NotificationChannel(CHANNEL_ID, channelName, NotificationImportance.Default)
        {
            Description = channelDescription
        };

        var notificationManager = (NotificationManager)GetSystemService(NotificationService);
        notificationManager.CreateNotificationChannel(channel);
    }
    ```

10. Затем в классе `MainActivity.cs` добавьте следующий код в `OnCreate` после `base.OnCreate(savedInstanceState)`.

    ```csharp
    if (Intent.Extras != null)
    {
        foreach (var key in Intent.Extras.KeySet())
        {
            if(key!=null)
            {
                var value = Intent.Extras.GetString(key);
                Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
            }
        }
    }

    IsPlayServicesAvailable();
    CreateNotificationChannel();
    ```

15. Добавьте класс с именем `MyFirebaseMessagingService` в свой проект. 
16. Добавьте указанные ниже операторы using в `MyFirebaseMessagingService.cs`.

    ```csharp
    using Android.Util;
    using Firebase.Messaging;
    using Android.Support.V4.App;    
    using WindowsAzure.Messaging;
    ```

17. Добавьте следующий код в класс перед объявлением class и настройте наследование класса из `FirebaseMessagingService`.

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class MyFirebaseMessagingService : FirebaseMessagingService
    ```

18. Добавьте в файл `MyFirebaseMessagingService.cs` следующий код для обработки полученных сообщений. 

    ```csharp
        const string TAG = "MyFirebaseMsgService";
        NotificationHub hub;
    
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            if (message.GetNotification() != null)
            {
                //These is how most messages will be received
                Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
                SendNotification(message.GetNotification().Body);
            }
            else
            {
                //Only used for debugging payloads sent from the Azure portal
                SendNotification(message.Data.Values.First());
    
            }
        }
    
        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);
    
            var notificationBuilder = new NotificationCompat.Builder(this, MainActivity.CHANNEL_ID);
    
            notificationBuilder.SetContentTitle("FCM Message")
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(messageBody)
                        .SetAutoCancel(true)
                        .SetShowWhen(false)
                        .SetContentIntent(pendingIntent);
    
            var notificationManager = NotificationManager.FromContext(this);
    
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```

19. Добавьте в класс MyFirebaseMessagingService следующие методы, чтобы получить маркер регистрации FCM и отправить его в экземпляр Центров уведомлений (концентратор). 

    ```csharp
        public override void OnNewToken(string token)
        {
            Log.Debug(TAG, "FCM token: " + token);
            SendRegistrationToServer(token);
        }

        void SendRegistrationToServer(string token)
        {
            // Register with Notification Hubs
            hub = new NotificationHub(Constants.NotificationHubName,
                                        Constants.ListenConnectionString, this);

            var tags = new List<string>() { };
            var regID = hub.Register(token, tags.ToArray()).RegistrationId;

            Log.Debug(TAG, $"Successful registration of ID {regID}");
        }
    ```
1. **Выполните сборку** проекта.
1. **Запустите** приложение на устройстве или в загруженном эмуляторе.

## <a name="send-test-notification-from-the-azure-portal"></a>Отправка тестового уведомления с портала Azure

Можно проверить, поступают ли в приложение уведомления, с помощью параметра **Тестовая отправка** на [портале Azure]. Этот параметр позволяет отправить на устройство тестовое push-уведомление.

![Портал Azure — тестовая отправка](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

Push-уведомления обычно отправляются в серверной службе, например в мобильной службе или ASP.NET, с помощью совместимой библиотеки. Если библиотека недоступна для серверной части, можно также непосредственно использовать REST API для отправки сообщений с уведомлениями.

## <a name="next-steps"></a>Дополнительная информация

В рамках этого руководства вы отправили широковещательные уведомления на все устройства Android, зарегистрированные в серверной части. Чтобы узнать, как отправлять push-уведомления на конкретные устройства Android, перейдите к следующему руководству:

> [!div class="nextstepaction"]
>[Руководство по отправке push-уведомлений на конкретные устройства Android с помощью Центров уведомлений Azure и Google Cloud Messaging](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio с Xamarin]: https://docs.microsoft.com/visualstudio/install/install-visual-studio
[Visual Studio для Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[портале Azure]: https://portal.azure.com/
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Android]: https://msdn.microsoft.com/library/dn282661.aspx
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[GitHub]: https://github.com/Azure/azure-notificationhubs-android
