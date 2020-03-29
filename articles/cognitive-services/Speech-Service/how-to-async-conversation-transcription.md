---
title: Асинхронная транскрипция разговора (Предварительный просмотр) - Служба речи
titleSuffix: Azure Cognitive Services
description: Узнайте, как использовать асинхронную транскрипцию разговоров с помощью службы Speech. Доступно только для Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: 57543f4a3779145ce66259eec1abac195b63c7ba
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384302"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Асинхронная транскрипция разговора (Предварительный просмотр)

В этой статье асинхронная транскрипция разговоров демонстрируется с помощью **API RemoteConversationTranscriptionClient.** Если вы настроили разговор транскрипции, чтобы сделать `conversationId`асинхронную транскрипцию и, вы можете получить транскрипцию, связанную с этим `conversationId` с помощью **RemoteConversationTranscriptionClient** API.

## <a name="asynchronous-vs-real-time--asynchronous"></a>Асинхронный против реального времени - асинхронный

С асинхронной транскрипции, вы поток разговор аудио, но не нужно транскрипции вернулся в режиме реального времени. Вместо этого, после отправки `conversationId` аудио, используйте `Conversation` запрос для состояния асинхронной транскрипции. Когда асинхронная транскрипция будет готова, вы получите `RemoteConversationTranscriptionResult`.

С реальным временем плюс асинхронный, вы получите транскрипцию в режиме реального времени, но и получить транскрипцию, задав запрос с `conversationId` (по аналогии с асинхронным сценарием).

Для выполнения асинхронной транскрипции требуется два шага. Первый шаг заключается в загрузке аудио, выбирая либо асинхронные только или в режиме реального времени плюс асинхронный. Второй шаг заключается в том, чтобы получить результаты транскрипции.

## <a name="upload-the-audio"></a>Загрузить аудио

Прежде чем асинхронная транскрипция может быть выполнена, вам нужно отправить аудио в службу разговорной транскрипции с помощью Microsoft Cognitive Speech клиента SDK (версия 1.8.0 или выше).

Этот пример кода показывает, как создать транскрибный разговор для асинхронного режима. Для того, чтобы поток аудио на транскрибирование, вам нужно будет добавить аудио потокового кода, полученного из [транскрибных разговоров в режиме реального времени с речью SDK.](how-to-use-conversation-transcription-service.md) Обратитесь в раздел Ограничения этой **темы,** чтобы просмотреть поддерживаемые платформы и Языковые AA.

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speechConfig.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// pick a conversation Id that is a GUID.
String conversationId = UUID.randomUUID().toString();

// Create a Conversation
Future<Conversation> conversationFuture = Conversation.createConversationAsync(speechConfig, conversationId);
Conversation conversation = conversationFuture.get();

// Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
// Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16kHz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16kHz, 16 bits and 8 channel pcm wav file
AudioStreamFormat audioStreamFormat = AudioStreamFormat.getWaveFormatPCM((long)16000, (short)16,(short)8);
// Create an input stream
AudioInputStream audioStream = AudioInputStream.createPullStream(wavfilePullStreamCallback, audioStreamFormat);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromStreamInput(audioStream));

// join a conversation
transcriber.joinConversationAsync(conversation);

// Add the event listener for the realtime events
transcriber.transcribed.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber Recognized:" + e.toString());
});

transcriber.canceled.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber canceled:" + e.toString());
    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

transcriber.sessionStopped.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber stopped:" + e.toString());

    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

// start the transcription.
Future<?> future = transcriber.startTranscribingAsync();
...
```

Если вы хотите в режиме реального времени _плюс_ асинхронные, комментировать и не комментировать соответствующие строки кода следующим образом:

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Получить результаты транскрипции

Этот шаг получает асинхронные результаты транскрипции, но предполагает, что любая обработка в режиме реального времени, которая может потребоваться, выполняется в другом месте. Для получения дополнительной информации, [см Транскриб разговоры в режиме реального времени с речью SDK](how-to-use-conversation-transcription-service.md).

Для кода, показанного здесь, вам нужна **версия удаленного разговора 1.8.0,** поддерживаемая только для Java (1.8.0 или выше) на Windows, Linux и Android (уровень API 26 или выше).

### <a name="obtaining-the-client-sdk"></a>Получение клиента SDK

Вы можете получить **удаленный разговор,** редактируя файл pom.xml следующим образом.

1. В конце файла, перед тегом `</project>` `repositories` закрытия, создайте элемент со ссылкой на репозиторий Maven для речи SDK:

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. Также добавьте `dependencies` элемент, с удаленным разговором-клиентом-sdk 1.8.0 в качестве зависимости:

   ```xml
   <dependencies>
     <dependency>
       <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
       <artifactId>remote-conversation</artifactId>
       <version>1.8.0</version>
     </dependency>
   </dependencies>
   ```

3. Сохраните изменения

### <a name="sample-transcription-code"></a>Пример кода транскрипции

После того, `conversationId`как у вас есть , создать удаленный разговор транскрипции клиента **RemoteConversationTranscriptionКлиент** в клиентском приложении для запроса статуса асинхронной транскрипции. Используйте метод **getTranscriptionOperation** в **RemoteConversationTranscriptionClient,** чтобы получить объект [PollerFlux.](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java) Объект PollerFlux будет иметь информацию о состоянии удаленной операции **RemoteConversationTranscriptionOperation** и конечный результат **RemoteConversationTranscriptionResult.** После завершения операции получите **RemoteConversationTranscriptionResult,** позвонив **в GetFinalResult** на [SyncPoller.](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java) В этом коде мы просто печатаем содержимое результата на вывод системы.

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Get the PollerFlux for the remote operation
PollerFlux<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> remoteTranscriptionOperation = client.getTranscriptionOperation(conversationId);

// Subscribe to PollerFlux to get the remote operation status
remoteTranscriptionOperation.subscribe(
        pollResponse -> {
            System.out.println("Poll response status : " + pollResponse.getStatus());
            System.out.println("Poll response status : " + pollResponse.getValue().getServiceStatus());
        }
);

// Obtain the blocking operation using getSyncPoller
SyncPoller<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> blockingOperation =  remoteTranscriptionOperation.getSyncPoller();

// Wait for the operation to finish
blockingOperation.waitForCompletion();

// Get the final result response
RemoteConversationTranscriptionResult resultResponse = blockingOperation.getFinalResult();

// Print the result
if(resultResponse != null) {
    if(resultResponse.getConversationTranscriptionResults() != null) {
        for (int i = 0; i < resultResponse.getConversationTranscriptionResults().size(); i++) {
            ConversationTranscriptionResult result = resultResponse.getConversationTranscriptionResults().get(i);
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult.name()));
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult));
            System.out.println(result.getOffset());
            System.out.println(result.getDuration());
            System.out.println(result.getUserId());
            System.out.println(result.getReason());
            System.out.println(result.getResultId());
            System.out.println(result.getText());
            System.out.println(result.toString());
        }
    }
}

System.out.println("Operation finished");
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Примеры на сайте GitHub](https://aka.ms/csspeech/samples)
