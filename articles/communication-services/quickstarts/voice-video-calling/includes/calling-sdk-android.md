---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: bed2a4ccbe87aef9afa395ed789da393e885cc89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779815"
---
## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно. 
- Развернутый ресурс Служб коммуникации. [Создайте ресурс Служб коммуникации.](../../create-communication-resource.md)
- Объект `User Access Token` для включения клиента вызова. Дополнительные сведения о [том, `User Access Token` как получить](../../access-tokens.md)
- Необязательно. Выполните инструкции из краткого руководства по [началу работы с добавлением вызова в приложение](../getting-started-with-calling.md) .

## <a name="setting-up"></a>Настройка

### <a name="install-the-package"></a>Установка пакета

<!-- TODO: update with instructions on how to download, install and add package to project -->
Выберите build.gradle на уровне проекта и добавьте `mavenCentral()` в список репозиториев в разделах `buildscript` и `allprojects`.
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
Затем на уровне модуля выполните сборку. gradle добавьте следующие строки в раздел зависимостей.

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.2'
    ...
}

```

## <a name="object-model"></a>Объектная модель

Следующие классы и интерфейсы реализуют некоторые основные функции клиентской библиотеки Служб коммуникации Azure для вызовов:

| Имя                                  | Описание                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| CallClient — это основная точка входа в клиентскую библиотеку для вызовов.|
| CallAgent | CallAgent используется для инициирования вызовов и управления ими. |
| CommunicationUserCredential | CommunicationUserCredential используется в качестве учетных данных маркера для создания экземпляра CallAgent.|

## <a name="initialize-the-callclient-create-a-callagent-and-access-the-devicemanager"></a>Инициализация Каллклиент, создание Каллажент и доступ к Девицеманажер

Чтобы создать экземпляр, необходимо `CallAgent` вызвать `createCallAgent` метод для `CallClient` экземпляра. Это асинхронно возвращает `CallAgent` объект экземпляра.
`createCallAgent`Метод принимает в `CommunicationUserCredential` качестве аргумента, который инкапсулирует [маркер доступа](../../access-tokens.md).
Чтобы получить доступ к `DeviceManager` , необходимо сначала создать экземпляр каллажент, а затем использовать `CallClient.getDeviceManager` метод для получения девицеманажер.

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationUserCredential tokenCredential = new CommunicationUserCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = await callClient.createCallAgent((appContext, tokenCredential).get();
DeviceManage deviceManager = await callClient.getDeviceManager().get();
```

## <a name="place-an-outgoing-call-and-join-a-group-call"></a>Размещение исходящего вызова и присоединение к группе

Чтобы создать и запустить вызов, необходимо вызвать `CallAgent.call()` метод и предоставить `Identifier` вызываемые объекты.
Чтобы присоединиться к групповому вызову, необходимо вызвать `CallAgent.join()` метод и предоставить объект groupId. Идентификаторы групп должны быть в формате GUID или UUID.

Создание и запуск вызываются синхронно. Экземпляр Call позволяет подписываться на все события в вызове.

### <a name="place-a-11-call-to-a-user"></a>Размещение 1:1 вызова пользователю
Чтобы поместить вызов другого пользователя служб Communication Services, вызовите `call` метод для `callAgent` и передайте объект с `communicationUserId` ключом.
```java
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
CommunicationUser acsUserId = new CommunicationUser(<USER_ID>);
CommunicationUser participants[] = new CommunicationUser[]{ acsUserId };
call oneToOneCall = callAgent.call(appContext, participants, startCallOptions);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Поместите вызов 1: n с помощью пользователей и PSTN
> [!WARNING]
> В настоящее время вызов PSTN недоступен для размещения пользователя 1: n и номера PSTN, которое необходимо указать для номера телефона вызываемого объекта.
Ресурс служб связи должен быть настроен на разрешение вызова PSTN:
```java
CommunicationUser acsUser1 = new CommunicationUser(<USER_ID>);
PhoneNumber acsUser2 = new PhoneNumber("<PHONE_NUMBER>");
CommunicationIdentifier participants[] = new CommunicationIdentifier[]{ acsUser1, acsUser2 };
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
Call groupCall = callAgent.call(participants, startCallOptions);
```

### <a name="place-a-11-call-with-video-camera"></a>Размещение 1:1 звонка с помощью видеокамеры
> [!WARNING]
> Сейчас поддерживается только один исходящий поток видео для вызова с помощью видео. необходимо перечислить локальные камеры, используя `deviceManager` `getCameraList` API.
После выбора нужной камеры используйте ее для создания `LocalVideoStream` экземпляра и передайте его в `videoOptions` качестве элемента `localVideoStream` массива в `call` метод.
После того как вызов подключится, он автоматически начнет отправлять видеопоток от выбранной камеры другим участникам.
```java
Context appContext = this.getApplicationContext();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
LocalVideoStream currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentVideoStream);

CommunicationUser[] participants = new CommunicationUser[]{ new CommunicationUser("<acs user id>") };
StartCallOptions startCallOptions = new StartCallOptions();
startCallOptions.setVideoOptions(videoOptions);
Call call = callAgent.call(context, participants, startCallOptions);
```

### <a name="join-a-group-call"></a>Вызов группы
Чтобы начать новый вызов группы или присоединиться к текущему вызову группы, необходимо вызвать метод "Join" и передать объект со `groupId` свойством. Значение должно быть идентификатором GUID.
```java
Context appContext = this.getApplicationContext();
GroupCallContext groupCallContext = new groupCallContext("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallContext, joinCallOptions);
```

## <a name="push-notifications"></a>Push-уведомления

### <a name="overview"></a>Обзор
Мобильные push-уведомления — это всплывающие уведомления, отображаемые на мобильных устройствах. Для вызова мы будем сосредоточиться на push-уведомлениях VoIP (голосовой через Интернет). Мы будем регистрироваться для получения push-уведомлений, обработайте push-уведомления, а затем отменяют регистрацию push-уведомлений.

### <a name="prerequisites"></a>Предварительные требования

Чтобы завершить работу с этим разделом, создайте учетную запись Firebase и включите облачные службы обмена сообщениями (FCM). Убедитесь, что Firebase Cloud Messaging подключен к экземпляру центра уведомлений Azure (АНХ). Инструкции см. [в статье Connect Firebase to Azure](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started) .
В этом разделе также предполагается, что вы используете Android Studio версии 3,6 или более поздней для создания приложения.

Для приложения Android требуется набор разрешений, чтобы получать уведомления от Firebase Cloud Messaging. В `AndroidManifest.xml` файле добавьте следующий набор разрешений сразу после * манифеста<... >* или под *</application>* тегом.

```XML
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

### <a name="register-for-push-notifications"></a>Регистрация для получения push-уведомлений

Чтобы зарегистрироваться для получения push-уведомлений, приложению необходимо вызвать `registerPushNotification()` в экземпляре *каллажент* с маркером регистрации устройства.

Чтобы получить маркер регистрации устройства, добавьте клиентскую библиотеку Firebase в файл *Build. gradle* модуля приложения, добавив следующие строки в `dependencies` раздел, если он еще не создан:

```
    // Add the client library for Firebase Cloud Messaging
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

В файле *Build. gradle* на уровне проекта добавьте следующий фрагмент в `dependencies` раздел, если он еще не создан:

```
    classpath 'com.google.gms:google-services:4.3.3'
```

Добавьте следующий подключаемый модуль в начало файла, если он еще не создан:

```
apply plugin: 'com.google.gms.google-services'
```

Выберите *Синхронизировать сейчас* на панели инструментов. Добавьте следующий фрагмент кода, чтобы получить маркер регистрации устройства, созданный клиентской библиотекой Firebase Cloud Messaging для экземпляра клиентского приложения, и добавьте приведенные ниже импортируемые элементы в заголовок главного действия для экземпляра. Они необходимы для получения маркера фрагмента:

```
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.iid.InstanceIdResult;
```

Добавьте следующий фрагмент для получения маркера:

```
        FirebaseInstanceId.getInstance().getInstanceId()
                .addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
                    @Override
                    public void onComplete(@NonNull Task<InstanceIdResult> task) {
                        if (!task.isSuccessful()) {
                            Log.w(TAG, "getInstanceId failed", task.getException());
                            return;
                        }

                        // Get new Instance ID token
                        String deviceToken = task.getResult().getToken();
                        // Log
                        Log.d(TAG, "Device Registration token retrieved successfully");
                    }
                });
```
Зарегистрируйте маркер регистрации устройства в клиентской библиотеке вызывающих служб для входящих push-уведомлений о вызовах:

```java
String deviceRegistrationToken = "some_token";
try {
    callAgent.registerPushNotification(deviceRegistrationToken).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while registering for Incoming Calls Push Notifications.")
}
```

### <a name="push-notification-handling"></a>Обработка push-уведомлений

Чтобы получать входящие push-уведомления о вызовах, вызовите *хандлепушнотификатион ()* в экземпляре *каллажент* с полезной нагрузкой.

Чтобы получить полезные данные из Firebase Cloud Messaging, начните с создания новой службы (файл > новая служба > Service > Service), которая расширяет класс клиентской библиотеки *фиребасемессагингсервице* Firebase и переопределяет `onMessageReceived` метод. Этот метод является обработчиком событий, который вызывается, когда Firebase Cloud Messaging доставляет push-уведомление в приложение.

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private java.util.Map<String, String> pushNotificationMessageData;

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        // Check if message contains a notification payload.
        if (remoteMessage.getNotification() != null) {
            Log.d(TAG, "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
        else {
            pushNotificationMessageData = serializeDictionaryAsJson(remoteMessage.getData());
        }
    }
}
```
Добавьте следующее определение службы в `AndroidManifest.xml` файл внутри <application> тега:

```
        <service
            android:name=".MyFirebaseMessagingService"
            android:exported="false">
            <intent-filter>
                <action android:name="com.google.firebase.MESSAGING_EVENT" />
            </intent-filter>
        </service>
```

После получения полезных данных их можно передать в клиентскую библиотеку служб Communication Services, чтобы обработать ее путем вызова `handlePushNotification` метода в `CallAgent` экземпляре.

```java
java.util.Map<String, String> pushNotificationMessageDataFromFCM = remoteMessage.getData();
try {
    callAgent.handlePushNotification(pushNotificationMessageDataFromFCM).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while handling the Incoming Calls Push Notifications.");
}
```

Если обработка сообщения push-уведомления прошла успешно и все обработчики событий зарегистрированы должным образом, приложение будет звонить.

### <a name="unregister-push-notifications"></a>Отмена регистрации push-уведомлений

Приложения могут отменить регистрацию push-уведомлений в любое время. Вызовите `unregisterPushNotification()` метод для каллажент, чтобы отменить регистрацию.

```java
try {
    callAgent.unregisterPushNotifications().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```

## <a name="call-management"></a>Управление вызовами
Вы можете получить доступ к свойствам вызова и выполнить различные операции во время вызова для управления параметрами, связанными с видео и аудио.

### <a name="call-properties"></a>Свойства вызова

Получите уникальный идентификатор для этого вызова:

```java
String callId = call.getCallId();
```

Чтобы узнать о других участниках в коллекции проверки вызовов `remoteParticipant` на `call` экземпляре, выполните следующие действия.

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
```

Удостоверение вызывающего объекта, если вызов является входящим:

```java
CommunicationIdentifier callerId = call.getCallerId();
```

Получить состояние вызова: 

```java
CallState callState = call.getState();
```

Он возвращает строку, представляющую текущее состояние вызова:
* "None" — состояние начального вызова
* "Входящий" — указывает, что вызов является входящим, он должен быть либо принят, либо отклонен.
* "Подключение" — начальное состояние перехода, когда вызов помещается или принимается
* "Кольцо" — для исходящего вызова — указывает, что вызов вызывается для удаленных участников, это "входящая" сторона
* "Еарлимедиа" — указывает состояние, в котором воспроизводится объявление перед подключением вызова
* "Connected" — вызов подключен
* "Удержание" — вызов помещается в удержание, медиа-файлы не передаются между локальной конечной точкой и удаленными участниками
* "Идет отключение"-состояние перехода перед тем, как вызов переходит в состояние "disconnected"
* "Disconnected" — окончательное состояние вызова


Чтобы узнать, почему вызов завершился, проверьте `callEndReason` свойство. Он содержит код/код: 

```java
CallEndReason callEndReason = call.getCallEndReason();
int code = callEndReason.getCode();
int subCode = callEndReason.getSubCode();
```

Чтобы узнать, является ли текущий вызов входящим вызовом, проверьте `isIncoming` свойство:

```java
boolean isIncoming = call.getIsIncoming();
```

Чтобы проверить, отключен ли текущий микрофон, проверьте `muted` свойство:

```java
boolean muted = call.getIsMicrophoneMuted();
```

Чтобы проверить активные видеопотоки, проверьте `localVideoStreams` коллекцию:

```java
List<LocalVideoStream> localVideoStreams = call.getLocalVideoStreams();
```

### <a name="mute-and-unmute"></a>Отключить и включить звук

Чтобы отключить или включить выключение локальной конечной точки, можно использовать `mute` `unmute` асинхронные API и.

```java
call.mute().get();
call.unmute().get();
```

### <a name="start-and-stop-sending-local-video"></a>Запуск и отмена отправки локального видео

Чтобы запустить видео, необходимо перечислить камеры с помощью `getCameraList` API для `deviceManager` объекта. Затем создайте новый экземпляр `LocalVideoStream` , передающий нужную камеру, и передайте его в `startVideo` API в качестве аргумента:

```java
VideoDeviceInfo desiredCamera = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
videoOptions = new VideoOptions(currentVideoStream);
Future startVideoFuture = call.startVideo(currentVideoStream);
startVideoFuture.get();
```

После успешного начала отправки видео `LocalVideoStream` экземпляр будет добавлен в `localVideoStreams` коллекцию в экземпляре вызова.

```java
currentVideoStream == call.getLocalVideoStreams().get(0);
```

Чтобы отключить локальное видео, передайте `localVideoStream` экземпляр, доступный в `localVideoStreams` коллекции:

```java
call.stopVideo(localVideoStream).get();
```

При отправке видео можно переключиться на другое устройство камеры, вызвав `switchSource` на `localVideoStream` экземпляре:
```java
localVideoStream.switchSource(source).get();
```

## <a name="remote-participants-management"></a>Управление удаленными участниками

Все удаленные участники представлены по `RemoteParticipant` типу и доступны через `remoteParticipants` коллекцию в экземпляре вызова.

### <a name="list-participants-in-a-call"></a>Вывод списка участников в вызове
`remoteParticipants`Коллекция возвращает список удаленных участников в данном вызове:
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
```

### <a name="remote-participant-properties"></a>Свойства удаленного участника
С каждым указанным удаленным участником связан набор свойств и коллекций, связанных с ним:

* Получите идентификатор для этого удаленного участника.
Identity является одним из типов "идентификатор"
```java
CommunicationIdentifier participantIdentity = remoteParticipant.getIdentifier();
```

* Получение состояния удаленного участника.
```java
ParticipantState state = remoteParticipant.getState();
```
Состояние может быть одним из
* "Idle" — начальное состояние
* "Connected" — состояние перехода во время подключения участника к вызову
* "Connected" — участник подключен к вызову
* "Удержание"-участник находится в удержании
* "Еарлимедиа" — объявление воспроизводится до того, как участник подключен к вызову
* "Disconnected" — конечное состояние — участник отключен от вызова


* Чтобы узнать, почему участник оставил вызов, проверьте `callEndReason` свойство:
```java
CallEndReason callEndReason = remoteParticipant.getCallEndReason();
```

* Чтобы проверить, не отключен ли удаленный участник, проверьте `isMuted` свойство:
```java
boolean isParticipantMuted = remoteParticipant.getIsMuted();
```

* Чтобы проверить, говорит ли этот удаленный участник, проверьте `isSpeaking` свойство:
```java
boolean isParticipantSpeaking = remoteParticipant.getIsSpeaking();
```

* Чтобы проверить все потоки видео, отправляемые данным участником в этом вызове, проверьте `videoStreams` коллекцию:
```java
List<RemoteVideoStream> videoStreams = remoteParticipant.getVideoStreams(); // [RemoteVideoStream, RemoteVideoStream, ...]
```


### <a name="add-a-participant-to-a-call"></a>Добавление участника в вызов

Чтобы добавить участника в вызов (либо пользователя, либо номер телефона), можно вызвать `addParticipant` . Это приведет к синхронному возврату экземпляра удаленного участника.

```java
const acsUser = new CommunicationUser("<acs user id>");
const acsPhone = new PhoneNumber("<phone number>");
RemoteParticipant remoteParticipant1 = call.addParticipant(acsUser);
RemoteParticipant remoteParticipant2 = call.addParticipant(acsPhone);
```

### <a name="remove-participant-from-a-call"></a>Удалить участника из вызова
Чтобы удалить участника из вызова (либо пользователя, либо номера телефона), можно вызвать `removeParticipant` .
Это будет разрешаться асинхронно после удаления участника из вызова.
Участник также будет удален из `remoteParticipants` коллекции.
```java
RemoteParticipant remoteParticipant = call.getParticipants().get(0);
call.removeParticipant(acsUser).get();
call.removeParticipant(acsPhone).get();
```

## <a name="render-remote-participant-video-streams"></a>Прорисовка потоков видео удаленных участников
Чтобы получить список потоков видео и потоков, использующих удаленные участники, просмотрите `videoStreams` коллекции:
```java
RemoteParticipant remoteParticipant = call.getRemoteParticipants().get(0);
RemoteVideoStream remoteParticipantStream = remoteParticipant.getVideoStreams().get(0);
MediaStreamType streamType = remoteParticipantStream.getType(); // of type MediaStreamType.Video or MediaStreamType.ScreenSharing
```
 
Чтобы подготовиться к просмотру `RemoteVideoStream` из удаленного участника, необходимо подписываться на `OnVideoStreamsUpdated` событие.

В рамках события изменение `isAvailable` Свойства на true указывает на то, что удаленный участник в настоящий момент отправляет поток. После этого создайте новый экземпляр `Renderer` , а затем создайте новый `RendererView` с помощью асинхронного `createView` API и ПОДКЛЮЧИТЕСЬ `view.target` в пользовательском интерфейсе приложения.

При каждом изменении доступности удаленного потока можно удалить весь модуль подготовки отчетов, конкретный `RendererView` или оставить его, но это приведет к отображению пустого кадра видео.

```java
Renderer remoteVideoRenderer = new Renderer(remoteParticipantStream, appContext);
View uiView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
layout.addView(uiView);

remoteParticipant.addOnVideoStreamsUpdatedListener(e -> onRemoteParticipantVideoStreamsUpdated(p, e));

void onRemoteParticipantVideoStreamsUpdated(RemoteParticipant participant, RemoteVideoStreamsEvent args) {
    for(RemoteVideoStream stream : args.getAddedRemoteVideoStreams()) {
        if(stream.getIsAvailable()) {
            startRenderingVideo();
        } else {
            renderer.dispose();
        }
    }
}
```

### <a name="remote-video-stream-properties"></a>Свойства удаленного потока видео
В удаленном видеопотоке есть несколько свойств

* `Id` — Идентификатор удаленного потока видео.
```java
int id = remoteVideoStream.getId();
```

* `MediaStreamType` -Может быть "Video" или "Скриншаринг"
```java
MediaStreamType type = remoteVideoStream.getType();
```

* `isAvailable` — Указывает, является ли конечная точка удаленного участника активной отправкой потока
```java
boolean availability = remoteVideoStream.getIsAvailable();
```

### <a name="renderer-methods-and-properties"></a>Методы и свойства модуля подготовки отчетов
Объект модуля подготовки отчетов следующие API

* Создайте `RendererView` экземпляр, который позже можно будет присоединить в пользовательском интерфейсе приложения для отображения удаленного потока видео.
```java
// Create a view for a video stream
renderer.createView()
```
* Удалить модуль подготовки отчетов и все `RendererView` связанные с этим модулем подготовки отчетов
```java
renderer.dispose()
```

* `StreamSize` — Размер (ширина и высота) удаленного видеопотока;
```java
StreamSize renderStreamSize = remoteVideoStream.getSize();
int width = renderStreamSize.getWidth();
int height = renderStreamSize.getHeight();
```


### <a name="rendererview-methods-and-properties"></a>Методы и свойства Рендерервиев
При создании `RendererView` можно указать `scalingMode` `mirrored` Свойства и, которые будут применяться к этому представлению. режим масштабирования может быть либо "Stretch" | "Обрезать" | "Fit" Если `mirrored` задано значение `true` , отображаемый поток будет зеркально передаваться по вертикали.

```java
Renderer remoteVideoRenderer = new Renderer(remoteVideoStream, appContext);
RendererView rendererView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
```

Созданный Рендерервиев можно затем подключить к пользовательскому интерфейсу приложения, используя следующий фрагмент кода:
```java
layout.addView(rendererView);
```

Позже можно обновить режим масштабирования, вызвав `updateScalingMode` API для объекта рендерервиев с одним из скалингмоде. Stretch | Скалингмоде. обрезать | Скалингмоде. Fit в качестве аргумента.
```java
// Update the scale mode for this view.
rendererView.updateScalingMode(ScalingMode.Crop)
```


## <a name="device-management"></a>Управление устройствами

`DeviceManager` позволяет перечислить локальные устройства, которые можно использовать в вызове для передачи аудио-или видеопотоков. Он также позволяет запрашивать у пользователя разрешение на доступ к микрофону и камере с помощью собственного API браузера.

Доступ можно получить `deviceManager` , вызвав `callClient.getDeviceManager()` метод.
> [!WARNING]
> Сейчас `callAgent` для получения доступа к девицеманажер необходимо сначала создать экземпляр объекта.

```java
DeviceManager deviceManager = callClient.getDeviceManager().get();
```

### <a name="enumerate-local-devices"></a>Перечисление локальных устройств

Для доступа к локальным устройствам можно использовать методы перечисления в Device Manager. Перечисление является синхронным действием.

```java
//  Get a list of available video devices for use.
List<VideoDeviceInfo> localCameras = deviceManager.getCameraList(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
List<AudioDeviceInfo> localMicrophones = deviceManager.getMicrophoneList(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
List<AudioDeviceInfo> localSpeakers = deviceManager.getSpeakerList(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-default-microphonespeaker"></a>Установка микрофона или динамика по умолчанию

Диспетчер устройств позволяет задать устройство по умолчанию, которое будет использоваться при запуске вызова.
Если значения по умолчанию для клиента не заданы, службы связи будут возвращаться к значениям по умолчанию для ОС.

```java

// Get the microphone device that is being used.
AudioDeviceInfo defaultMicrophone = deviceManager.getMicrophoneList().get(0);

// Set the microphone device to use.
deviceManager.setMicrophone(defaultMicrophone);

// Get the speaker device that is being used.
AudioDeviceInfo defaultSpeaker = deviceManager.getSpeakerList().get(0);

// Set the speaker device to use.
deviceManager.setSpeaker(defaultSpeaker);
```

### <a name="local-camera-preview"></a>Предварительная версия локальной камеры

Вы можете использовать `DeviceManager` и, `Renderer` чтобы начать отрисовку потоков из локальной камеры. Этот поток не будет отправлен другим участникам; Это локальный канал предварительной версии. Это асинхронное действие.

```java
VideoDeviceInfo videoDevice = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(videoDevice, appContext);
videoOptions = new VideoOptions(currentVideoStream);

Renderer previewRenderer = new Renderer(currentVideoStream, appContext);
View uiView previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));

// Attach the renderingSurface to a viewable location on the app at this point
layout.addView(uiView);
```

## <a name="eventing-model"></a>Модель событий
Вы можете подписываться на большинство свойств и коллекций, чтобы получать уведомления при изменении значений.

### <a name="properties"></a>Свойства
Чтобы подписываться на `property changed` события, выполните следующие действия.

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnCallStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnCallStateChangedListener(callStateChangeListener);
```

### <a name="collections"></a>Коллекции
Чтобы подписываться на `collection updated` события, выполните следующие действия.

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```
