---
title: Рекомендации по реализации блокировки для голосов в Windows
titleSuffix: Azure Cognitive Services
description: Инструкции по реализации функций активации голоса и блокировки для приложения агента речи.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 7a142060a29561526c378ce04b23aa2b286cd6c1
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997406"
---
# <a name="implementing-voice-assistants-on-windows"></a>Реализация речевых помощников в Windows

В этом руководстве рассматриваются важные сведения о реализации для создания речевого помощника в Windows.

## <a name="implementing-voice-activation"></a>Реализация активации голоса

После [настройки среды](how-to-windows-voice-assistants-get-started.md) и изучения того, [как работает активация голоса](windows-voice-assistants-overview.md#how-does-voice-activation-work), можно приступить к реализации активации голоса для собственного приложения Voice Assistant.

### <a name="registration"></a>Регистрация

#### <a name="ensure-that-the-microphone-is-available-and-accessible-then-monitor-its-state"></a>Убедитесь, что микрофон доступен и доступен, а затем Отслеживайте его состояние.

MVA требуется, чтобы микрофон присутствовал и был доступен для обнаружения активации голоса. Используйте классы [аппкапабилити](https://docs.microsoft.com/uwp/api/windows.security.authorization.appcapabilityaccess.appcapability?view=winrt-18362), [девицеватчер](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher?view=winrt-18362)и [медиакаптуре](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture?view=winrt-18362) , чтобы проверить конфиденциальность микрофона, присутствие устройства и состояние устройства (например, громкость и отключение звука) соответственно.

### <a name="register-the-application-with-the-background-service"></a>Регистрация приложения в фоновой службе

Чтобы служба MVA запускала приложение в фоновом режиме, приложение должно быть зарегистрировано в фоновой службе. Ознакомьтесь с полным руководством по регистрации фоновой службы [здесь](https://docs.microsoft.com/windows/uwp/launch-resume/register-a-background-task).

### <a name="unlock-the-limited-access-feature"></a>Разблокирование функции ограниченного доступа

Используйте предоставленный корпорацией Майкрософт ключ ограниченного доступа, чтобы разблокировать функцию речевого помощника. Для этого используйте класс [лимитедакцессфеатуре](https://docs.microsoft.com/uwp/api/windows.applicationmodel.limitedaccessfeatures?view=winrt-18362) из Windows SDK.

### <a name="register-the-keyword-for-the-application"></a>Зарегистрируйте ключевое слово для приложения

Приложению необходимо зарегистрировать себя, его ключевое слово Model и его язык в Windows.

Начните с извлечения средства обнаружения ключевых слов. В этом примере кода мы получаем первое средство обнаружения, но можно выбрать конкретное средство обнаружения, выбрав его из `configurableDetectors`.

```csharp
private static async Task<ActivationSignalDetector> GetFirstEligibleDetectorAsync()
{
    var detectorManager = ConversationalAgentDetectorManager.Default;
    var allDetectors = await detectorManager.GetAllActivationSignalDetectorsAsync();
    var configurableDetectors = allDetectors.Where(candidate => candidate.CanCreateConfigurations
        && candidate.Kind == ActivationSignalDetectorKind.AudioPattern
        && (candidate.SupportedModelDataTypes.Contains("MICROSOFT_KWSGRAPH_V1")));

    if (configurableDetectors.Count() != 1)
    {
        throw new NotSupportedException($"System expects one eligible configurable keyword spotter; actual is {configurableDetectors.Count()}.");
    }

    var detector = configurableDetectors.First();

    return detector;
}
```

После получения объекта Активатионсигналдетектор вызовите его `ActivationSignalDetector.CreateConfigurationAsync` метод с идентификатором сигнала, идентификатором модели и отображаемым именем, чтобы зарегистрировать ключевое слово и получить приложение. `ActivationSignalDetectionConfiguration` Идентификаторы сигнала и модели должны быть идентификаторами GUID, принятыми разработчиком, и оставаться единообразными для одного и того же ключевого слова.

### <a name="verify-that-the-voice-activation-setting-is-enabled"></a>Убедитесь, что параметр активации голоса включен.

Чтобы использовать активацию голоса, пользователю необходимо включить активацию речи для своей системы и включить активацию речи для своего приложения. Этот параметр можно найти в разделе "Параметры конфиденциальности для активации голоса" раздела "Параметры Windows". Чтобы проверить состояние настройки активации голоса в приложении, используйте экземпляр `ActivationSignalDetectionConfiguration` из раздела регистрация ключевого слова. Поле [аваилабилитинфо](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-uwp/UWPVoiceAssistantSample/UIAudioStatus.cs#L128) в `ActivationSignalDetectionConfiguration` содержит значение enum, описывающее состояние настройки активации голоса.

### <a name="retrieve-a-conversationalagentsession-to-register-the-app-with-the-mva-system"></a>Получение Конверсатионалажентсессион для регистрации приложения в системе MVA

`ConversationalAgentSession` — Это класс в Windows SDK, который позволяет приложению обновлять окна с помощью состояния приложения (бездействие, обнаружение, прослушивание, работа, Диктовка) и получения событий, таких как обнаружение активации и изменения состояния системы, такие как блокировка экрана. Получение экземпляра Ажентсессион также служит для регистрации приложения в Windows как активируемого по голосовым. Рекомендуется поддерживать одну ссылку на `ConversationalAgentSession`. Чтобы получить сеанс, используйте `ConversationalAgentSession.GetCurrentSessionAsync` API.

### <a name="listen-to-the-two-activation-signals-the-onbackgroundactivated-and-onsignaldetected"></a>Прослушивать два сигнала активации: Онбаккграундактиватед и Онсигналдетектед

Windows подаст приложению сигнал при обнаружении ключевого слова одним из двух способов. Если приложение неактивно (т. е. у вас нет ссылки на неликвидированный экземпляр `ConversationalAgentSession`), оно запустит приложение и вызовет метод онбаккграундактиватед в файле App.XAML.CS приложения. Если поле "аргументы события `BackgroundActivatedEventArgs.TaskInstance.Task.Name` " совпадает со строкой "ажентбаккграундтригжер", запуск приложения был инициирован с помощью активации голоса. Приложению необходимо переопределить этот метод и получить экземпляр Конверсатионалажентсессион, чтобы сообщить о том, что Windows теперь активна. Когда приложение активно, Windows будет сообщать о возникновении активации голоса с помощью `ConversationalAgentSession.OnSignalDetected` события. Добавьте обработчик событий для этого события, как только вы получите `ConversationalAgentSession`.

## <a name="keyword-verification"></a>Проверка ключевого слова

После активации приложения агента голоса с помощью голоса необходимо убедиться, что обнаружение ключевого слова допустимо. Windows не предоставляет решение для проверки ключевых слов, но оно разрешает помощникам голосовой почты получать доступ к аудио из гипотетической активации и выполнять проверку самостоятельно.

### <a name="retrieve-activation-audio"></a>Получение звука активации

Создайте [аудиограф](https://docs.microsoft.com/uwp/api/windows.media.audio.audiograph) и передайте его в `CreateAudioDeviceInputNodeAsync` коллекцию. `ConversationalAgentSession` Это приведет к загрузке звукового буфера графа с аудио, который *начинается приблизительно 3 секунды, прежде чем было обнаружено ключевое слово*. Этот дополнительный ведущий звук включает в себя широкий спектр ключевых слов и частот для динамиков. Затем обработайте событие [куантумстартед](https://docs.microsoft.com/uwp/api/windows.media.audio.audiograph.quantumstarted?view=winrt-18362) из аудио графа для получения звуковых данных.

```csharp
var inputNode = await agentSession.CreateAudioDeviceInputNodeAsync(audioGraph);
var outputNode = inputGraph.CreateFrameOutputNode();
inputNode.AddOutgoingConnection(outputNode);
audioGraph.QuantumStarted += OnQuantumStarted;
```

Примечание. Ведущий звук, входящий в буфер аудио, может привести к сбою проверки по ключевым словам. Чтобы устранить эту проблему, обрежьте начало звукового буфера перед отправкой звука для проверки ключевого слова. Эта начальная Обрезка должна быть адаптирована для каждого помощника.

### <a name="launch-in-the-foreground"></a>Запуск на переднем плане

После завершения проверки ключевых слов приложение должно перейти на передний план, чтобы отобразить пользовательский интерфейс. Вызовите `ConversationalAgentSession.RequestForegroundActivationAsync` API, чтобы переместить приложение на передний план.

### <a name="transition-from-compact-view-to-full-view"></a>Переход от компактного представления к полному представлению

Когда приложение сначала активируется голосовым методом, оно запускается в компактном представлении. Ознакомьтесь с [руководством по проектированию для предварительной версии активации голоса](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) , чтобы получить рекомендации по различным представлениям и переходам между ними для речевых помощников в Windows.

Чтобы перевести переход с Compact представления на полное представление приложения, используйте API `TryEnterViewModeAsync`аппликатионвиев:

```csharp
var appView = ApplicationView.GetForCurrentView();
await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);
```

## <a name="implementing-above-lock-activation"></a>Реализация выше активации блокировки

В следующих шагах рассматриваются требования для включения речевого помощника в Windows для запуска над блокировкой, включая ссылки на примеры кода и рекомендации по управлению жизненным циклом приложения. Рекомендации по проектированию приведенных выше функций блокировки см. в [руководстве по использованию](windows-voice-assistants-best-practices.md).

### <a name="detecting-lock-screen-transitions"></a>Обнаружение переходов экрана блокировки

Библиотека Конверсатионалажент в Windows SDK предоставляет API для того, чтобы сделать состояние экрана блокировки доступным и изменить состояние экрана блокировки. Чтобы обнаружить текущее состояние экрана блокировки, проверьте `ConversationalAgentSession.IsUserAuthenticated` поле. Чтобы обнаружить изменения в состоянии блокировки, добавьте обработчик событий в `ConversationalAgentSession` `SystemStateChanged` событие объекта. Он будет срабатывать всякий раз, когда экран изменится с "разблокирован" на "заблокировано" или наоборот. Если значение аргументов события равно `ConversationalAgentSystemStateChangeType.UserAuthentication`, состояние экрана блокировки изменилось и приложение должно закрыться.

```csharp
// When the app changes lock state, close the application to prevent duplicates running at once
conversationalAgentSession.SystemStateChanged += (s, e) =>
{
    if (e.SystemStateChangeType == ConversationalAgentSystemStateChangeType.UserAuthentication)
    {
        WindowService.CloseWindow();
    }
};
```

### <a name="detecting-above-lock-activation-user-preference"></a>Обнаружение указанных выше настроек пользователя для активации блокировки

Запись приложения на странице Параметры конфиденциальности активации голоса имеет переключатель для функции блокировки выше. Чтобы приложение могло запуститься до блокировки, пользователь должен будет включить этот параметр. Состояние приведенных выше разрешений на блокировку также хранится в объекте Активатионсигналдетектионконфигуратион. Состояние Аваилабилитинфо. Хаслоккскринпермиссион отражает, выдает ли пользователь выше разрешение Lock. Если этот параметр отключен, голосовое приложение может запросить у пользователя ссылку на соответствующую страницу параметров по ссылке "MS-Settings: privacy-воицеактиватион" с инструкциями о том, как включить активацию выше блокировки для приложения.

## <a name="closing-the-application"></a>Закрытие приложения

Чтобы правильно закрыть приложение программным способом при блокировке выше или ниже, используйте `WindowService.CloseWindow()` API. Это активирует все методы жизненного цикла UWP, в том числе onsuspend, позволяя приложению удалить `ConversationalAgentSession` его экземпляр перед закрытием.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Примеры и пошаговые инструкции по созданию кода см. в примере приложения "голосовое помощник UWP".](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)
