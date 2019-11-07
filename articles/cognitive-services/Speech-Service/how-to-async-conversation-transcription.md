---
title: Асинхронная транскрипция диалога (Предварительная версия) — служба речи
titleSuffix: Azure Cognitive Services
description: Узнайте, как использовать асинхронную транскрипцию беседы с помощью службы распознавания речи. Доступно только для Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: 9a2a00a8c76c7b5c02cde623bf93f536b27cf074
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621951"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Асинхронная транскрипция диалога (Предварительная версия)

В этой статье функция записи асинхронного диалога демонстрируется с помощью API **ремотеконверсатионтранскриптионклиент** . Если вы настроили запись разговора для асинхронного транскрипции и имеете `conversationId`, можно получить описание, связанное с этим `conversationId` помощью API **ремотеконверсатионтранскриптионклиент** .

## <a name="asynchronous-vs-real-time--asynchronous"></a>Асинхронное и в режиме реального времени + асинхронное

С помощью асинхронной записи сообщений потоковая передача передается в режиме реального времени, но не требуется. Вместо этого после отправки звука используйте `conversationId` `Conversation`, чтобы запросить состояние асинхронной транскрипции. Когда асинхронная транскрипция готова, вы получите `RemoteConversationTranscriptionResult`.

В режиме реального времени и асинхронных событий вы получаете запись в режиме реального времени, но также можете получить транскрипцию, выполнив запрос к `conversationId` (аналогично асинхронному сценарию).

Для выполнения асинхронной записи требуются два действия. Первым шагом является Отправка звукового сигнала, выбор асинхронного режима только для асинхронного выполнения или в режиме реального времени. Второй шаг — получение результатов транскрипции.

## <a name="upload-the-audio"></a>Отправка звука

Прежде чем можно будет выполнить асинхронную транскрипцию, необходимо отправить службу транскрипции, используя пакет SDK для клиента Microsoft для распознавания речи (версия 1.8.0 или выше).

В этом примере кода показано, как создать диалог транскрибер для асинхронного режима. Чтобы потоковая передача аудио в транскрибер, необходимо добавить код потоковой передачи, полученный из [транскрипция диалогов в режиме реального времени с помощью речевого пакета SDK](how-to-use-conversation-transcription-service.md). Сведения о поддерживаемых интерфейсах API для платформ и языков см. в разделе **ограничения** этой статьи.

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speechConfig.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// pick a conversation Id that is a GUID.
String conversationId = UUID.randomUUID().toString();

// Create a Conversation
Conversation conversation = new Conversation(speechConfig, conversationId);

// Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
// Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16Khz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16Khz, 16 bits and 8 channel pcm wav file
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

Если вы хотите _в режиме реального времени использовать_ асинхронные, закомментируйте и раскомментируйте соответствующие строки кода следующим образом:

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Получение результатов транскрипции

Этот шаг возвращает результаты асинхронного разговора, но предполагает, что обработка в режиме реального времени может быть выполнена в других местах. Дополнительные сведения см. в разделе [транскрипцияные беседы в режиме реального времени с помощью речевого пакета SDK](how-to-use-conversation-transcription-service.md).

Для кода, показанного здесь, требуется **версия Remote-CONVERSATION 1.8.0**, поддерживаемая только для Java (1.8.0 или выше) в Windows, Linux и Android (только на уровне API 26 или выше).

### <a name="obtaining-the-client-sdk"></a>Получение пакета SDK для клиента

**Удаленный сеанс** можно получить, отредактировав файл POM. XML следующим образом.

1. В конце файла перед закрывающим тегом `</project>`создайте элемент `repositories` со ссылкой на репозиторий Maven для пакета SDK для распознавания речи:

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. Также добавьте элемент `dependencies` с ремотеконверсатион-Client-SDK 1.8.0 в качестве зависимости:

   ```xml
   <dependencies>
     <dependency>
       <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
       <artifactId>remote-conversation</artifactId>
       <version>1.8.0</version>
     </dependency>
   </dependencies>
   ```

3. Сохранить изменения

### <a name="sample-transcription-code"></a>Пример кода транскрипции

После создания `conversationId`Создайте клиентский **ремотеконверсатионтранскриптионклиент** для записи удаленного диалога в клиентском приложении, чтобы запросить состояние асинхронной транскрипции. Чтобы получить объект [поллерфлукс](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java) , используйте метод **жеттранскриптионоператион** в **ремотеконверсатионтранскриптионклиент** . Объект Поллерфлукс будет содержать сведения о состоянии удаленной операции **ремотеконверсатионтранскриптионоператион** и конечный результат **ремотеконверсатионтранскриптионресулт**. После завершения операции получите **ремотеконверсатионтранскриптионресулт** , вызвав **жетфиналресулт** в [синкполлер](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java). В этом коде мы просто печатаем содержимое результатов в системный вывод.

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
