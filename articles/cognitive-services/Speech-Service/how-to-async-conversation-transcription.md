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
ms.openlocfilehash: 433ea2778d99de39991565b2529e2f8eab4e13df
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506981"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Асинхронная транскрипция диалога (Предварительная версия)

В этой статье функция записи асинхронного диалога демонстрируется с помощью API **ремотеконверсатионтранскриптионклиент** . Если вы настроили запись разговора для асинхронного транскрипции и имеете `conversationId`, можно получить описание, связанное с этим `conversationId` помощью API **ремотеконверсатионтранскриптионклиент** .

## <a name="asynchronous-vs-real-time--asynchronous"></a>Асинхронное и в режиме реального времени + асинхронное

С помощью асинхронной записи сообщений потоковая передача передается в режиме реального времени, но не требуется. Вместо этого после отправки звука используйте `conversationId` `ConversationTranscriber`, чтобы запросить состояние асинхронной транскрипции. Когда асинхронная транскрипция готова, вы получите `RemoteConversationTranscriptionResult`.

В режиме реального времени и асинхронных событий вы получаете запись в режиме реального времени, но также можете получить транскрипцию, выполнив запрос к `conversationId` (аналогично асинхронному сценарию).

Для выполнения асинхронной записи требуются два действия. Первым шагом является Отправка звукового сигнала, выбор асинхронного режима только для асинхронного выполнения или в режиме реального времени. Второй шаг — получение результатов транскрипции.

## <a name="upload-the-audio"></a>Отправка звука

Прежде чем можно будет выполнить асинхронную расшифровку, необходимо отправить звуковой сообщение в речь о пакете SDK для распознавания речи Майкрософт (версия 1.8.0 или более поздняя).

В этом примере кода показано, как создать диалог транскрибер для асинхронного режима. Чтобы потоковая передача аудио в транскрибер, необходимо добавить код потоковой передачи, полученный из [транскрипция диалогов в режиме реального времени с помощью речевого пакета SDK](how-to-use-conversation-transcription-service.md). Сведения о поддерживаемых интерфейсах API для платформ и языков см. в разделе **ограничения** этой статьи.

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speech_config.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// Do rest of the things as explained in how to use Conversation Transcription

// pick a conversation Id that is a GUID.
Conversation conversation = new Conversation(speechConfig, conversationId);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromDefaultMicrophoneInput());

// join a conversation
transcriber.joinConversationAsync(conversation).get();

// stream audio as shown in “Transcribe conversations in real time”
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

Для кода, показанного здесь, требуется **ремотеконверсатион-Client-SDK версии 1.0.0**, который поддерживается только для Java (1,8 или более поздних версий) в Windows, Linux и Android (уровень API 26 или выше).

### <a name="obtaining-the-client-sdk"></a>Получение пакета SDK для клиента

Вы можете получить **ремотеконверсатион-Client-SDK** , отредактировав файл POM. XML следующим образом.

- В конце файла перед закрывающим тегом `</project>`создайте элемент `repositories` со ссылкой на репозиторий Maven для пакета SDK для распознавания речи:

  ```xml
  <repositories>
    <repository>
      <id>maven-cognitiveservices-speech</id>
      <name>Microsoft Cognitive Services Speech Maven Repository</name>
      <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
    </repository>
  </repositories>
  ```

- Также добавьте элемент `dependencies` с ремотеконверсатион-Client-SDK 1.0.0 в качестве зависимости:

  ```xml
  <dependencies>
    <dependency>
      <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
      <artifactId>remoteconversation-client-sdk</artifactId>
      <version>1.0.0</version>
    </dependency>
  </dependencies>
  ```

- Сохранить изменения

### <a name="sample-transcription-code"></a>Пример кода транскрипции

После создания `conversationId`создайте объект удаленной операции **ремотеконверсатионтранскриптионоператион** на клиенте, чтобы запросить состояние асинхронной транскрипции. **Ремотеконверсатионтранскриптионоператион** расширена из модуля [опроса](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/Poller.java). После завершения опроса получите **ремотеконверсатионтранскриптионресулт** , подписавшись на него и запрашивая объект. В этом коде мы просто печатаем содержимое результатов в системный вывод.

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Create a remote Conversation Transcription operation
RemoteConversationTranscriptionOperation operation = new RemoteConversationTranscriptionOperation(conversationId, client);

// Operation identifier now be the value of `conversationId`
System.out.println("Operation Identifier:" + operation.getId());

// Get the observer (which is a Flux) and subscribe to it for the response
operation.getObserver()
                .subscribe(
                        pollResponse -> {
                            System.out.println("Poll response status : " + pollResponse.getStatus());
                            if(pollResponse.getValue().getConversationTranscriptionResults() != null) {
                                for (int i = 0; i < pollResponse.getValue().getConversationTranscriptionResults().size(); i++) {
                                    ConversationTranscriptionResult result = pollResponse.getValue().getConversationTranscriptionResults().get(i);
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
                );
// Block on the operation till it is finished
operation.block();

System.out.println("Operation finished");
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Примеры на сайте GitHub](https://aka.ms/csspeech/samples)
