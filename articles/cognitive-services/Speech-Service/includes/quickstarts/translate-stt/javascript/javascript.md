---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: ad3e005f65b1660bd843bf2819f41eddbed44e9c
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86035461"
---
## <a name="prerequisites"></a>Предварительные требования

Необходимые условия:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Создайте ресурс службы "Речь" в Azure <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
> * [Настройте среду разработки и создайте пустой проект](../../../../quickstarts/setup-platform.md).

## <a name="create-a-new-website-folder"></a>Создание папки веб-сайта

Создайте пустую папку. В случае, если вы хотите разместить пример на веб-сервере, убедитесь, что у этого веб-сервера есть доступ к созданной папке.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Распаковка пакета SDK для службы "Речь" для JavaScript в созданную папку

Скачайте пакет SDK для службы "Речь" как [ZIP-файл](https://aka.ms/csspeech/jsbrowserpackage) и распакуйте его в созданную папку. В результате должны распаковаться два файла: `microsoft.cognitiveservices.speech.sdk.bundle.js` и `microsoft.cognitiveservices.speech.sdk.bundle.js.map`.
Второй файл является необязательным. Он используются для отладки кода пакета SDK.

## <a name="create-an-indexhtml-page"></a>Создание страницы index.html

Создайте в папке файл `index.html` и откройте его в текстовом редакторе.

1. Скопируйте следующий фрагмент кода HTML:

 [!code-html [SampleCode](~/samples-cognitive-services-speech-sdk/quickstart/javascript/browser/index-translate-stt.html)]

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

Чтобы запустить приложение, дважды щелкните файл index.html или откройте файл index.html с помощью веб-браузера на свой выбор. Отобразится простой графический пользовательский интерфейс, в котором можно ввести ключ подписки, указать [регион](../../../../regions.md) и активировать транскрибирование текста из речевого ввода.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Сборка и запуск примера на веб-сервере

Чтобы запустить приложение, откройте используемый вами веб-браузер и введите общедоступный URL-адрес, по которому размещена ваша папка. Затем укажите [регион](../../../../regions.md) и активируйте транскрибирование текста из речевого ввода. Приложение получит маркер из источника маркера, если он настроен.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [footer](./footer.md)]