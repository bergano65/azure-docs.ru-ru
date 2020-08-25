---
title: Краткое руководство. Синтез речи с помощью службы "Речь"
titleSuffix: Azure Cognitive Services
description: Узнайте, как использовать пакет SDK службы "Речь" для преобразования текста в речь. Из этого краткого руководства вы узнаете о создании объектов и конструктивных шаблонах, поддерживаемых форматах выходного аудиопотока, CLI службы "Речь" и пользовательских параметрах конфигурации для синтеза речи.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/11/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-javascript
zone_pivot_groups: speech-full-stack
ms.openlocfilehash: e66da95bea0b03ddad1096b2b19751f73aaacbe0
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88170168"
---
# <a name="get-started-with-speech-synthesis"></a>Начало работы со службой синтеза речи

Из этого краткого руководства вы узнаете, как работать с распространенными конструктивными шаблонами для синтеза текста в речь, используя пакет SDK службы "Речь". Вы начнете с основных настроек и синтеза, а затем перейдете к более сложным примерам для разработки пользовательских приложений, в том числе к таким задачам:

* получение ответов в виде потоков в памяти;
* настройка частоты выборки и скорости передачи выходных данных;
* отправка запросов синтеза с помощью SSML (язык разметки синтеза речи);
* использование нейронных голосовых моделей.

> [!TIP]
> Если вы хотите перейти к примерам кода, см. [примеры для краткого руководства](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) на сайте GitHub.

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-javascript.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-spx"
[!INCLUDE [CLI include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cli.md)]
::: zone-end

## <a name="next-steps"></a>Дальнейшие действия

* [Начало работы с набором средств "Пользовательский голос"](how-to-custom-voice.md)
* [Улучшение синтеза с помощью SSML](speech-synthesis-markup.md)
* Узнайте, как использовать [API длинных аудиоматериалов](long-audio-api.md) для примеров текста большого размера, например для книг или новостных статей.
* См. [примеры для краткого руководства](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) на сайте GitHub.
