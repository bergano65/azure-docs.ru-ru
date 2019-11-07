---
title: Запись разговора в реальном времени (Предварительная версия) — служба речи
titleSuffix: Azure Cognitive Services
description: Узнайте, как использовать транскрипцию разговора в режиме реального времени с помощью речевого пакета SDK. Доступно для C++, C#и Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.openlocfilehash: 4ee597f96d28b43b9c69d1515634aadde2f0ab7c
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73608055"
---
# <a name="real-time-conversation-transcription-preview"></a>Запись разговора в реальном времени (Предварительная версия)

API **конверсатионтранскрибер** для речевых средств позволяет транскрипция встречи и другие беседы с возможностью добавления, удаления и обнаружения нескольких участников путем потоковой передачи в службы речи с помощью `PullStream` или `PushStream`. В этом разделе вы узнаете, как использовать речь в тексте с пакетом SDK для распознавания речи (версия 1.8.0 или более поздняя). Дополнительные сведения см. в разделе [что такое речевые службы](overview.md).

## <a name="limitations"></a>Ограничения

- API конверсатионтранскрибер поддерживается для C++, C#и Java в Windows, Linux и Android.
- В настоящее время доступны в языках "en-US" и "zh-CN" в следующих регионах: _centralus_ и _eastasia_.
- Требуется 7-MIC циклический массив из нескольких микрофонов с потоком ссылки для воспроизведения. Массив микрофонов должен соответствовать [нашей спецификации](https://aka.ms/sdsdk-microphone).
- [Пакет SDK для речевых устройств](speech-devices-sdk.md) предоставляет подходящие устройства и пример приложения, демонстрирующий запись разговора.

## <a name="optional-sample-code-resources"></a>Необязательный образец ресурсов кода

Пакет SDK для речевых устройств предоставляет пример кода в Java для записи звука в реальном времени с помощью 8 каналов.

- [Пример кода устройства РУБО](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/Conversation.java)
- [Пример кода для пакета Azure Kinect Dev Kit](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>Предварительные требования

Подписка на речевые службы. Если у вас ее нет, вы можете [получить подписку на пробную версию речи](https://azure.microsoft.com/try/cognitive-services/) .

## <a name="create-voice-signatures"></a>Создание подписок

Первым шагом является создание голосовых подписей для участников диалога для эффективной идентификации докладчика.

### <a name="audio-input-requirements"></a>Требования к входным аудио

- Входной звуковой WAV-файл для создания голосовых подписей должен быть в 16-разрядных примерах, частоте выборки 16 кГц и одном канале (Mono).
- Рекомендуемая длина для каждого звукового образца — от тридцати секунд до двух минут.

### <a name="sample-code"></a>Пример кода

В следующем примере показаны два разных способа создания подписи голоса с [помощью REST API](https://aka.ms/cts/signaturegenservice) в C#. Обратите внимание, что необходимо заменить реальную информацию на "Йоурсубскриптионкэй", имя WAV-файла для "Спеакервоице. wav" и регион для `{region}` и "Йоурсервицерегион" (_centralus_ или _eastasia_).

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
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
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
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribe-conversations"></a>Транскрипция беседы

В следующем примере кода показано, как транскрипция беседы в режиме реального времени для трех динамиков. Предполагается, что для каждого динамика уже созданы голосовые подписи, как показано выше. При создании объекта Спичконфиг замените реальную информацию на "Йоурсубскриптионкэй" и "Йоурсервицерегион".

Примеры кода:

- Создание объекта `Conversation` из объекта `SpeechConfig` с помощью идентификатора собрания, созданного с помощью `Guid.NewGuid()`
- Создание `ConversationTranscriber` объекта и присоединение диалога к `JoinConversationAsync()` для начала записи
- Регистрация интересующих событий
- Добавление или удаление участников диалога с помощью объекта CONVERSATION
- Потоковая передача звука

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
            using (var conversation = new Conversation(config, meetingId))
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
                    // { "Version": <Numeric value>, "Tag": "string", "Data": "string" }

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
> [Асинхронная транскрипция диалога](how-to-async-conversation-transcription.md)
