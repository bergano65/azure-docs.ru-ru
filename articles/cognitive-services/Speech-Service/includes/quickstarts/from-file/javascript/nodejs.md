---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 66422657aefa5bca2e7f20852a19faca63db5a15
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85838998"
---
## <a name="prerequisites"></a>Предварительные требования

Необходимые условия:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Создайте ресурс службы "Речь" в Azure <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
> * [Настройте среду разработки и создайте пустой проект](../../../../quickstarts/setup-platform.md).

## <a name="start-with-some-boilerplate-code"></a>Добавление стандартного кода

Добавим код, который выступает в качестве основы для нашего проекта. Создайте файл index.js и добавьте этот код.

Не забудьте ввести значения для `subscriptionKey`, `servcieRegion`, а также `filename`.

```JavaScript
(function() {
  // <code>
  "use strict";
  
  // pull in the required packages.
  var sdk = require("microsoft-cognitiveservices-speech-sdk");
  var fs = require("fs");
  
  // replace with your own subscription key,
  // service region (e.g., "westus"), and
  // the name of the file you want to run
  // through the speech recognizer.
  var subscriptionKey = "YourSubscriptionKey";
  var serviceRegion = "YourServiceRegion"; // e.g., "westus"
  var filename = "YourAudioFile.wav"; // 16000 Hz, Mono
 
}());
  
```
## <a name="load-the-file-into-an-pushaudioinputstream"></a>Загрузка файла в PushAudioInputStream

Для NodeJS пакет SDK для службы "Речь" изначально не поддерживает доступ к файлам напрямую, поэтому мы загрузим файл и поместим его в `PushAudioInputStream`. Этот подход будет полезен для файлов меньшего размера, однако для более крупных файлов следует применить `PullAudioInputStream`, чтобы более эффективно использовать память.

```JavaScript
// create the push stream we need for the speech sdk.
  var pushStream = sdk.AudioInputStream.createPushStream();
  
  // open the file and push it to the push stream.
  fs.createReadStream(filename).on('data', function(arrayBuffer) {
    pushStream.write(arrayBuffer.slice());
  }).on('end', function() {
    pushStream.close();
  });
```

## <a name="create-a-speech-configuration"></a>Создание конфигурации службы "Речь"

Перед инициализацией объекта `SpeechRecognizer` необходимо создать конфигурацию, использующую ключ и регион подписки. Вставьте этот код.

> [!NOTE]
> Пакет SDK для распознавания речи по умолчанию распознает использование языкового стандарта en-us. Дополнительные сведения о выборе исходного языка см. в разделе [Specify source language for speech to text](../../../../how-to-specify-source-language.md) (Указание исходного языка для преобразования речи в текст).

 ```JavaScript
   // now create the audio-config pointing to our stream and
  // the speech config specifying the language.
  var speechConfig = sdk.SpeechConfig.fromSubscription(subscriptionKey, serviceRegion);
  
  // setting the recognition language to English.
  speechConfig.speechRecognitionLanguage = "en-US";
  
```
## <a name="create-an-audio-configuration"></a>Создание конфигурации звука

Теперь необходимо создать объект `AudioConfig`, указывающий на `PushAudioInputStream`. Вставьте этот код непосредственно под конфигурацией службы "Речь".

```JavaScript
    var audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
```

## <a name="initialize-a-speechrecognizer"></a>Инициализация SpeechRecognizer

Теперь создадим объект `SpeechRecognizer` с помощью созданных ранее объектов `SpeechConfig` и `AudioConfig`.

```JavaScript
  // create the speech recognizer.
  var recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
  
```
## <a name="recognize-a-phrase-and-display-results"></a>Распознавание фразы и отображение результатов

В объекте `SpeechRecognizer` необходимо вызвать метод `recognizeOnceAsync()`. С помощью этого метода служба "Речь" узнает, что для распознавания отправляется одна фраза и что после идентификации фразы необходимо остановить распознавание речи.

Мы также отправим возвращенный текст, или любые ошибки, в консоль и, наконец, закроем распознаватель.
```JavaScript
 // we are done with the setup
  console.log("Now recognizing from: " + filename);
  
  // start the recognizer and wait for a result.
  recognizer.recognizeOnceAsync(
    function (result) {
      console.log(result);
  
      recognizer.close();
      recognizer = undefined;
    },
    function (err) {
      console.trace("err - " + err);
  
      recognizer.close();
      recognizer = undefined;
    });
```
## <a name="check-your-code"></a>Проверка кода
```JavaScript
(function() {
  "use strict";
  
  // pull in the required packages.
  var sdk = require("microsoft-cognitiveservices-speech-sdk");
  var fs = require("fs");
  
  // replace with your own subscription key,
  // service region (e.g., "westus"), and
  // the name of the file you want to run
  // through the speech recognizer.
  var subscriptionKey = "YourSubscriptionKey";
  var serviceRegion = "YourServiceRegion"; // e.g., "westus"
  var filename = "YourAudioFile.wav"; // 16000 Hz, Mono
  
  // create the push stream we need for the speech sdk.
  var pushStream = sdk.AudioInputStream.createPushStream();
  
  // open the file and push it to the push stream.
  fs.createReadStream(filename).on('data', function(arrayBuffer) {
    pushStream.write(arrayBuffer.slice());
  }).on('end', function() {
    pushStream.close();
  });
  
  // now create the audio-config pointing to our stream and
  // the speech config specifying the language.
  var speechConfig = sdk.SpeechConfig.fromSubscription(subscriptionKey, serviceRegion);
  
  // setting the recognition language to English.
  speechConfig.speechRecognitionLanguage = "en-US";
  
  var audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
  
  // create the speech recognizer.
  var recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
  
  // we are done with the setup
  console.log("Now recognizing from: " + filename);
  
  // start the recognizer and wait for a result.
  recognizer.recognizeOnceAsync(
    function (result) {
      console.log(result);
  
      recognizer.close();
      recognizer = undefined;
    },
    function (err) {
      console.trace("err - " + err);
  
      recognizer.close();
      recognizer = undefined;
    });

}());
```
## <a name="run-the-sample-locally"></a>Локальный запуск примера

Выполнение кода с помощью Node.js
```bash
node index.js
```

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
