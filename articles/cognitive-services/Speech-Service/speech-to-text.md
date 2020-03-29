---
title: Речевой текст - Речевой сервис
titleSuffix: Azure Cognitive Services
description: Функция «речи к тексту» позволяет в режиме реального времени транскрипции аудиопотоков в текст. Ваши приложения, инструменты или устройства могут потреблять, отображать и принимать меры по ввоза этого текста. Эта служба работает бесшовно с текстом к речи (синтез речи), и функции перевода речи.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: dapine
ms.openlocfilehash: 2854f4e8d91164c8ae1f35761d6f605cae725245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052634"
---
# <a name="what-is-speech-to-text"></a>Что такое преобразование речи в текст?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Речевой текст из службы речевой речи, также известный как распознавание речи, позволяет в режиме реального времени транскрипции аудио потоков в текст. Ваши приложения, инструменты или устройства могут потреблять, отображать и принимать меры по этому тексту в качестве ввода команд. Эта услуга работает на той же технологии распознавания, что и Microsoft для продуктов Cortana и Office. Он беспрепятственно работает с <a href="./speech-translation.md" target="_blank">предложениями услуг перевода <span class="docon docon-navigate-external x-hidden-focus"></span> </a> и от текста к <a href="./text-to-speech.md" target="_blank">речи. <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Полный список доступных языков от речи [supported languages](language-support.md#speech-to-text)к тексту см.

Служба от речи к тексту по умолчанию использует модель универсального языка. Эта модель была обучена с использованием данных, принадлежащих корпорации Майкрософт, и развернута в облаке. Он оптимальный для разговорных и диктовки сценариев. При использовании речи к тексту для распознавания и транскрипции в уникальной среде, вы можете создавать и обучать пользовательские акустические, язык, и произношение моделей. Настройка полезна для решения проблемы окружающего шума или словарного запаса в отрасли.

> [!NOTE]
> Bing Speech был списан 15 октября 2019 года. Если приложения, инструменты или продукты используют AI-приложения Bing Speech, мы создали руководства, которые помогут вам перейти на службу speech.
> - [Миграция из Bing Speech в речевую службу](how-to-migrate-from-bing-speech.md)

## <a name="get-started-with-speech-to-text"></a>Начало работы с речи к тексту

Служба речевого текста доступна через [Speech SDK](speech-sdk.md). Есть несколько общих сценариев, доступных как quickstarts, на различных языках и платформах:

 - [Быстрый запуск: Распознать речь с ввоза микрофона](quickstarts/speech-to-text-from-microphone.md)
 - [Быстрый запуск: Распознать речь из файла](quickstarts/speech-to-text-from-file.md)
 - [Быстрый запуск: Распознать речь, хранящуюся в хранилище капли](quickstarts/from-blob.md)

Если вы предпочитаете использовать услугу REST от [REST APIs](rest-speech-to-text.md)речи к тексту, см.

## <a name="tutorials-and-sample-code"></a>Учебники и пример кода

После того как у вас будет возможность использовать службы "Речь", просмотрите наше руководство, которое научит вас распознавать намерения из речи с помощью пакета SDK для распознавания речи и LUIS.

- [Учебник: Распознать намерения от речи с речью SDK и LUIS, используя C #](how-to-recognize-intents-from-speech-csharp.md)

Пример кода для речи SDK доступен на GitHub. В этих примерах рассматриваются сценарии общего характера, такие как чтение аудио из файла или потока, непрерывное и одиночное распознавание, а также работа с пользовательскими моделями.

- [Образцы речи к тексту (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Примеры транскрибирования с помощью пакетной службы (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Настройка

В дополнение к стандартной модели службы речи, вы можете создавать пользовательские модели. Настройка помогает преодолеть барьеры распознавания речи, такие как стиль речи, словарный запас и фоновый шум, [см.](how-to-custom-speech.md) Параметры настройки варьируются в зависимости от языка/локализации, см. [поддерживаемые языки](supported-languages.md) для проверки поддержки.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Дальнейшие действия

- [Try the Speech service for free](get-started.md) (Бесплатное использование службы "Речь")
- [Получение пакета SDK для службы "Речь"](speech-sdk.md)
