---
title: Краткое руководство. Настройка платформы для пакета SDK службы "Речь" для JavaScript (браузер) — служба "Речь"
titleSuffix: Azure Cognitive Services
description: В этом руководстве показано, как настроить платформу для использования JavaScript (браузер) с пакетом SDK службы "Речь".
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/11/2019
ms.author: erhopf
ms.custom: devx-track-js
ms.openlocfilehash: 2701b4cd17a132de07c031166bbe4cb1086227e9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324784"
---
Здесь также описано, как установить [пакет SDK службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) для JavaScript для использования с веб-страницей.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="create-a-new-website-folder"></a>Создание папки веб-сайта

Создайте пустую папку. В случае, если вы хотите разместить пример на веб-сервере, убедитесь, что у этого веб-сервера есть доступ к созданной папке.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Распаковка пакета SDK для службы "Речь" для JavaScript в созданную папку

Скачайте пакет SDK для службы "Речь" как [ZIP-файл](https://aka.ms/csspeech/jsbrowserpackage) и распакуйте его в созданную папку. В результате будут распакованы пять файлов:
* `microsoft.cognitiveservices.speech.sdk.bundle.js` — доступная для чтения версия пакета SDK службы "Речь";
* `microsoft.cognitiveservices.speech.sdk.bundle.js.map` — файл сопоставления, используемый для отладки кода пакета SDK;
* `microsoft.cognitiveservices.speech.sdk.bundle.d.ts` — определения объектов для использования с TypeScript;
* `microsoft.cognitiveservices.speech.sdk.bundle-min.js` — сокращенная версия пакета SDK службы "Речь".
* `speech-processor.js` — код для повышения производительности в некоторых браузерах.

## <a name="create-an-indexhtml-page"></a>Создание страницы index.html

Создайте в папке файл `index.html` и откройте его в текстовом редакторе.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [windows](../quickstart-list.md)]