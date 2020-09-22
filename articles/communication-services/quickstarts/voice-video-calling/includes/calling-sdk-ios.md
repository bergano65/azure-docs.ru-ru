---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: fa7fd73a7d8019919a89dd9e9522b7389dc9c18f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940066"
---
## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно. 
- Развернутый ресурс служб связи. [Создайте ресурс служб связи](../../create-communication-resource.md).
- Объект `User Access Token` для включения клиента вызова. Дополнительные сведения о [том, `User Access Token` как получить](../../access-tokens.md)
- Необязательно. Выполните инструкции из краткого руководства по [началу работы с добавлением вызова в приложение](../getting-started-with-calling.md) .

## <a name="setting-up"></a>Настройка

### <a name="creating-the-xcode-project"></a>Создание проекта Xcode

В Xcode создайте новый проект iOS и выберите шаблон приложения с **одним представлением** . В этом кратком руководстве используется [платформа свифтуи](https://developer.apple.com/xcode/swiftui/), поэтому необходимо задать для **языка** значение **SWIFT** , а для **пользовательского интерфейса** — **свифтуи**. Вы не собираетесь создавать модульные тесты или тесты пользовательского интерфейса во время этого краткого руководства. Вы можете снять флажок **включить модульные тесты** , а также снять флажок **включить тесты пользовательского интерфейса**.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Снимок экрана, показывающий окно создания нового проекта в Xcode.":::

### <a name="install-the-package"></a>Установка пакета

Добавьте в проект вызывающую клиентскую библиотеку служб связи Azure и ее зависимости (Азурекоре. Framework и Азурекоммуникатион. Framework).

> [!NOTE]
> С выпуском пакета SDK для Азурекоммуникатионкаллинг вы найдете сценарий bash `BuildAzurePackages.sh` . Сценарий при запуске `sh ./BuildAzurePackages.sh` предоставит путь к созданным пакетам платформы, которые необходимо импортировать в пример приложения на следующем шаге. Обратите внимание, что необходимо настроить средства командной строки Xcode, если это еще не сделано перед запуском сценария: Start Xcode, выберите "предпочтения-> Locations". Выберите версию Xcode для программ командной строки.

1. Скачайте клиентскую библиотеку вызовов служб связи Azure для iOS.
2. В Xcode щелкните файл проекта и выберите целевой объект Build (проект), чтобы открыть редактор параметров проекта.
3. На вкладке **Общие** перейдите к разделу **платформы, библиотеки и внедренное содержимое** и щелкните значок **"+"** .
4. В левом нижнем углу диалогового окна выберите **Добавить файлы**, перейдите к каталогу **азурекоммуникатионкаллинг. Framework** пакета несжатой клиентской библиотеки.
    1. Повторите Последнее действие для добавления **азурекоре. Framework** и **азурекоммуникатион. Framework**.
5. Откройте вкладку **параметры сборки** в редакторе параметров проекта и прокрутите страницу до раздела **пути поиска** . Добавьте новую запись **пути поиска платформы** для каталога, содержащего **азурекоммуникатионкаллинг. Framework**.
    1. Добавьте еще одну запись пути поиска платформы, указывающую на папку, содержащую зависимости.

:::image type="content" source="../media/ios/xcode-framework-search-paths.png" alt-text="Снимок экрана, показывающий обновление путей поиска платформы в XCode.":::

### <a name="request-access-to-the-microphone"></a>Запрос доступа к микрофону

Чтобы получить доступ к микрофону устройства, необходимо обновить список информационных свойств приложения с помощью `NSMicrophoneUsageDescription` . Вы устанавливаете связанное значение `string` , которое будет включаться в диалоговое окно, используемое системой для запроса доступа пользователя.

Щелкните правой кнопкой мыши `Info.plist` запись дерева проекта и выберите пункт **Открыть как**  >  **Исходный код**. Добавьте следующие строки в раздел верхнего уровня `<dict>` , а затем сохраните файл.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Настройка платформы приложения

Откройте файл **ContentView. SWIFT** проекта и добавьте `import` объявление в начало файла, чтобы импортировать `AzureCommunicationCalling library` . Кроме того, импорт `AVFoundation` должен потребоваться для запроса разрешения аудио в коде.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="object-model"></a>Объектная модель

Следующие классы и интерфейсы обрабатывали некоторые основные функции вызывающей клиентской библиотеки служб связи Azure для iOS.


| Имя                                  | Описание                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| акскаллклиент | Акскаллклиент — это Главная точка входа в вызывающую клиентскую библиотеку.|
| акскаллажент | Акскаллажент используется для запуска вызовов и управления ими. |
| коммуникатионусеркредентиал | Коммуникатионусеркредентиал используется в качестве учетных данных маркера для создания экземпляра Каллажент.| 
| коммуникатиониндентифиер | Коммуникатиониндентифиер используется для представления удостоверения пользователя, который может быть одним из следующих: Коммуникатионусер/PhoneNumber/Каллингаппликатион. |

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

callClient = ACSCallClient()
callClient?.createCallAgent(userCredential!,
    withCompletionHandler: { (callAgent, error) in
        if error != nil {
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
let oneToOneCall = self.CallingApp.callAgent.call(participants: callees, options: ACSStartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Поместите вызов 1: n с помощью пользователей и PSTN
Чтобы позвонить в PSTN, необходимо указать номер телефона, полученный с помощью служб связи.
```swift

let pstnCallee = PhoneNumber('+1999999999')
let callee = CommunicationUser(identifier: 'acsUserId')
let groupCall = self.CallingApp.callAgent.call(participants: [pstnCallee, callee], options: ACSStartCallOptions())

```

### <a name="place-a-11-call-with-with-video"></a>Поместите вызов 1:1 с помощью видео
Чтобы получить экземпляр диспетчера устройств, см. [здесь](#device-management)

```swift

let camera = self.deviceManager!.getCameraList()![0]
let localVideoStream = ACSLocalVideoStream(camera)
let videoOptions = ACSVideoOptions(localVideoStream)

let startCallOptions = ACSStartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUser(identifier: 'acsUserId')
let call = self.callAgent?.call([callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>Вызов группы
Чтобы присоединиться к вызову, необходимо вызвать один из API-интерфейсов на *каллажент*

```swift

let groupCallContext = ACSGroupCallContext()
groupCallContext?.groupId = UUID(uuidString: "uuid_string")!
let call = self.callAgent?.join(with: groupCallContext, joinCallOptions: ACSJoinCallOptions())

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
callAgent.registerPushNotifications(deviceToken,
                withCompletionHandler: { (error) in
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
callAgent.handlePushNotification(dictionaryPayload, withCompletionHandler: { (error) in
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
call.mute(completionHandler: { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
})

```

Асинхронной Локальный включение звука

```swift
call.unmute(completionHandler:{ (error) in
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

let firstCamera: ACSVideoDeviceInfo? = self.deviceManager?.getCameraList()![0]
let localVideoStream = ACSLocalVideoStream(firstCamera)

call.startVideo(localVideoStream) { (error) in
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

call.stopVideo(localVideoStream,{ (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    }
    else {
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

let remoteParticipantAdded: ACSRemoteParticipant = call.addParticipant(CommunicationUser(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>Удаление участника из вызова
Чтобы удалить участника из вызова (либо пользователя, либо номера телефона), можно вызвать  `removeParticipant` API. Это будет разрешено асинхронно.

```swift

call!.remove(remoteParticipantAdded) { (error) in
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

var type: ACSMediaStreamType = remoteParticipantVideoStream.type // 'ACSMediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-stream"></a>Прорисовка потока удаленных участников

Чтобы начать отрисовку потоков удаленных участников, выполните следующие действия.

```swift

let renderer: ACSRenderer? = ACSRenderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: ACSRendererView? = renderer?.createView(ACSRenderingOptions(ACSScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(ACSScalingMode.fit)

```

### <a name="remote-video-renderer-methods-and-properties"></a>Удаленные методы и свойства модуля подготовки видео

```swift
// [Bool] isRendering - indicating if stream is being rendered
remoteVideoRenderer.isRendering()
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
deviceManager.setMicrophone(ACSAudioDeviceInfo())
// get first speaker
var firstSpeaker = self.deviceManager!.getSpeakerList()![0]
// [Synchronous] set speaker
deviceManager.setSpeakers(ACSAudioDeviceInfo())
```

### <a name="local-camera-preview"></a>Предварительная версия локальной камеры

С помощью можно `ACSRenderer` начать отрисовку потока с локальной камеры. Этот поток не будет отправлен другим участникам; Это локальный канал предварительной версии. Это асинхронное действие.

```swift

let camera: ACSVideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: ACSLocalVideoStream = ACSLocalVideoStream(camera)
let renderer: ACSRenderer = ACSRenderer(localVideoStream: localVideoStream)
self.view = renderer!.createView(ACSRenderingOptions())

```

### <a name="local-camera-preview-properties"></a>Свойства предварительного просмотра локальной камеры

Модуль подготовки отчетов имеет набор свойств и методов, позволяющих управлять отрисовкой:

```swift

// Constructor can take in ACSLocalVideoStream or ACSRemoteVideoStream
let localRenderer = ACSRenderer(localVideoStream:localVideoStream)
let remoteRenderer = ACSRenderer(remoteVideoStream:remoteVideoStream)

// [ACSStreamSize] size of the rendering view
localRenderer.size

// [ACSRendererDelegate] an object you provide to receive events from this ACSRenderer instance
localRenderer.delegate

// [Synchronous] create view with rendering options
localRenderer.createView(options:ACSRenderingOptions())
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
public func onCallStateChanged(_ call: ACSCall!,
                               _ args: ACSPropertyChangedEventArgs!)
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
public func onLocalVideoStreamsChanged(_ call: ACSCall!,
                                       _ args: ACSLocalVideoStreamsUpdatedEventArgs!)
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
