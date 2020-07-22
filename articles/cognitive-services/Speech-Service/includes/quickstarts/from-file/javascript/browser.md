---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 7e2960adce028450fd3ccdb9eb11190629bf7bb8
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86035574"
---
## <a name="prerequisites"></a>Предварительные требования

Необходимые условия:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Создайте ресурс службы "Речь" в Azure <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
> * [Настройте среду разработки и создайте пустой проект](../../../../quickstarts/setup-platform.md).

## <a name="start-with-some-boilerplate-code"></a>Добавление стандартного кода

Добавим код, который выступает в качестве основы для нашего проекта.

```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Microsoft Cognitive Services Speech SDK JavaScript Quickstart</title>
    <meta charset="utf-8" />
    </head>
    <body style="font-family:'Helvetica Neue',Helvetica,Arial,sans-serif; font-size:13px;">
    </body>
    </html>
```
## <a name="add-ui-elements"></a>Добавление элементов пользовательского интерфейса

Теперь мы добавим базовый пользовательский интерфейс для полей ввода, создадим ссылку на код JavaScript пакета SDK службы "Речь" и получим токен авторизации, если он доступен.

```html  
  <div id="content" style="display:none">
    <table width="100%">
      <tr>
        <td></td>
        <td><h1 style="font-weight:500;">Microsoft Cognitive Services Speech SDK JavaScript Quickstart</h1></td>
      </tr>
      <tr>
        <td align="right"><a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started" target="_blank">Subscription</a>:</td>
        <td><input id="subscriptionKey" type="text" size="40" value="subscription"></td>
      </tr>
      <tr>
        <td align="right">Region</td>
        <td><input id="serviceRegion" type="text" size="40" value="YourServiceRegion"></td>
      </tr>
      <tr>
        <td align="right">File</td>
        <td><input type="file" id="filePicker" accept=".wav" style="display:none" /></td>
      </tr>
      <tr>
        <td></td>
        <td><button id="startRecognizeOnceAsyncButton">Start recognition</button></td>
      </tr>
      <tr>
        <td align="right" valign="top">Results</td>
        <td><textarea id="phraseDiv" style="display: inline-block;width:500px;height:200px"></textarea></td>
      </tr>
    </table>
  </div>

  <script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>

   <script>
  // Note: Replace the URL with a valid endpoint to retrieve
  //       authorization tokens for your subscription.
  var authorizationEndpoint = "token.php";

  function RequestAuthorizationToken() {
    if (authorizationEndpoint) {
      var a = new XMLHttpRequest();
      a.open("GET", authorizationEndpoint);
      a.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
      a.send("");
      a.onload = function() {
          var token = JSON.parse(atob(this.responseText.split(".")[1]));
          serviceRegion.value = token.region;
          authorizationToken = this.responseText;
          subscriptionKey.disabled = true;
          subscriptionKey.value = "using authorization token (hit F5 to refresh)";
          console.log("Got an authorization token: " + token);
      }
    }
  }
  </script>
  
  <script>
    // status fields and start button in UI
    var phraseDiv;
    var startRecognizeOnceAsyncButton;

    // subscription key and region for speech services.
    var subscriptionKey, serviceRegion;
    var authorizationToken;
    var SpeechSDK;
    var recognizer;
    var filePicker;
    var audioFile;

    document.addEventListener("DOMContentLoaded", function () {
      startRecognizeOnceAsyncButton = document.getElementById("startRecognizeOnceAsyncButton");
      subscriptionKey = document.getElementById("subscriptionKey");
      serviceRegion = document.getElementById("serviceRegion");
      phraseDiv = document.getElementById("phraseDiv");
      filePicker = document.getElementById('filePicker');
      
      filePicker.addEventListener("change", function () {
                audioFile = filePicker.files[0];
            });

      startRecognizeOnceAsyncButton.addEventListener("click", function () {
        startRecognizeOnceAsyncButton.disabled = true;
        phraseDiv.innerHTML = "";

      });

      if (!!window.SpeechSDK) {
        SpeechSDK = window.SpeechSDK;
        startRecognizeOnceAsyncButton.disabled = false;

        document.getElementById('content').style.display = 'block';
        document.getElementById('warning').style.display = 'none';

        // in case we have a function for getting an authorization token, call it.
        if (typeof RequestAuthorizationToken === "function") {
            RequestAuthorizationToken();
        }
      }
    });
  </script>
```
 
## <a name="create-a-speech-configuration"></a>Создание конфигурации службы "Речь"

Перед инициализацией объекта `SpeechRecognizer` необходимо создать конфигурацию, использующую ключ и регион подписки. Вставьте код в метод `startRecognizeOnceAsyncButton.addEventListener()`.

> [!NOTE]
> Пакет SDK для распознавания речи по умолчанию распознает использование языкового стандарта en-us. Дополнительные сведения о выборе исходного языка см. в разделе [Specify source language for speech to text](../../../../how-to-specify-source-language.md) (Указание исходного языка для преобразования речи в текст).


```JavaScript
        // if we got an authorization token, use the token. Otherwise use the provided subscription key
        var speechConfig;
        if (authorizationToken) {
          speechConfig = SpeechSDK.SpeechConfig.fromAuthorizationToken(authorizationToken, serviceRegion.value);
        } else {
          if (subscriptionKey.value === "" || subscriptionKey.value === "subscription") {
            alert("Please enter your Microsoft Cognitive Services Speech subscription key!");
            return;
          }
          speechConfig = SpeechSDK.SpeechConfig.fromSubscription(subscriptionKey.value, serviceRegion.value);
        }

        speechConfig.speechRecognitionLanguage = "en-US";
```

## <a name="create-an-audio-configuration"></a>Создание конфигурации звука

Теперь необходимо создать объект `AudioConfig`, указывающий на звуковой файл. Вставьте этот код в метод `startRecognizeOnceAsyncButton.addEventListener()` непосредственно под конфигурацией службы "Речь".

```JavaScript
        var audioConfig  = SpeechSDK.AudioConfig.fromWavFileInput(audioFile);
```

## <a name="initialize-a-speechrecognizer"></a>Инициализация SpeechRecognizer

Теперь создадим объект `SpeechRecognizer` с помощью созданных ранее объектов `SpeechConfig` и `AudioConfig`. Вставьте код в метод `startRecognizeOnceAsyncButton.addEventListener()`.

```JavaScript
        recognizer = new SpeechSDK.SpeechRecognizer(speechConfig, audioConfig);
```

## <a name="recognize-a-phrase"></a>Распознавание фразы

В объекте `SpeechRecognizer` необходимо вызвать метод `recognizeOnceAsync()`. С помощью этого метода служба "Речь" узнает, что для распознавания отправляется одна фраза и что после идентификации фразы необходимо остановить распознавание речи.

```JavaScript
recognizer.recognizeOnceAsync(
          function (result) {
            startRecognizeOnceAsyncButton.disabled = false;
            phraseDiv.innerHTML += result.text;
            window.console.log(result);

            recognizer.close();
            recognizer = undefined;
          },
          function (err) {
            startRecognizeOnceAsyncButton.disabled = false;
            phraseDiv.innerHTML += err;
            window.console.log(err);

            recognizer.close();
            recognizer = undefined;
          });
```

## <a name="check-your-code"></a>Проверка кода

 [!code-html [SampleCode](~/samples-cognitive-services-speech-sdk/quickstart/javascript/browser/index-from-file.html)]

## <a name="create-the-token-source-optional"></a>Создание источника маркера (необязательно)

В случае, если вы хотите разместить веб-страницу на веб-сервере, можно указать источник маркера для вашего демоверсии приложения.
Таким образом ключ подписки никогда не покинет пределы сервера, позволяя при этом пользователям использовать возможности распознавания речи, не вводя код авторизации.

Создайте файл с именем `token.php`. В этом примере предполагается, что веб-сервер поддерживает язык сценариев PHP с включенной служебной программой cURL. Введите приведенный ниже код.

```php
<?php
header('Access-Control-Allow-Origin: ' . $_SERVER['SERVER_NAME']);

// Replace with your own subscription key and service region (e.g., "westus").
$subscriptionKey = 'YourSubscriptionKey';
$region = 'YourServiceRegion';

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, 'https://' . $region . '.api.cognitive.microsoft.com/sts/v1.0/issueToken');
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, '{}');
curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/json', 'Ocp-Apim-Subscription-Key: ' . $subscriptionKey));
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
echo curl_exec($ch);
?>
```

> [!NOTE]
> Маркеры авторизации имеют ограниченное время существования.
> Этом упрощенном примере не показано, как автоматически обновлять маркеры проверки подлинности. Пользователь может вручную перезагрузить страницу или нажать клавишу F5, чтобы обновить ее.

## <a name="build-and-run-the-sample-locally"></a>Сборка и запуск примера в локальной среде

Чтобы запустить приложение, дважды щелкните файл index.html или откройте файл index.html с помощью веб-браузера на свой выбор. Отобразится простой графический пользовательский интерфейс, в котором можно ввести ключ подписки и [регион](../../../../regions.md) и активировать распознавание речи с помощью микрофона.

> [!NOTE]
> Этот метод не работает в браузере Safari.
> Пример веб-страницы в Safari должен быть размещен на веб-сервере. В Safari загруженные из локального файла веб-сайты не могут использовать микрофон.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Сборка и запуск примера на веб-сервере

Чтобы запустить приложение, откройте веб-браузер на свой выбор и введите в нем общедоступный URL-адрес, по которому размещена ваша папка. Затем введите свой [регион](../../../../regions.md) и активируйте распознавание речи с помощью микрофона. Приложение получит маркер из источника маркера, если он настроен.


## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]