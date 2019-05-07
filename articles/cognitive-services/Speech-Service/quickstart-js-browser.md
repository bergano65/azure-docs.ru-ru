---
title: Краткое руководство. Распознавание речи с использованием JavaScript в браузере и службы "Речь"
titleSuffix: Azure Cognitive Services
description: Узнайте, как распознавать речь в браузере, используя JavaScript и пакет SDK службы "Речь"
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: fmegen
ms.openlocfilehash: bda38825cd65f1d44b497de1445ead5689428917
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020727"
---
# <a name="quickstart-recognize-speech-in-javascript-in-a-browser-using-the-speech-sdk"></a>Краткое руководство. Распознавание речи в браузере с помощью JavaScript и пакета SDK службы "Речь"

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Из этой статьи вы узнаете, как создать веб-сайт, использующий привязку JavaScript из пакета SDK для службы "Речь" в Cognitive Services, который будет преобразовывать речь в текст.
Приложение основано на пакете SDK службы "Речь" для JavaScript ([скачать версию 1.5.0](https://aka.ms/csspeech/jsbrowserpackage)).

## <a name="prerequisites"></a>Предварительные требования

* Ключ подписки для службы распознавания речи. См. статью [Бесплатная пробная подписка на службу "Речь"](get-started.md).
* Компьютер Windows или Mac с рабочим микрофоном.
* Текстовый редактор.
* Текущая версия Chrome, Microsoft Edge или Safari.
* Веб-сервер, поддерживающий размещение сценариев PHP (при необходимости).

## <a name="create-a-new-website-folder"></a>Создание папки веб-сайта

Создайте пустую папку. В случае, если вы хотите разместить пример на веб-сервере, убедитесь, что у этого веб-сервера есть доступ к созданной папке.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Распаковка пакета SDK для службы "Речь" для JavaScript в созданную папку

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Скачайте пакет SDK для службы "Речь" как [ZIP-файл](https://aka.ms/csspeech/jsbrowserpackage) и распакуйте его в созданную папку. В результате должны распаковаться два файла: `microsoft.cognitiveservices.speech.sdk.bundle.js` и `microsoft.cognitiveservices.speech.sdk.bundle.js.map`.
Второй файл является необязательным. Он используются для отладки кода пакета SDK.

## <a name="create-an-indexhtml-page"></a>Создание страницы index.html

Создайте в папке файл `index.html` и откройте его в текстовом редакторе.

1. Скопируйте следующий фрагмент кода HTML:

   ```html
   <html>
   <head>
      <title>Speech SDK JavaScript Quickstart</title>
   </head>
   <body>
    <!-- UI code goes here -->

    <!-- SDK reference goes here -->

    <!-- Optional authorization token request goes here -->

    <!-- Sample code goes here -->
   </body>
   </html>
   ```

1. Добавьте приведенный ниже код пользовательского интерфейса в файл под первым комментарием.

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#uidiv)]

1. Добавление ссылки на пакет SDK

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#speechsdkref)]

1. Подключите обработчики для кнопки распознавания речи, полей результатов распознавания и подписки, определенных в коде пользовательского интерфейса.

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#quickstartcode)]

## <a name="create-the-token-source-optional"></a>Создание источника маркера (необязательно)

В случае, если вы хотите разместить веб-страницу на веб-сервере, можно указать источник маркера для вашего демоверсии приложения.
Таким образом ключ подписки никогда не покинет пределы сервера, позволяя при этом пользователям использовать возможности распознавания речи, не вводя код авторизации.

1. Создайте файл с именем `token.php`. В этом примере предполагается, что веб-сервер поддерживает язык сценариев PHP. Введите приведенный ниже код.

   [!code-php[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/token.php)]

1. Измените файл `index.html`, добавив в него следующий код.

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#authorizationfunction)]

> [!NOTE]
> Маркеры авторизации имеют ограниченное время существования.
> Этом упрощенном примере не показано, как автоматически обновлять маркеры проверки подлинности. Пользователь может вручную перезагрузить страницу или нажать клавишу F5, чтобы обновить ее.

## <a name="build-and-run-the-sample-locally"></a>Сборка и запуск примера в локальной среде

Чтобы запустить приложение, дважды щелкните файл index.html или откройте файл index.html с помощью веб-браузера на свой выбор. Отобразится простой графический пользовательский интерфейс, в котором можно ввести ключ подписки и [регион](regions.md) и активировать распознавание речи с помощью микрофона.

> [!NOTE]
> Этот метод не работает в браузере Safari.
> Пример веб-страницы в Safari должен быть размещен на веб-сервере. В Safari загруженные из локального файла веб-сайты не могут использовать микрофон.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Сборка и запуск примера на веб-сервере

Чтобы запустить приложение, откройте веб-браузер на свой выбор и введите в нем общедоступный URL-адрес, по которому размещена ваша папка. Затем введите свой [регион](regions.md) и активируйте распознавание речи с помощью микрофона. Приложение получит маркер из источника маркера, если он настроен.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры для JavaScript на сайте GitHub](https://aka.ms/csspeech/samples)
