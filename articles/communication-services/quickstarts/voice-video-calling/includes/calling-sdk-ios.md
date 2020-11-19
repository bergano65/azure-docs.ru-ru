---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: a9af249aac18c847bf353f22b23ee67ab6e264c4
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2020
ms.locfileid: "94915275"
---
## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно. 
- Развернутый ресурс Служб коммуникации. [Создайте ресурс Служб коммуникации.](../../create-communication-resource.md)
- `User Access Token` для включения клиента вызова. Дополнительные сведения о том, [как получить `User Access Token`](../../access-tokens.md)
- Необязательно. Выполните инструкции из краткого руководства по [началу работы с добавлением вызова в приложение](../getting-started-with-calling.md) .

## <a name="setting-up"></a>Настройка

### <a name="creating-the-xcode-project"></a>Создание проекта Xcode

В Xcode создайте новый проект iOS и выберите шаблон **Single View App** (Приложение с одним представлением). В этом кратком руководстве используется [платформа свифтуи](https://developer.apple.com/xcode/swiftui/), поэтому необходимо задать для **языка** значение **SWIFT** , а для **пользовательского интерфейса** — **свифтуи**. Вы не собираетесь создавать модульные тесты или тесты пользовательского интерфейса во время этого краткого руководства. Вы можете снять флажок **включить модульные тесты** , а также снять флажок **включить тесты пользовательского интерфейса**.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Снимок экрана с демонстрацией создания окна New Project (Новый проект) в Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Установка пакета и зависимостей с помощью CocoaPods

1. Создайте Podfile для своего приложения следующим образом:

   ```
   platform :ios, '13.0'
   use_frameworks!
   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.5'
     pod 'AzureCommunication', '~> 1.0.0-beta.5'
     pod 'AzureCore', '~> 1.0.0-beta.5'
   end
   ```

2. Выполните `pod install`.
3. Откройте `.xcworkspace` с помощью Xcode.

### <a name="request-access-to-the-microphone"></a>Запрос доступа к микрофону

Чтобы получить доступ к микрофону устройства, вам необходимо указать ключ `NSMicrophoneUsageDescription` в списке свойств сведений приложения. Задайте связанное значение для строки `string`, которая будет включена в диалоговое окно, отображаемое системой при запросе доступа у пользователя.

В дереве проекта щелкните правой кнопкой мыши запись `Info.plist` и выберите **Open As** > **Source Code** (Открыть как > Исходный код). Добавьте в раздел верхнего уровня `<dict>` следующие строки, а затем сохраните файл.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Настройка платформы приложения

Откройте файл **ContentView.swift** проекта и добавьте объявление `import` в начало файла, чтобы импортировать `AzureCommunicationCalling library`. Кроме того, импорт `AVFoundation` должен потребоваться для запроса разрешения аудио в коде.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="object-model"></a>Объектная модель

Следующие классы и интерфейсы обрабатывали некоторые основные функции вызывающей клиентской библиотеки служб связи Azure для iOS.


| Имя                                  | Описание                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ACSCallClient | Акскаллклиент — это Главная точка входа в вызывающую клиентскую библиотеку.|
| ACSCallAgent | Акскаллажент используется для запуска вызовов и управления ими. |
| CommunicationUserCredential | CommunicationUserCredential используется в качестве учетных данных маркера для создания экземпляра CallAgent.| 
| CommunicationIndentifier | CommunicationIndentifier используется для представления идентификатора пользователя, который может иметь один из следующих типов: CommunicationUser, PhoneNumber, CallingApplication. |

> [!NOTE]
> При реализации делегатов событий приложение должно содержать строгую ссылку на объекты, для которых требуются подписки на события. Например, если `ACSRemoteParticipant` объект возвращается при вызове `call.addParticipant` метода, а приложение устанавливает делегат для прослушивания `ACSRemoteParticipantDelegate` , приложение должно содержать строгую ссылку на `ACSRemoteParticipant` объект. В противном случае, если этот объект будет собран, делегат выдаст неустранимое исключение, когда вызывающий пакет SDK попытается вызвать объект.

## <a name="initialize-the-acscallagent"></a>Инициализация Акскаллажент

Чтобы создать `ACSCallAgent` экземпляр из `ACSCallClient` , необходимо использовать `callClient.createCallAgent` метод, который асинхронно возвращает `ACSCallAgent` объект после инициализации.

Для создания клиента вызова необходимо передать `CommunicationUserCredential` объект.

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationUserCredential?
do {
    userCredential = try CommunicationUserCredential(
        initialToken: tokenString, refreshProactively: true,
        tokenRefresher: self.fetchTokenSync
    )
} catch {
    print("Failed to create CommunicationCredential object")
}

// tokenProvider needs to be implemented by contoso which fetches new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

Передать объект Коммуникатионусеркредентиал, созданный выше, в Акскаллклиент

```swift

callClient = CallClient()
callClient?.createCallAgent(with: userCredential!,
    completionHandler: { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>Размещение исходящего вызова

Чтобы создать и запустить вызов, необходимо вызвать один из API в `ACSCallAgent` и предоставить удостоверение служб связи для пользователя, подготовленного с помощью клиентской библиотеки управления службами связи.

Создание и запуск вызываются синхронно. Вы получите экземпляр Call, который позволяет подписываться на все события в вызове.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Поместите 1:1 вызов пользователя или 1: n в вызове пользователей и PSTN

```swift

let callees = [CommunicationUser(identifier: 'acsUserId')]
let oneToOneCall = self.callAgent.call(participants: callees, options: StartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Поместите вызов 1: n с помощью пользователей и PSTN
Чтобы позвонить в PSTN, необходимо указать номер телефона, полученный с помощью служб связи.
```swift

let pstnCallee = PhoneNumber('+1999999999')
let callee = CommunicationUser(identifier: 'acsUserId')
let groupCall = self.callAgent.call(participants: [pstnCallee, callee], options: StartCallOptions())

```

### <a name="place-a-11-call-with-with-video"></a>Поместите вызов 1:1 с помощью видео
Чтобы получить экземпляр диспетчера устройств, см. [здесь](#device-management)

```swift

let camera = self.deviceManager!.getCameraList()![0]
let localVideoStream = LocalVideoStream(camera: camera)
let videoOptions = VideoOptions(localVideoStream: localVideoStream)

let startCallOptions = StartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUser(identifier: 'acsUserId')
let call = self.callAgent?.call(participants: [callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>Присоединение к групповому вызову
Чтобы присоединиться к вызову, необходимо вызвать один из API-интерфейсов на *каллажент*

```swift

let groupCallContext = GroupCallContext()
groupCallContext?.groupId = UUID(uuidString: "uuid_string")!
let call = self.callAgent?.join(with: groupCallContext, joinCallOptions: JoinCallOptions())

```

## <a name="push-notification"></a>push-уведомление

Мобильное push-уведомление — это всплывающее уведомление, которое вы получаете на мобильном устройстве. Для вызова мы будем сосредоточиться на push-уведомлениях VoIP (голосовой через Интернет). Мы предлагаем вам возможности для регистрации push-уведомлений, управления Push-уведомлениями и отмены регистрации push-уведомлений.

### <a name="prerequisite"></a>Предварительное требование

- Шаг 1. Xcode-> подписывание & возможностей — > добавить возможность — > "Push-уведомления".
- Шаг 2. Xcode-> подписывание & возможностей — > добавить возможность — > "фоновые режимы"
- Шаг 3. "фоновые режимы"-> выберите "голоса по IP" и "Удаленные уведомления"

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="Снимок экрана, показывающий, как добавить возможности в Xcode." lightbox="../media/ios/xcode-push-notification.png":::

#### <a name="register-for-push-notifications"></a>Регистрация для получения push-уведомлений

Чтобы зарегистрироваться для получения push-уведомления, вызовите Регистерпушнотификатион () в экземпляре *каллажент* с маркером регистрации устройства.

Регистрация для push-уведомлений должна быть вызвана после успешной инициализации. При `callAgent` уничтожении объекта `logout` будет вызван метод, который автоматически отменит регистрацию push-уведомлений.


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken,
                completionHandler: { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
})

```

#### <a name="push-notification-handling"></a>Обработка push-уведомлений
Чтобы получать входящие вызовы push-уведомлений, вызовите *хандлепушнотификатион ()* в экземпляре *каллажент* с полезными данными словаря.

```swift

let dictionaryPayload = pushPayload?.dictionaryPayload
callAgent.handlePushNotification(payload: dictionaryPayload, completionHandler: { (error) in
    if (error != nil) {
        print("Handling of push notification failed")
    } else {
        print("Handling of push notification was successful")
    }
})

```
#### <a name="unregister-push-notification"></a>Отмена регистрации push-уведомления

Приложения могут отменить регистрацию push-уведомлений в любое время. Просто вызовите `unRegisterPushNotification` метод для *каллажент*.
> [!NOTE]
> Регистрация приложений не выполняется автоматически из push-уведомления при выходе из системы.

```swift

callAgent.unRegisterPushNotifications(completionHandler: { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
})

```

## <a name="mid-call-operations"></a>Операции среднего вызова

Вы можете выполнять различные операции во время вызова, чтобы управлять параметрами, связанными с видео и аудио.

### <a name="mute-and-unmute"></a>Отключить и включить звук

Чтобы отключить или включить выключение локальной конечной точки, можно использовать `mute` `unmute` асинхронные API и.

```swift
call!.mute(completionHandler: { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
})

```

Асинхронной Локальный включение звука

```swift
call!.unmute(completionHandler:{ (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
})
```

### <a name="start-and-stop-sending-local-video"></a>Запуск и отмена отправки локального видео

Чтобы начать отправку локального видео другим участникам в вызове, используйте `startVideo` API и передайте `localVideoStream``camera`

```swift

let firstCamera: VideoDeviceInfo? = self.deviceManager?.getCameraList()![0]
let localVideoStream = LocalVideoStream(camera: firstCamera)

call!.startVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    }
    else {
        print("Local video failed to start")
    }
}

```

После начала отправки видео `ACSLocalVideoStream` экземпляр добавляет `localVideoStreams` коллекцию в экземпляре вызова:

```swift

call.localVideoStreams[0]

```

Асинхронной Чтобы отключить локальное видео, передайте `localVideoStream` возвращенный результат вызова `call.startVideo` :

```swift

call!.stopVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    } else {
        print("Local video failed to stop")
    }
}

```

## <a name="remote-participants-management"></a>Управление удаленными участниками

Все удаленные участники представлены `ACSRemoteParticipant` типом и доступны через `remoteParticipants` коллекцию в экземпляре вызова:

### <a name="list-participants-in-a-call"></a>Вывод списка участников в вызове

```swift

call.remoteParticipants

```

### <a name="remote-participant-properties"></a>Свойства удаленного участника

```swift

// [ACSRemoteParticipantDelegate] delegate - an object you provide to receive events from this ACSRemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision token for another user
var identity = remoteParticipant.identity

// ACSParticipantStateIdle = 0, ACSParticipantStateEarlyMedia = 1, ACSParticipantStateConnecting = 2, ACSParticipantStateConnected = 3, ACSParticipantStateOnHold = 4, ACSParticipantStateInLobby = 5, ACSParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [ACSError] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// ACSRemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [ACSRemoteVideoStream, ACSRemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>Добавление участника в вызов

Чтобы добавить участника в вызов (либо пользователя, либо номер телефона), можно вызвать `addParticipant` . Это приведет к синхронному возврату экземпляра удаленного участника.

```swift

let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUser(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>Удаление участника из вызова
Чтобы удалить участника из вызова (либо пользователя, либо номера телефона), можно вызвать  `removeParticipant` API. Это будет разрешено асинхронно.

```swift

call!.remove(participant: remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}

```

## <a name="render-remote-participant-video-streams"></a>Прорисовка потоков видео удаленных участников

Удаленные участники могут инициировать общий доступ к видео или экрану во время вызова.

### <a name="handle-remote-participant-videoscreen-sharing-streams"></a>Обработку потоков общего доступа к видео/экрану удаленного участника

Чтобы получить список потоков удаленных участников, изучите `videoStreams` коллекции:

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="remote-video-stream-properties"></a>Свойства удаленного потока видео

```swift

var type: MediaStreamType = remoteParticipantVideoStream.type // 'ACSMediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-stream"></a>Прорисовка потока удаленных участников

Чтобы начать отрисовку потоков удаленных участников, выполните следующие действия.

```swift

let renderer: Renderer? = Renderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: RendererView? = renderer?.createView(with: RenderingOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)

```

### <a name="remote-video-renderer-methods-and-properties"></a>Удаленные методы и свойства модуля подготовки видео

```swift
// [Bool] isRendering - indicating if stream is being rendered
remoteVideoRenderer.isRendering()
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```

## <a name="device-management"></a>Управление устройствами

`DeviceManager` позволяет перечислить локальные устройства, которые можно использовать в вызове для передачи аудио-и видеопотоков. Она также позволяет запросить у пользователя разрешение на доступ к микрофону или камере. Вы можете получить доступ к `deviceManager` `callClient` объекту:

```swift

self.callClient!.getDeviceManager(
    completionHandler: { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    })
```

### <a name="enumerate-local-devices"></a>Перечисление локальных устройств

Для доступа к локальным устройствам можно использовать методы перечисления в Device Manager. Перечисление является синхронным действием.

```swift
// enumerate local cameras
var localCameras = deviceManager.getCameraList() // [ACSVideoDeviceInfo, ACSVideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.getMicrophoneList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.getSpeakerList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
``` 

### <a name="set-default-microphonespeaker"></a>Установка микрофона или динамика по умолчанию

Диспетчер устройств позволяет задать устройство по умолчанию, которое будет использоваться при запуске вызова. Если значения по умолчанию стека не заданы, службы связи будут возвращаться к значениям по умолчанию для ОС.

```swift
// get first microphone
var firstMicrophone = self.deviceManager!.getMicrophoneList()![0]
// [Synchronous] set microphone
deviceManager.setMicrophone(microphoneDevice: firstMicrophone)
// get first speaker
var firstSpeaker = self.deviceManager!.getSpeakerList()![0]
// [Synchronous] set speaker
deviceManager.setSpeaker(speakerDevice: firstSpeaker)
```

### <a name="local-camera-preview"></a>Предварительная версия локальной камеры

С помощью можно `ACSRenderer` начать отрисовку потока с локальной камеры. Этот поток не будет отправлен другим участникам; Это локальный канал предварительной версии. Это асинхронное действие.

```swift

let camera: VideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: LocalVideoStream = LocalVideoStream(camera: camera)
let renderer: Renderer = Renderer(localVideoStream: localVideoStream)
self.view = renderer!.createView()

```

### <a name="local-camera-preview-properties"></a>Свойства предварительного просмотра локальной камеры

Модуль подготовки отчетов имеет набор свойств и методов, позволяющих управлять отрисовкой:

```swift

// Constructor can take in ACSLocalVideoStream or ACSRemoteVideoStream
let localRenderer = Renderer(localVideoStream:localVideoStream)
let remoteRenderer = Renderer(remoteVideoStream:remoteVideoStream)

// [ACSStreamSize] size of the rendering view
localRenderer.size

// [ACSRendererDelegate] an object you provide to receive events from this ACSRenderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer.createView(with: RenderingOptions(scalingMode: ScalingMode.fit))

// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="eventing-model"></a>Модель событий

Вы можете подписываться на большинство свойств и коллекций, чтобы получать уведомления при изменении значений.

### <a name="properties"></a>Свойства
Чтобы подписываться на `property changed` события, выполните следующие действия.

```swift
call.delegate = self
// Get the property of the call state by doing get on the call's state member
public func onCallStateChanged(_ call: Call!,
                               args: PropertyChangedEventArgs!)
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>Коллекции
Чтобы подписываться на `collection updated` события, выполните следующие действия.

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func onLocalVideoStreamsChanged(_ call: Call!,
                                       args: LocalVideoStreamsUpdatedEventArgs!)
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
