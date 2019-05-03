---
title: Транскрипция нескольких участников беседы с Speech SDK - службы распознавания речи
titleSuffix: Azure Cognitive Services
description: Узнайте, как использовать службу расшифровка дикторского текста для диалога с пакетом SDK для распознавания речи. Для C++, C#и Java.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: jhakulin
ms.openlocfilehash: 73ab4cfa92a1efc49dea16ba2941cf16b7a1cf3e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025796"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Транскрипция нескольких участников беседы с пакетом SDK для распознавания речи

Пакет SDK для распознавания речи **ConversationTranscriber** API позволяет переписывать собрания/диалоги с возможностью добавления, удаления и определить участников путем потоковой передачи аудио в службы распознавания речи с помощью `PullStream` или `PushStream`.

## <a name="limitations"></a>Ограничения

* Обеспечивается поддержка диалога transcriber C++, C#и Java в Windows, Linux и Android.
* ROOBO DevKit является средой поддерживаемое оборудование для создания диалогов, предоставляющий циклическая массив несколькими "микрофон", который может эффективно использоваться служба диалога расшифровка дикторского текста для идентификации говорящего. [Дополнительные сведения см. в разделе Speech SDK устройств](speech-devices-sdk.md). 
* Поддержка пакета SDK для распознавания речи ограничено использование аудио по запросу и отправьте режим потоки с 8 каналов PCM звука.

## <a name="prerequisites"></a>Технические условия

* [Сведения об использовании речи в текст с пакетом SDK для распознавания речи.](quickstart-csharp-dotnet-windows.md)
* [Получение пробной подписки речи.](https://azure.microsoft.com/try/cognitive-services/)

## <a name="create-voice-signatures-for-participants"></a>Создание подписей голоса для участников

Первым шагом является создание голосовой подписи для участников диалога. Создание голосовой подписи является обязательным для идентификации говорящего эффективным.
В следующем примере мы будем [использовать REST API для получения голосовой подписи.](https://aka.ms/cts/signaturegenservice)

В приведенном ниже примере показано два разных способа создания подписей voice:
```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://{region}.signature.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature can be extracted from the jsonData
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature can be extracted from the jsonData
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
    private static string endpoint = "YourOwnEndpoint";

    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region.
        // Replace with your own endpoint and subscription key.
        var config = SpeechConfig.FromEndpoint(new Uri(endpoint), "YourSubScriptionKey");
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
                        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}, SpeakerID={e.Result.SpeakerId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                // Sets a conversation Id.
                transcriber.ConversationId = "AConversationFromTeams";

                // Add participants to the conversation.
                // Create data for voice signatures using REST API described in the earlier section in this document.
                // How to create voice signatureA, signatureB & signatureC variables, please check the SDK API samples.

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
