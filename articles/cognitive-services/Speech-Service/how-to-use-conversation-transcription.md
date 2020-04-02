---
title: Транскрипция разговора в реальном времени (Предварительный просмотр) - Служба речи
titleSuffix: Azure Cognitive Services
description: Узнайте, как использовать транскрипцию разговоров в реальном времени с помощью речи SDK. Доступно для СЗ, СЗ и Явы.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.openlocfilehash: e2c9c0aadc8e443f07f60f3ccddb4a1b6dd661b1
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520908"
---
# <a name="real-time-conversation-transcription-preview"></a>Транскрипция разговора в реальном времени (Предварительный просмотр)

Речь SDK в **ConversationTranscriber** API позволяет транскрибировать встречи и другие разговоры с возможностью добавить, удалить `PullStream` `PushStream`и идентифицировать несколько участников, потоковое аудио на службу речи с помощью или . Эта тема требует, чтобы вы знали, как использовать речь к тексту с речью SDK (версия 1.8.0 или позже). Для получения дополнительной информации [см.](overview.md)

## <a name="limitations"></a>Ограничения

- API ConversationTranscriber поддерживается для СЗ, КЗ и Ява на Windows, Linux и Android.
- В настоящее время доступна на языках "en-US" и "zh-CN" в следующих регионах: _Центральная_ и _Восточная._
- Требуется 7-микрофонный многомикрофонный массив с справочным потоком воспроизведения. Микрофонный массив должен соответствовать [нашей спецификации.](https://aka.ms/sdsdk-microphone)
- [DDK Speech Devices](speech-devices-sdk.md) предоставляет подходящие устройства и пример приложения, демонстрирующего транскрипцию разговоров.

## <a name="optional-sample-code-resources"></a>Дополнительные ресурсы кода образца

Речевое устройство SDK предоставляет пример кода на Java для записи звука в режиме реального времени с помощью 8 каналов.

- [Код образца устройства ROOBO](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java)
- [Код образца образца Azure Kinect Dev Kit](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>Предварительные требования

Подписка на речевые услуги. Вы можете [получить пробную подписку на речь,](https://azure.microsoft.com/try/cognitive-services/) если у вас ее нет.

## <a name="create-voice-signatures"></a>Создание голосовых подписей

Первым шагом является создание голосовых подписей для участников беседы для эффективной идентификации динамиков.

### <a name="audio-input-requirements"></a>Требования к ввода звукам

- Файл входной аудиоволны для создания голосовых подписей должен быть в 16-битных образцах, частоте выборки 16 кГц и одном канале (моно) формате.
- Рекомендуемая длина каждого аудиообразца составляет от тридцати секунд до двух минут.

### <a name="sample-code"></a>Образец кода

В следующем примере показаны два различных способа создания голосовой подписи [с помощью REST API](https://aka.ms/cts/signaturegenservice) в C. Обратите внимание, что вам нужно будет заменить реальную информацию для "YourSubscriptionKey", ваше `{region}` имя файла волны для "speakerVoice.wav", и ваш регион для и "YourServiceRegion"_(центральный_ или _Восток_).

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribe-conversations"></a>Транскрибные разговоры

Следующий пример кода демонстрирует, как транскрибировать разговоры в режиме реального времени для трех динамиков. Предполагается, что вы уже создали голосовые подписи для каждого динамика, как показано выше. Заменить реальную информацию на "YourSubscriptionKey" и "YourServiceRegion" при создании объекта SpeechConfig.

Основные моменты примера кода включают:

- Создание `Conversation` объекта из `SpeechConfig` объекта с помощью идентификатора собрания, генерируемого с помощью`Guid.NewGuid()`
- Создание `ConversationTranscriber` объекта и присоединиться `JoinConversationAsync()` к разговору с, чтобы начать транскрипцию
- Регистрация интересных событий
- Добавление или удаление участников в беседу с помощью объекта «Разговор»
- Потоковая передача звука
- В версии Speech SDK 1.9.0 `int` `string` и далее оба и типы значений поддерживаются в поле версии голосовой подписи.

Транскрипция и идентификатор динамика возвращаются в зарегистрированные события.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Transcription;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region
        // Replace with your own subscription key and region
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            var meetingId = Guid.NewGuid().ToString();
            using (var conversation = await Conversation.CreateConversationAsync(config, meetingId).ConfigureAwait(false))
            {
                // Create a conversation transcriber using audio stream input
                using (var conversationTranscriber = new ConversationTranscriber    (audioInput))
                {
                    await conversationTranscriber.JoinConversationAsync(conversation);

                    // Subscribe to events
                    conversationTranscriber.Transcribing += (s, e) =>
                    {
                            Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text}");
                    };

                    conversationTranscriber.Transcribed += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    conversationTranscriber.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");

                        if (e.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                            stopTranscription.TrySetResult(0);
                        }
                    };

                    conversationTranscriber.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\nSession started event.");
                    };

                    conversationTranscriber.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\nSession stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopTranscription.TrySetResult(0);
                    };

                    // Add participants to the conversation.
                    // Create voice signatures using REST API described in the earlier section in this document.
                    // Voice signature needs to be in the following format:
                    // { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }

                    var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                    var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                    var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                    await conversation.AddParticipantAsync(speakerA);
                    await conversation.AddParticipantAsync(speakerB);
                    await conversation.AddParticipantAsync(speakerC);

                    // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                    await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopTranscription.Task });

                    // Stop transcribing the conversation.
                    await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
                 }
            }
       }
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Асинхронное транскрибирование бесед](how-to-async-conversation-transcription.md)
