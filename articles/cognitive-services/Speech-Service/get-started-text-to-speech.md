---
title: Краткое руководство по преобразованию текста в речь. Служба "Речь"
titleSuffix: Azure Cognitive Services
description: Узнайте, как использовать пакет SDK службы "Речь" для преобразования текста в речь. Из этого краткого руководства вы узнаете о создании объектов и конструктивных шаблонах, поддерживаемых форматах выходного аудиопотока, CLI службы "Речь" и пользовательских параметрах конфигурации для синтеза речи.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/01/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: speech-full-stack
keywords: Преобразование текста в речь
ms.openlocfilehash: 79409f95d698e015d15d9131dcf1f27b34b03343
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400924"
---
# <a name="get-started-with-text-to-speech"></a>Начало работы с преобразованием текста в речь

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
