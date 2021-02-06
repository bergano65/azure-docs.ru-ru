---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 7d391998e7f20cff0f77f6aab7938bc375f75c9e
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99616627"
---
## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно. 
- Развернутый ресурс Служб коммуникации. [Создайте ресурс Служб коммуникации.](../../create-communication-resource.md)
- `User Access Token` для включения клиента вызова. Дополнительные сведения о том, [как получить `User Access Token`](../../access-tokens.md)
- Необязательно. Выполните инструкции из краткого руководства по [началу работы с добавлением вызова в приложение](../getting-started-with-calling.md) .

## <a name="setting-up"></a>Настройка

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

Используйте `npm install` команду, чтобы установить вызовы и общие клиентские библиотеки служб связи Azure для JavaScript.

```console
npm install @azure/communication-calling --save
```

## <a name="object-model"></a>Объектная модель

Следующие классы и интерфейсы реализуют некоторые основные функции клиентской библиотеки Служб коммуникации Azure для вызовов:

| Имя                             | Описание                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient — это основная точка входа в клиентскую библиотеку для вызовов.                                                                       |
| CallAgent                        | CallAgent используется для инициирования вызовов и управления ими.                                                                                            |
| девицеманажер                    | Девицеманажер используется для управления устройствами мультимедиа.                                                                                           |
| азурекоммуникатионтокенкредентиал | Класс Азурекоммуникатионтокенкредентиал реализует интерфейс Коммуникатионтокенкредентиал, который используется для создания экземпляра Каллажент. |


## <a name="initialize-the-callclient-create-callagent-and-access-devicemanager"></a>Инициализация Каллклиент, создание Каллажент и доступ к Девицеманажер

Создайте `CallClient` экземпляр нового экземпляра. Его можно настроить с помощью настраиваемых параметров, таких как экземпляр средства ведения журнала.
После `CallClient` создания экземпляра можно создать `CallAgent` экземпляр, вызвав `createCallAgent` метод в `CallClient` экземпляре. Это асинхронно возвращает `CallAgent` объект экземпляра.
`createCallAgent`Метод принимает в `CommunicationTokenCredential` качестве аргумента, который принимает [маркер доступа пользователя](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens).
Для доступа к `DeviceManager` экземпляру каллажент необходимо сначала создать. Затем можно использовать метод для `getDeviceManager` `CallClient` экземпляра, чтобы получить девицеманажер.

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-an-outgoing-call"></a>Размещение исходящего вызова

Чтобы создать и запустить вызов, необходимо использовать один из API-интерфейсов на Каллажент и предоставить пользователя, созданного с помощью клиентской библиотеки администрирования служб связи.

Создание и запуск вызываются синхронно. Экземпляр Call позволяет подписываться на события вызова.

## <a name="place-a-call"></a>Осуществление вызовов

### <a name="place-a-11-call-to-a-user-or-pstn"></a>Поместите вызов 1:1 для пользователя или PSTN
Чтобы поместить вызов другого пользователя служб Communication Services, вызовите `call` метод в `callAgent` и передайте его коммуникатионусеридентифиер:

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.call([userCallee]);
```

Чтобы поместить вызов в PSTN, вызовите `call` метод для `callAgent` и передайте его фоненумберидентифиер.
Для ресурса служб связи необходимо настроить разрешение на вызов PSTN.
При вызове номера PSTN необходимо указать идентификатор альтернативного звонящего.
```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.call([pstnCallee], {alternateCallerId});
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Поместите вызов 1: n с помощью пользователей и PSTN
```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.call([userCallee, pstnCallee], {alternateCallerId});
```

### <a name="place-a-11-call-with-video-camera"></a>Размещение 1:1 звонка с помощью видеокамеры
> [!WARNING]
> Сейчас может быть не более одного исходящего локального потока видео.
Чтобы поместить видеовызов, необходимо перечислить локальные камеры с помощью `getCameraList` API девицеманажер.
После выбора нужной камеры используйте ее для создания `LocalVideoStream` экземпляра и передайте его в `videoOptions` качестве элемента в `localVideoStream` массиве в `call` метод.
После того, как звонок будет подключен, автоматически начнется отправка видеопотока из выбранной камеры другим участникам. Это также относится к параметрам Call. Accept () видео и Каллажент. Join () видео.
```js
const deviceManager = await callClient.getDeviceManager();
const videoDeviceInfo = deviceManager.getCameraList()[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.call(['acsUserId'], placeCallOptions);

```

### <a name="join-a-group-call"></a>Присоединение к групповому вызову
Чтобы начать новый вызов группы или присоединиться к текущему вызову группы, используйте метод "Join" и передайте объект со `groupId` свойством. Значение должно быть идентификатором GUID.
```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```
### <a name="join-a-teams-meeting"></a>Присоединяйтесь к собранию команд
Чтобы присоединиться к собранию команд, используйте метод "Join" и передайте ссылку на собрание или координату собрания.
```js
// Join using meeting link
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);

// Join using meeting coordinates
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receiving-an-incoming-call"></a>Получение входящего вызова

`CallAgent`Экземпляр создает `incomingCall` событие, когда зарегистрированное в системе удостоверение получает входящий вызов. Чтобы прослушать это событие, подпишитесь следующим образом:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    //accept the call
    var call = await incomingCall.accept();

    //reject the call
    incomingCall.reject();
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

Это `incomingCall` событие предоставит экземпляр, `IncomingCall` на котором можно принять или отклонить вызов.


## <a name="call-management"></a>Управление вызовами

Вы можете получить доступ к свойствам вызова и выполнить различные операции во время вызова для управления параметрами, связанными с видео и аудио.

### <a name="call-properties"></a>Свойства вызова
* Получите уникальный идентификатор (String) для этого вызова.
```js

const callId: string = call.id;

```

* Чтобы узнать о других участниках вызова, просмотрите `remoteParticipant` коллекцию в `call` экземпляре. Массив содержит `RemoteParticipant` объекты списка
```js
const remoteParticipants = call.remoteParticipants;
```

* Удостоверение вызывающего объекта, если вызов является входящим. Identity является одним из `CommunicationIdentifier` типов
```js

const callerIdentity = call.callerInfo.identity;

```

* Возвращает состояние вызова.
```js

const callState = call.state;

```
Возвращает строку, представляющую текущее состояние вызова:
* "None" — состояние начального вызова
* "Входящий" — указывает, что вызов является входящим, он должен быть либо принят, либо отклонен.
* "Подключение" — начальное состояние перехода, когда вызов помещается или принимается
* "Кольцо" — для исходящего вызова — указывает, что вызов вызывается для удаленных участников, это "входящее" на их стороне
* "Еарлимедиа" — указывает состояние, в котором воспроизводится объявление перед подключением вызова
* "Connected" — вызов подключен
* "Удержание" — вызов помещается в удержание, медиа-файлы не передаются между локальной конечной точкой и удаленными участниками
* "Идет отключение" — состояние перехода перед тем, как вызов переходит в состояние "disconnected"
* "Disconnected" — окончательное состояние вызова
  * Если сетевое подключение потеряно, состояние переходит в режим "отключено" через 2 минуты.

* Чтобы увидеть, почему заданный вызов завершен, проверьте `callEndReason` свойство.
```js

const callEndReason = call.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* Чтобы узнать, является ли текущий вызов входящим или исходящим вызовом, проверьте `direction` свойство, возвращаемое `CallDirection` .
```js
const isIncoming = call.direction == 'Incoming';
const isOutgoing = call.direction == 'Outgoing';
```

*  Чтобы проверить, отключен ли текущий микрофон, проверьте `muted` свойство, которое возвращается `Boolean` .
```js

const muted = call.isMicrophoneMuted;

```

* Чтобы узнать, отправляется ли поток обмена на экран из данной конечной точки, проверьте `isScreenSharingOn` свойство, возвращаемое `Boolean` .
```js

const isScreenSharingOn = call.isScreenSharingOn;

```

* Чтобы проверить активные видеопотоки, проверьте `localVideoStreams` коллекцию, содержащую `LocalVideoStream` объекты
```js

const localVideoStreams = call.localVideoStreams;

```

### <a name="call-ended-event"></a>Событие завершения вызова

`Call`Экземпляр создает `callEnded` событие при завершении вызова. Для прослушивания этого события Подпишитесь следующим образом:

```js
const callEndHander = async (args: { callEndReason: CallEndReason }) => {
    console.log(args.callEndReason)
};

call.on('callEnded', callEndHander);
```

### <a name="mute-and-unmute"></a>Отключить и включить звук

Чтобы отключить или включить выключение локальной конечной точки, можно использовать `mute` `unmute` асинхронные API и.

```js

//mute local device 
await call.mute();

//unmute local device 
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Запуск и отмена отправки локального видео


Чтобы запустить видео, необходимо перечислить камеры с помощью `getCameraList` метода `deviceManager` объекта. Затем создайте новый экземпляр, `LocalVideoStream` передающий нужную камеру в `startVideo` метод в качестве аргумента:


```js
const localVideoStream = new LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);

```

После успешного начала отправки видео `LocalVideoStream` экземпляр будет добавлен в `localVideoStreams` коллекцию в экземпляре вызова.

```js

call.localVideoStreams[0] === localVideoStream;

```

Чтобы отключить локальное видео, передайте `localVideoStream` экземпляр, доступный в `localVideoStreams` коллекции:

```js

await call.stopVideo(localVideoStream);

```

При отправке видео можно переключиться на другое устройство камеры, вызвав `switchSource` на `localVideoStream` экземпляре:

```js
const source callClient.getDeviceManager().getCameraList()[1];
localVideoStream.switchSource(source);

```

## <a name="remote-participants-management"></a>Управление удаленными участниками

Все удаленные участники представлены `RemoteParticipant` типом и доступны через `remoteParticipants` коллекцию в экземпляре вызова.

### <a name="list-participants-in-a-call"></a>Вывод списка участников в вызове
`remoteParticipants`Коллекция возвращает список удаленных участников в данном вызове:

```js

call.remoteParticipants; // [remoteParticipant, remoteParticipant....]

```

### <a name="remote-participant-properties"></a>Свойства удаленного участника
Удаленный участник имеет набор свойств и коллекций, связанных с ним
#### <a name="communicationidentifier"></a>CommunicationIdentifier
Получите идентификатор для этого удаленного участника.
```js
const identifier = remoteParticipant.identifier;
```
Это может быть один из типов "Коммуникатионидентифиер":
  * {Коммуникатионусерид: ' <ACS_USER_ID ' >}-объект, представляющий пользователя ACS
  * {phoneNumber: ' <E. 164> '} — объект, представляющий номер телефона в формате E. 164
  * {Микрософттеамсусерид: ' <TEAMS_USER_ID> ', Anonymous?: Boolean; облако?: "Public" | "DOD" | "гкч"} — объект, представляющий пользователя команд

#### <a name="state"></a>Область
Получение состояния удаленного участника.
```js

const state = remoteParticipant.state;
```
Состояние может быть одним из
* "Idle" — начальное состояние
* "Connected" — состояние перехода во время подключения участника к вызову
* "Connected" — участник подключен к вызову
* "Удержание"-участник находится в удержании
* "Еарлимедиа" — объявление воспроизводится до того, как участник подключен к вызову
* "Disconnected" — конечное состояние — участник отключен от вызова
  * Если удаленный участник потерял подключение к сети, состояние удаленного участника переходит в режим "отключено" через 2 минуты.

#### <a name="call-end-reason"></a>Причина окончания вызова
Чтобы узнать, почему участник оставил вызов, проверьте `callEndReason` свойство:
```js
const callEndReason = remoteParticipant.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```
#### <a name="is-muted"></a>Отключен
Чтобы проверить, не отключен ли удаленный участник, проверьте `isMuted` свойство, возвращаемое значение `Boolean`
```js
const isMuted = remoteParticipant.isMuted;
```
#### <a name="is-speaking"></a>Говорит
Чтобы проверить, говорит ли этот удаленный участник, проверьте `isSpeaking` возвращаемое свойство. `Boolean`
```js
const isSpeaking = remoteParticipant.isSpeaking;
```

#### <a name="video-streams"></a>Потоки видео
Чтобы проверить все потоки видео, отправляемые данным участником в этом вызове, проверьте `videoStreams` коллекцию, содержащую `RemoteVideoStream` объекты
```js

const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]

```


### <a name="add-a-participant-to-a-call"></a>Добавление участника в вызов

Чтобы добавить участника в вызов (либо пользователя, либо номер телефона), можно вызвать `addParticipant` .
Укажите один из типов "идентификатор".
Это приведет к синхронному возврату экземпляра удаленного участника.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-participant-from-a-call"></a>Удалить участника из вызова

Чтобы удалить участника из вызова (либо пользователя, либо номера телефона), можно вызвать `removeParticipant` .
Необходимо передать один из типов "идентификатор", который будет разрешаться асинхронно после удаления участника из вызова.
Участник также будет удален из `remoteParticipants` коллекции.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>Прорисовка потоков видео удаленных участников

Чтобы получить список потоков видео и потоков, использующих удаленные участники, просмотрите `videoStreams` коллекции:

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.type;
```
 
Чтобы подготовиться к просмотру `RemoteVideoStream` , необходимо подписываться на `isAvailableChanged` событие.
Если `isAvailable` свойство изменяется на `true` , удаленный участник отправляет поток.
После этого создайте новый экземпляр `Renderer` , а затем создайте новый `RendererView` экземпляр с помощью асинхронного `createView` метода.  Затем вы можете присоединиться `view.target` к любому элементу пользовательского интерфейса.
При каждом изменении доступности удаленного потока можно удалить весь модуль подготовки отчетов, конкретный `RendererView` или оставить его, но это приведет к отображению пустого кадра видео.

```js
let renderer: Renderer = new Renderer(remoteParticipantStream);
const displayVideo = () => {
    const view = await renderer.createView();
    htmlElement.appendChild(view.target);
}
remoteParticipantStream.on('availabilityChanged', async () => {
    if (remoteParticipantStream.isAvailable) {
        displayVideo();
    } else {
        renderer.dispose();
    }
});
if (remoteParticipantStream.isAvailable) {
    displayVideo();
}
```

### <a name="remote-video-stream-properties"></a>Свойства удаленного потока видео
Удаленные потоки видео имеют следующие свойства:

* `Id` — Идентификатор удаленного потока видео.
```js
const id: number = remoteVideoStream.id;
```

* `StreamSize` — Размер (ширина и высота) удаленного видеопотока;
```js
const size: {width: number; height: number} = remoteVideoStream.size;
```

* `MediaStreamType` -может быть "Video" или "Скриншаринг"
```js
const type: MediaStreamType = remoteVideoStream.type;
```
* `isAvailable` — Указывает, является ли конечная точка удаленного участника активной отправкой потока
```js
const type: boolean = remoteVideoStream.isAvailable;
```

### <a name="renderer-methods-and-properties"></a>Методы и свойства модуля подготовки отчетов

* Создайте `RendererView` экземпляр, который позже можно будет подключить в пользовательском интерфейсе приложения для отображения удаленного видеопотока.
```js
renderer.createView()
```

* Уничтожает модуль подготовки отчетов и все связанные с ним `RendererView` экземпляры.
```js
renderer.dispose()
```


### <a name="rendererview-methods-and-properties"></a>Методы и свойства Рендерервиев
При создании `RendererView` можно указать `scalingMode` `mirrored` Свойства и.
Режим масштабирования может быть "Stretch", "обрезать" или "вписать", если `Mirrored` указан параметр, отрисованный поток будет зеркально передаваться по вертикали.

```js
const rendererView: RendererView = renderer.createView({ scalingMode, mirrored });
```
Каждый заданный `RendererView` экземпляр имеет `target` свойство, представляющее область отрисовки. Он должен быть присоединен в пользовательском интерфейсе приложения:
```js
document.body.appendChild(rendererView.target);
```

Позднее можно обновить режим масштабирования, вызвав `updateScalingMode` метод.
```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Управление устройствами

`DeviceManager` позволяет перечислить локальные устройства, которые можно использовать в вызове для передачи аудио-или видеопотоков. Он также позволяет запрашивать у пользователя разрешение на доступ к микрофону и камере с помощью собственного API браузера.

Доступ к можно получить `deviceManager` , вызвав `callClient.getDeviceManager()` метод.
> [!WARNING]
> Сейчас `callAgent` для получения доступа к девицеманажер необходимо сначала создать экземпляр объекта.

```js

const deviceManager = await callClient.getDeviceManager();

```

### <a name="enumerate-local-devices"></a>Перечисление локальных устройств

Для доступа к локальным устройствам можно использовать методы перечисления в диспетчер устройств. Перечисление является синхронным действием.

```js

//  Get a list of available video devices for use.
const localCameras = deviceManager.getCameraList(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = deviceManager.getMicrophoneList(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = deviceManager.getSpeakerList(); // [AudioDeviceInfo, AudioDeviceInfo...]

```

### <a name="set-default-microphonespeaker"></a>Установка микрофона или динамика по умолчанию

Диспетчер устройств позволяет задать устройство по умолчанию, которое будет использоваться при запуске вызова.
Если значения по умолчанию для клиента не заданы, службы связи будут возвращаться к значениям по умолчанию для ОС.

```js

// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.getMicrophone();

// Set the microphone device to use.
await deviceManager.setMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.getSpeaker();

// Set the speaker device to use.
await deviceManager.setSpeaker(AudioDeviceInfo);

```

### <a name="local-camera-preview"></a>Предварительная версия локальной камеры

Вы можете использовать `DeviceManager` и, `Renderer` чтобы начать отрисовку потоков из локальной камеры. Этот поток не будет отправлен другим участникам; Это локальный канал предварительной версии. Это асинхронное действие.

```js
const localVideoDevice = deviceManager.getCameraList()[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-cameramicrophone"></a>Запросить разрешение для камеры или микрофона

Запрашивать у пользователя разрешение на доступ к камере и микрофону следующим образом:

```js
const result = await deviceManager.askDevicePermission(audio: true, video: true);
```
Это будет разрешено асинхронно с помощью объекта, указывающего `audio` , `video` были ли предоставлены разрешения.
```js
console.log(result.audio);
console.log(result.video);
```

Проверить текущее состояние разрешения для данного типа можно путем вызова `getPermissionState` :

```js

const result = deviceManager.getPermissionState('Microphone'); // for microphone permission state
const result = deviceManager.getPermissionState('Camera'); // for camera permission state

console.log(result); // 'Granted' | 'Denied' | 'Prompt' | 'Unknown';

```

## <a name="call-recording-management"></a>Управление записью звонков

Запись вызовов — это расширенная функция базового `Call` API. Сначала необходимо получить объект API функции записи:

```js
const callRecordingApi = call.api(Features.Recording);
```

Затем, чтобы проверить, записывается ли вызов, проверьте `isRecordingActive` свойство объекта `callRecordingApi` , возвращаемое значение `Boolean` .

```js
const isResordingActive = callRecordingApi.isRecordingActive;
```

Вы также можете подписываться на запись изменений:

```js
const isRecordingActiveChangedHandler = () => {
  console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);
               
```

## <a name="call-transfer-management"></a>Управление перенаправлением вызовов

Функция "перенаправление вызова" является расширенной функцией основного `Call` API. Сначала необходимо получить объект API функции перемещения:

```js
const callTransferApi = call.api(Features.Transfer);
```

В процесс перемещения вызовов входит *три стороны, получатель и*  *цель перемещения*. Поток передачи работает следующим образом:

1. Между *перенаправлением* *и объектом* уже есть подключенный вызов
2. *получатель* принимает решение *о переносе вызова (*  ->  *Цель перенаправления*)
3. API вызова *пересылки* `transfer`
4. *получатель* решает, должен ли `accept` `reject` запрос на перемещение *передавать цели* через `transferRequested` событие.
5. *цель перемещения* будет принимать входящий вызов только в том случае, если *получатель* сделал `accept` запрос на перенос

### <a name="transfer-terminology"></a>Терминология перемещения

- Получатель — тот, который инициирует запрос на перемещение.
- Передаваемый объект — тот, который передается перенаправлением на цель передачи.
- Целевой объект передачи — тот, в который передается целевой объект.

Для перемещения текущего вызова можно использовать `transfer` синхронный API. `transfer` принимает необязательный `TransferCallOptions` параметр, который позволяет установить `disableForwardingAndUnanswered` флаг:

- `disableForwardingAndUnanswered` = false — если *целевой объект передачи* не отвечает на вызов передачи, то он будет следовать за перенаправлением *передачи целевого объекта* и без ответа.
- `disableForwardingAndUnanswered` = true — если *целевой объект перемещения* не отвечает на вызов пересылки, то будет завершена попыток перенаправления

```js
// transfer target can be ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

Перемещение позволяет подписываться на `transferStateChanged` `transferRequested` события и. `transferRequsted` событие поступает из экземпляра `call` , `transferStateChanged` события и перемещения `state` и `error` берется из `transfer` экземпляра

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if transfer request failed
```

Получатель может принять или отклонить запрос на перемещение, инициированный средством перемещения в `transferRequested` рамках события с помощью `accept()` или `reject()` в `transferRequestedEventArgs` . Доступ к `targetParticipant` сведениям, `accept` , `reject` методам можно получить в `transferRequestedEventArgs` .

```js
// Transferee to accept the transfer request
callTransferApi.on('transferRequested', args => {
  args.accept();
});

// Transferee to reject the transfer request
callTransferApi.on('transferRequested', args => {
  args.reject();
});
```

## <a name="eventing-model"></a>Модель событий

Вы можете подписываться на большинство свойств и коллекций, чтобы получать уведомления при изменении значений.

### <a name="properties"></a>Свойства
Чтобы подписываться на `property changed` события, выполните следующие действия.

```js

const eventHandler = () => {
    // check current value of a property, value is not passed to callback
    console.log(object.property);
};
object.on('propertyNameChanged',eventHandler);

// To unsubscribe:

object.off('propertyNameChanged',eventHandler);

```

### <a name="collections"></a>Коллекции
Чтобы подписываться на `collection updated` события, выполните следующие действия.

```js

const eventHandler = (e) => {
    // check added elements
    console.log(e.added);
    // check removed elements
    console.log(e.removed);
};
object.on('collectionNameUpdated',eventHandler);

// To unsubscribe:

object.off('collectionNameUpdated',eventHandler);

```
