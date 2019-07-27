---
title: Транскрипция бесед с несколькими участниками с помощью речевого пакета SDK — служба речи
titleSuffix: Azure Cognitive Services
description: Узнайте, как использовать транскрипцию разговора с помощью речевого пакета SDK. Доступно для C++, C#и Java.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: jhakulin
ms.openlocfilehash: 8c4ecc017d058900297f2220173e064700e7051b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559471"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Транскрипция бесед с несколькими участниками с помощью пакета SDK для распознавания речи

API **конверсатионтранскрибер** для речевых средств позволяет транскрипция встречи и беседы с возможностью добавлять, удалять и определять участников путем потоковой передачи в речевые службы с помощью `PullStream` или. `PushStream`

## <a name="limitations"></a>Ограничения

* Диалог транскрибер поддерживается для C++, C#и Java в Windows, Linux и Android.
* РУБО DevKit — это поддерживаемая аппаратная среда для создания сообщений о беседах, так как она предоставляет циклический многомикрофонный массив, который можно эффективно использовать для идентификации докладчика. Дополнительные сведения см. в статье [о пакете SDK для речевых устройств](speech-devices-sdk.md).
* Поддержка речевого пакета SDK для транскрипции ограничена использованием потоков передачи звука и режима push-уведомлений с восемью каналами 16-разрядных звуковых данных PCM в формате 16 кГц.
* В настоящее время запись разговора доступна в языках "en-US" и "zh-CN" в следующих регионах: centralus и eastasia.

## <a name="prerequisites"></a>Предварительные требования

* [Узнайте, как использовать речь в тексте с помощью пакета SDK для распознавания речи.](quickstart-csharp-dotnet-windows.md)
* [Получите подписку на пробную версию речи.](https://azure.microsoft.com/try/cognitive-services/)
* Требуется пакет SDK для распознавания речи версии 1.5.1 или более поздней.

## <a name="create-voice-signatures-for-participants"></a>Создание подписок для участников

Первым шагом является создание голосовых подписей для участников диалога. Для эффективной идентификации докладчика требуется создание голосовых подписей.

### <a name="requirements-for-input-wave-file"></a>Требования к входному звуковому файлу

* Входной звуковой волн для создания голосовых подписей должен быть 16-разрядными примерами, частотой выборки 16 кГц и одним каналом (Mono).
* Рекомендуемая длина для каждого звукового образца составляет от 30 секунд до двух минут.

В следующем примере показаны два разных способа создания подписи голоса с [помощью REST API](https://aka.ms/cts/signaturegenservice) из C#:

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
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

## <a name="transcribing-conversations"></a>Фотографировать беседы

Чтобы транскрипция беседы с несколькими участниками, создайте `ConversationTranscriber` объект, связанный `AudioConfig` с объектом, созданным для сеанса диалога, и потоковый звук `PullAudioInputStream` с `PushAudioInputStream`помощью или.

Предположим, что у вас есть класс Конверсатионтранскрибер с именем `MyConversationTranscriber`. Код может выглядеть следующим образом:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Conversation;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region.
        // Replace with your own subscription key and region.
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file.
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            // Creates a conversation transcriber using audio stream input.
            using (var transcriber = new ConversationTranscriber(config, audioInput))
            {
                // Subscribes to events.
                transcriber.Recognizing += (s, e) =>
                {
                    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                };

                transcriber.Recognized += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                transcriber.Canceled += (s, e) =>
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

                transcriber.SessionStarted += (s, e) =>
                {
                    Console.WriteLine("\nSession started event.");
                };

                transcriber.SessionStopped += (s, e) =>
                {
                    Console.WriteLine("\nSession stopped event.");
                    Console.WriteLine("\nStop recognition.");
                    stopTranscription.TrySetResult(0);
                };

                // Sets a conversation Id.
                transcriber.ConversationId = "AConversationFromTeams";

                // Add participants to the conversation.
                // Create voice signatures using REST API described in the earlier section in this document.
                // Voice signature needs to be in the following format:
                // { "Version": <Numeric value>, "Tag": "string", "Data": "string" }

                var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                transcriber.AddParticipant(speakerA);
                transcriber.AddParticipant(speakerB);
                transcriber.AddParticipant(speakerC);

                // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                await transcriber.StartTranscribingAsync().ConfigureAwait(false);

                // Waits for completion.
                // Use Task.WaitAny to keep the task rooted.
                Task.WaitAny(new[] { stopTranscription.Task });

                // Stop transcribing the conversation.
                await transcriber.StopTranscribingAsync().ConfigureAwait(false);

                // Ends the conversation.
                await transcriber.EndConversationAsync().ConfigureAwait(false);
            }
        }
    }
}
```

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Примеры на сайте GitHub](https://aka.ms/csspeech/samples)
