---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 7ca15baffd3fac4a1f3635ac7377bac620673446
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91451624"
---
## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно. 
- Развернутый ресурс Служб коммуникации. [Создайте ресурс Служб коммуникации.](../../create-communication-resource.md)
- Объект `User Access Token` для включения клиента вызова. Дополнительные сведения о [том, `User Access Token` как получить](../../access-tokens.md)
- Необязательно. Выполните инструкции из краткого руководства по [началу работы с добавлением вызова в приложение](../getting-started-with-calling.md) .

## <a name="setting-up"></a>Настройка

### <a name="creating-the-xcode-project"></a>Создание проекта Xcode

В Xcode создайте новый проект iOS и выберите шаблон **Single View App** (Приложение с одним представлением). В этом кратком руководстве используется [платформа свифтуи](https://developer.apple.com/xcode/swiftui/), поэтому необходимо задать для **языка** значение **SWIFT** , а для **пользовательского интерфейса** — **свифтуи**. Вы не собираетесь создавать модульные тесты или тесты пользовательского интерфейса во время этого краткого руководства. Вы можете снять флажок **включить модульные тесты** , а также снять флажок **включить тесты пользовательского интерфейса**.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Снимок экрана с демонстрацией создания окна New Project (Новый проект) в Xcode.":::

### <a name="install-the-package"></a>Установка пакета

Добавьте к своему проекту клиентскую библиотеку Служб коммуникации Azure для вызовов и ее зависимости (AzureCore.framework и AzureCommunication.framework).

> [!NOTE]
> В выпуске пакета SDK AzureCommunicationCalling вы найдете скрипт bash `BuildAzurePackages.sh`. При запуске (`sh ./BuildAzurePackages.sh`) скрипт предоставит вам путь к созданным пакетам платформы, которые необходимо импортировать в пример приложения на следующем шаге. Обратите внимание, что вам потребуется настроить средства командной строки Xcode, если вы не сделали этого до запуска скрипта: Запустите Xcode и выберите Preferences -> Locations (Настройки -> Расположения). Выберите свою версию Xcode для средств командной строки. **Обратите внимание, что сценарий BuildAzurePackages.sh работает только с Xcode 11,5 и выше.**

1. Скачайте клиентскую библиотеку Служб коммуникации Azure для реализации вызовов на iOS.
2. В Xcode щелкните файл проекта и выберите целевой объект сборки, чтобы открыть редактор параметров проекта.
3. На вкладке **General** (Общие) прокрутите к разделу **Frameworks, Libraries, and Embedded Content** (Платформы, библиотеки и встроенное содержимое) и щелкните значок **+** .
4. В левом нижнем углу диалогового окна выберите **Add Files** (Добавить файлы) и перейдите к каталогу **AzureCommunicationCalling.framework** разархивированного пакета клиентской библиотеки.
    1. Повторите последний шаг, чтобы добавить **AzureCore.framework** и **AzureCommunication.framework**.
5. Откройте вкладку **Build Settings** (Параметры сборки) в редакторе параметров проекта и прокрутите к разделу **Search Paths** (Пути поиска). Добавьте новую запись **Framework Search Paths** (Пути поиска платформы) для каталога, содержащего **AzureCommunicationCalling.framework**.
    1. Добавьте еще одну запись путей поиска платформы, указывающую на папку с зависимостями.

:::image type="content" source="../media/ios/xcode-framework-search-paths.png" alt-text="Снимок экрана с демонстрацией создания окна New Project (Новый проект) в Xcode.":::

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

callClient = ACSCallClient()
callClient?.createCallAgent(userCredential!,
    withCompletionHandler: { (callAgent, error) in
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

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="Снимок экрана с демонстрацией создания окна New Project (Новый проект) в Xcode." lightbox="../media/ios/xcode-push-notification.png":::

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
