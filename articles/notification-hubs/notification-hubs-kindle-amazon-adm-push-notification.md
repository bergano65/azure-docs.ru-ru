---
title: Отправка push-уведомлений в приложения Kindle с помощью Центров уведомлений Azure | Документация Майкрософт
description: Из этого учебника вы узнаете, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение Kindle.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 346fc8e5-294b-4e4f-9f27-7a82d9626e93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-kindle
ms.devlang: Java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/29/2019
ms.author: jowargo
ms.openlocfilehash: edd0e12460e07cfd2990cc43a9056ed06b84fb1d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927017"
---
# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Приступая к работе с Центрами уведомлений для приложений Kindle

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

В этом учебнике показано, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение Kindle. Вы создадите пустое приложение Kindle, которое получает push-уведомления с помощью Amazon Device Messaging (ADM).

В рамках этого руководства создается или обновляется код для выполнения следующих задач:

> [!div class="checklist"]
> * Добавление нового приложения на портал разработчика
> * Создание ключа API
> * Создание и настройка центра уведомлений
> * Настройка приложения
> * Создание обработчика сообщений ADM
> * Добавление ключа API в приложение
> * Запуск приложения
> * отправка проверочного уведомления.

## <a name="prerequisites"></a>Предварительные требования

- [Android Studio](https://developer.android.com/studio/?gclid=CjwKCAjwwZrmBRA7EiwA4iMzBPZ9YYmR0pbb5LtjnWhWCxe8PWrmjmeaR6ad5ksCr_j2mmkVj_-o6hoCAqwQAvD_BwE).
- Следуйте указаниям, приведенным в статье [Настройка среды разработки](https://developer.amazon.com/docs/fire-tablets/ft-set-up-your-development-environment.html), для настройки среды разработки для Kindle.

## <a name="add-a-new-app-to-the-developer-portal"></a>Добавление нового приложения на портал разработчика

1. Войдите на [Портал разработчика Amazon](https://developer.amazon.com/apps-and-games/console/apps/list.html).
2. Нажмите кнопку **Добавить новое приложение**, а затем выберите **Android**.  

    ![Кнопка "Добавить новое приложение"](./media/notification-hubs-kindle-get-started/add-new-app-button.png)
1. На странице **New App Submission** (Отправка нового приложения) выполните следующие действия, чтобы получить **ключ приложения**.
    1. Введите **имя** приложения.
    2. Выберите любую **категорию** (например, образование).
    4. Введите адрес электронной почты в поле **Customer support email address** (Адрес электронной почты службы поддержки клиентов). 
    5. Щелкните **Сохранить**.

        ![Страница отправки нового приложения](./media/notification-hubs-kindle-get-started/new-app-submission-page.png) 
2.  В верхней части перейдите на вкладку **Mobile Ads** (Мобильная реклама) и сделайте следующее: 
    1. Укажите, предназначено ли ваше приложение в первую очередь для детей младше 13 лет. Для работы с этим учебником выберите **No** (Нет).
    2. Нажмите кнопку **Submit** (Отправить). 

        ![Страница мобильной рекламы](./media/notification-hubs-kindle-get-started/mobile-ads-page.png)
    3. Скопируйте **ключ приложения** со страницы **Mobile Ads** (Мобильная реклама). 

        ![Ключ приложения](./media/notification-hubs-kindle-get-started/application-key.png)
3.  Щелкните меню **Apps & Services** (Приложения и службы) вверху и выберите свое приложение из списка. 

    ![Выбор приложения из списка](./media/notification-hubs-kindle-get-started/all-apps-select.png)
4. Перейдите на вкладку **Device Messaging** (Передача сообщений на устройства) и сделайте следующее: 
    1. Щелкните **Create a New Security Profile** (Создать профиль безопасности).
    2. Введите **имя** для профиля безопасности. 
    3. Введите **описание** для профиля безопасности. 
    4. Щелкните **Сохранить**. 
    5. Выберите **View Security Profile** (Просмотреть профиль безопасности) на странице результатов. 
5. Теперь на странице **Security Profile** (Профиль безопасности) сделайте следующее: 
    1. Перейдите на вкладку **Web Settings** (​​Веб-настройки) и скопируйте значение **идентификатора клиента** и **секрета клиента** для дальнейшего использования. 

        ![Получение идентификатора клиента и секрета](./media/notification-hubs-kindle-get-started/client-id-secret.png) 
    2. Перейдите на страницу **Android/Kindle Settings** (Параметры Android/Kindle) и оставьте ее открытой. Вы введете эти значения в следующем разделе. 

## <a name="create-an-api-key"></a>Создание ключа API
1. Откройте окно командной строки с правами администратора.
2. Перейдите в папку пакета Android SDK.
3. Введите следующую команду:

    ```shell
    keytool -list -v -alias androiddebugkey -keystore ./debug.keystore
    ```
4. Для пароля **keystore** введите **android**.
5. Скопируйте отпечатки **MD5** и **SHA256**. 
6. Вернувшись на портал разработчика, на вкладке **Android/Kindle Settings** (Параметры Android/Kindle) сделайте следующее: 
    1. Введите **название ключа API**. 
    2. Введите **имя пакета** для вашего приложения (например, **com.fabrikam.mykindleapp**) и значение **MD5**.
        
        >[!IMPORTANT]
        > При создании приложения в Android Studio используйте имя пакета, указанное здесь. 
    1. Вставьте **подпись MD5**, которую вы скопировали ранее. 
    2. Вставьте **подпись SHA256**, которую вы скопировали ранее.  
    3. Выберите **Generate New Key** (Создать ключ).

        ![Параметры Android/Kindle — создание ключа](./media/notification-hubs-kindle-get-started/android-kindle-settings.png)
    4. Теперь выберите из списка **Show** (Показать), чтобы увидеть ключ API. 

        ![Параметры Android/Kindle — отображение ключа API](./media/notification-hubs-kindle-get-started/show-api-key-button.png) 
    5. В окне **API Key Details** (Сведения о ключе API) скопируйте ключ API и сохраните его где-нибудь. Щелкните **X** в правом верхнем углу, чтобы закрыть окно. 


## <a name="create-and-configure-a-notification-hub"></a>Создание и настройка центра уведомлений

1. Выполните шаги, приведенные в статье [Создание центра уведомлений Azure с помощью портала Azure](create-notification-hub-portal.md), чтобы создать центр уведомлений. 
2. Щелкните **Amazon (ADM)** в меню **Settings** (Параметры).
3. Вставьте **идентификатор клиента** и **секрет**, сохраненные ранее. 
4. На панели инструментов щелкните **Сохранить**. 

    ![Настройка параметров ADM для центра уведомлений](./media/notification-hubs-kindle-get-started/configure-notification-hub.png)
5. Выберите **Политики доступа** в левом меню и нажмите кнопку **Копировать** для строки подключения политики **DefaultListenSharedAccessSignature**. Сохраните его где-либо. Она будет использоваться далее в исходном коде. 

    ![Строка подключения для ожидания передачи данных центра уведомлений](./media/notification-hubs-kindle-get-started/event-hub-listen-connection-string.png)    

## <a name="set-up-your-application"></a>Настройка приложения

1. Запустите Android Studio. 
2. В меню выберите **File** (Файл), **New** (Создать), а затем — **New Project** (Создать проект). 
3. На странице **Choose your project** (Выбор проекта) выберите **Phone and Tablet** (Телефоны и планшеты), **Empty Activity** (Пустое действие) и нажмите кнопку **Далее**. 
4. В окне **Configure your project** (Настройка проекта) сделайте следующее.
    1. Введите **имя приложения**. Возможно, вы захотите сделать его аналогичным имени приложения, созданного на Портале разработчика Amazon. 
    2. Введите **имя пакета**. 
        
        >[!IMPORTANT]
        >Имя пакета должно соответствовать имени пакета, указанному на Портале разработчика Amazon.
    3. Просмотрите и обновите оставшиеся значения по мере необходимости. 
    4. Выберите **Готово**. 

        ![Настройка проекта Android](./media/notification-hubs-kindle-get-started/new-android-studio-project.png)
5. Скачайте библиотеку [пакета SDK Разработки Amazon для Android](https://developer.amazon.com/sdk-download) на свой жесткий диск. Распакуйте ZIP-файл пакета SDK.
6. В Android Studio измените структуру папок с **Android** на **Project** (Проект), если еще не выбрано значение **Project** (Проект). 

    ![Android Studio — переключение структуры проекта](./media/notification-hubs-kindle-get-started/android-studio-project-view.png)
7. Разверните класс **app**, чтобы увидеть папку **libs** в представлении в виде дерева.     
8. В окне проводника перейдите в папку, в которую вы скачали пакет SDK Amazon для Android.
9. Нажмите клавишу **CTRL** и перетащите файл **amazon-device-messaging-1.0.1.jar** в узел **lib** в представлении в виде дерева. 

    ![Android Studio — добавление JAR-файла Amazon Device Messaging](./media/notification-hubs-kindle-get-started/drag-drop-amazon-device-messaging-jar.png)
9. В окне **Copy** (Копирование) нажмите кнопку **OК**. Если вы видите окно **Move** (Перемещение) вместо окна **Copy** (Копирование), закройте его и попробуйте выполнить операцию перетаскивания с зажатой клавишей **CTRL**. 

    ![Android Studio — копирование JAR-файла](./media/notification-hubs-kindle-get-started/copy-jar-window.png)
10. Добавьте следующий оператор в файл приложения **build.gradle** в разделе **dependencies**: `implementation files('libs/amazon-device-messaging-1.0.1.jar')`. 

    ![Android Studio — добавление ADM в файл приложения build.gradle](./media/notification-hubs-kindle-get-started/adm-build-gradle.png)
11. В файле `Build.Gradle` в классе **app** добавьте следующие строки в раздел **dependencies**. 

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```
12. **После** раздела **dependencies** добавьте следующий репозиторий.

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```
13. В редакторе для файла **build.gradle** для класса **app** щелкните **Sync now** (Синхронизировать сейчас) на панели инструментов. 

    ![Android Studio — синхронизация build.gradle класса app](./media/notification-hubs-kindle-get-started/gradle-sync-now.png)
14. Вернитесь к структуре Android в представлении в виде дерева.  Добавьте пространство имен Amazon в корневом элементе манифеста.

    ```xml
    xmlns:amazon="http://schemas.amazon.com/apk/res/android"
    ```
   
    ![Пространство имен Amazon в манифесте](./media/notification-hubs-kindle-get-started/amazon-namespace-manifest.png)
2. Добавьте разрешения в качестве первого элемента в элементе манифеста. Вместо **[YOUR PACKAGE NAME]** укажите пакет, который вы используете для создания приложения.

    ```xml
    <permission
        android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
        android:protectionLevel="signature" />

    <uses-permission android:name="android.permission.INTERNET"/>

    <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

    <!-- This permission allows your app access to receive push notifications
    from ADM. -->
    <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

    <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    ```
3. Вставьте приведенный ниже элемент в качестве первого потомка элемента приложения. Вместо **[YOUR PACKAGE NAME]** укажите имя пакета, которое вы использовали для создания приложения. Вы создадите класс MyADMMessageHandler в следующем шаге. 

    ```xml
        <amazon:enable-feature
            android:name="com.amazon.device.messaging"
            android:required="true"/>
        <service
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler"
            android:exported="false" />
        <receiver
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler$Receiver"
            android:permission="com.amazon.device.messaging.permission.SEND" >

            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
                <action android:name="com.amazon.device.messaging.intent.RECEIVE" />
                <!-- Replace the name in the category tag with your app's package name. -->
                <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>
    ```

## <a name="create-your-adm-message-handler"></a>Создание обработчика сообщений ADM

1. Добавьте новый класс в пакет `com.fabrikam.mykindleapp` в проекте, который наследуется от `com.amazon.device.messaging.ADMMessageHandlerBase`, и назовите его `MyADMMessageHandler`, как показано на следующем изображении:

    ![Создание класса MyADMMessageHandler](./media/notification-hubs-kindle-get-started/create-adm-message-handler.png)

2. Добавьте в класс `MyADMMessageHandler` следующие операторы `import`:

    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.support.v4.app.NotificationCompat;
    import android.util.Log;
    import com.amazon.device.messaging.ADMMessageReceiver;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    ```

3. Добавьте в созданный класс приведенный ниже код. Запомните `[HUB NAME]` и `[LISTEN CONNECTION STRING]` с именем вашего центра уведомлений и строки подключения для ожидания передачи данных. 

    ```java
    public static final int NOTIFICATION_ID = 1;
    private NotificationManager mNotificationManager;
    NotificationCompat.Builder builder;
    private static NotificationHub hub;

    public static NotificationHub getNotificationHub(Context context) {
        Log.v("com.wa.hellokindlefire", "getNotificationHub");
        if (hub == null) {
            hub = new NotificationHub("[HUB NAME]", "[HUB NAMESPACE CONNECTION STRING]", context);
        }
        return hub;
    }

    public MyADMMessageHandler() {
        super("MyADMMessageHandler");
    }

    @Override
    protected void onMessage(Intent intent) {
        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);
    }

    @Override
    protected void onRegistrationError(String s) {

    }

    @Override
    protected void onRegistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).register(s);
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
        }
    }

    @Override
    protected void onUnregistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).unregister();
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
        }
    }

    public static class Receiver extends ADMMessageReceiver
    {
        public Receiver()
        {
            super(MyADMMessageHandler.class);
        }
    }

    private void sendNotification(String msg) {
        Context ctx = getApplicationContext();

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
    ```

## <a name="add-your-api-key-to-your-app"></a>Добавление ключа API в приложение
1. Выполните следующие действия, чтобы добавить папку ресурсов в проект. 
    1. Переключитесь в представление **Project** (Проект). 
    2. Щелкните правой кнопкой мыши класс **app**.
    3. Нажмите кнопку **Создать**.
    4. Щелкните **Folder** (Папка). 
    5. Затем выберите **Assets Folder** (Папка ресурсов). 

        ![Добавление папки с ресурсами](./media/notification-hubs-kindle-get-started/add-assets-folder-menu.png)    
    6. На странице **Configure Component** (Настройка компонента) сделайте следующее:
        1. Выберите **Change folder location** (Изменить расположение папки).
        2. Убедитесь, что для папки задано значение: `src/main/assets`.
        3. Выберите **Готово**. 
        
            ![Настройка папки ресурсов](./media/notification-hubs-kindle-get-started/configure-asset-folder.png)
2. Добавьте файл с именем **api_key.txt** в папку **assets**. В представлении в виде дерева разверните класс **app**, разверните **src**, затем разверните **main** и щелкните правой кнопкой мыши **assets**, выберите **New** (Создать), а затем — **File** (Файл). Введите **api_key.txt** в поле имени файла. 3. 
5. Скопируйте ключ API, созданный на портале разработчика Amazon, в файл api_key.txt. 
6. Создайте проект. 

## <a name="run-the-app"></a>Запуск приложения
1. На устройстве Kindle сверху щелкните **Параметры**, затем **Моя учетная запись** и зарегистрируйтесь с использованием действующей учетной записи Amazon.
2. Запустите приложение на устройстве Kindle из Android Studio. 

> [!NOTE]
> Если возникает проблема, проверьте время эмулятора (или устройства). Значение времени должно быть точным. Для изменения времени эмулятора Kindle выполните следующую команду из каталога средств для платформы Android SDK:

```shell
adb shell  date -s "yyyymmdd.hhmmss"
```

## <a name="send-a-notification-message"></a>Отправка уведомления

Чтобы отправить сообщение с помощью .NET, используйте следующий код:

```csharp
static void Main(string[] args)
{
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

    hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
}
```

Пример кода см. [на GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/blob/master/Samples/SendPushSample/SendPushSample/Program.cs).

![][7]

## <a name="next-steps"></a>Дополнительная информация

В рамках этого руководства вы отправили широковещательные уведомления на все устройства Kindle, зарегистрированные в серверной части. Чтобы узнать, как отправлять push-уведомления на конкретные устройства Kindle, перейдите к следующему руководству.  В следующем руководстве объясняется, как отправлять push-уведомления на конкретные устройства Android, но можно применить ту же логику при отправке push-уведомлений на конкретные устройства Kindle.

> [!div class="nextstepaction"]
>[Руководство по отправке push-уведомлений на конкретные устройства Android с помощью Центров уведомлений Azure и Google Cloud Messaging](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- URLs. -->
[Amazon developer portal]: https://developer.amazon.com/home.html
[download the SDK]: https://developer.amazon.com/public/resources/development-tools/sdk
[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
