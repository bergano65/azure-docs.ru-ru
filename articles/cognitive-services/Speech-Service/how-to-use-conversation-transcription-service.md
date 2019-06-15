---
title: Транскрипция нескольких участников беседы с Speech SDK - службы распознавания речи
titleSuffix: Azure Cognitive Services
description: Сведения об использовании расшифровка дикторского текста для диалога с пакетом SDK для распознавания речи. Для C++, C#и Java.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jhakulin
ms.openlocfilehash: f0838d345abfcfdf69ca6ea44d3206c23010b457
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073086"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Транскрипция нескольких участников беседы с пакетом SDK для распознавания речи

Пакет SDK для распознавания речи **ConversationTranscriber** API позволяет переписывать собрания/диалоги с возможностью добавления, удаления и определить участников путем потоковой передачи аудио в службы распознавания речи с помощью `PullStream` или `PushStream`.

## <a name="limitations"></a>Ограничения

* Обеспечивается поддержка диалога transcriber C++, C#и Java в Windows, Linux и Android.
* ROOBO DevKit является средой поддерживаемое оборудование для создания диалога транскрипции, предоставляющий циклическая массив несколькими "микрофон", могут использоваться эффективно для идентификации говорящего. [Дополнительные сведения см. в разделе Speech SDK устройств](speech-devices-sdk.md).
* Поддержка пакета SDK для распознавания речи для расшифровка дикторского текста для сообщений ограничена Pull в аудио и Push-режим потоки с 8 каналов звуковых PCM кГц, 16 на 16-разрядное.
* Расшифровка дикторского текста для диалога сейчас доступна в «en US» и «zh-CN» языков в следующих регионах: centralus и eastasia.

## <a name="prerequisites"></a>Технические условия

* [Сведения об использовании речи в текст с пакетом SDK для распознавания речи.](quickstart-csharp-dotnet-windows.md)
* [Получение пробной подписки речи.](https://azure.microsoft.com/try/cognitive-services/)
* Speech SDK версии 1.5.1 или более поздней.

## <a name="create-voice-signatures-for-participants"></a>Создание подписей голоса для участников

Первым шагом является создание голосовой подписи для участников диалога. Создание голосовой подписи является обязательным для идентификации говорящего эффективным.

### <a name="requirements-for-input-wave-file"></a>Требования для входного звукового файла

* Файл входной звуковой волны для создания подписи голоса должны иметь примеры 16-разрядных, 16 кГц частота выборки и формат один канал (Mono).
* Рекомендуемая длина для каждой аудиовыборки — от 30 секунд до двух минут.

В следующем примере показано два разных способа создания подписи голоса по [с помощью REST API](https://aka.ms/cts/signaturegenservice) из C#:

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

## <a name="transcribing-conversations"></a>Фотографировать диалогов

Чтобы переписать диалогов с несколькими участниками, создайте `ConversationTranscriber` объекта, связанного с `AudioConfig` объект, созданный для сеанса диалога и аудио с помощью потока `PullAudioInputStream` или `PushAudioInputStream`.

Предположим, что у вас есть ConversationTranscriber класс с именем `MyConversationTranscriber`. Код может выглядеть следующим образом:

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

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Примеры на сайте GitHub](https://aka.ms/csspeech/samples)
