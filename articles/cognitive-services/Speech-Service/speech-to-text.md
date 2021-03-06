---
title: Служба распознавания речи в текст
titleSuffix: Azure Cognitive Services
description: Функция преобразования речи в текст позволяет подделать звуковые потоки в текстовом режиме в реальном времени. Приложения, средства и устройства могут использовать, отображать и предпринимать действия над этим текстовым входом. Эта служба легко работает с функциями преобразования текста в речь (речи) и перевода речи.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: erhopf
ms.openlocfilehash: 8518f92a4f4df1686d4b338783a93d969e04d219
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75379965"
---
# <a name="what-is-speech-to-text"></a>Что такое преобразование речи в текст?

Преобразование речи в текст из речевой службы, известной также как распознавание речи, позволяет подсчитать звуковые потоки в режиме реального времени в текст. Приложения, средства и устройства могут использовать, отображать и предпринимать действия над этим текстом в качестве входных данных команды. Эта служба работает с помощью той же технологии распознавания, которую корпорация Майкрософт использует для Кортаны и продуктов Office. Он легко работает с предложениями <a href="./speech-translation.md" target="_blank">службы <span class="docon docon-navigate-external x-hidden-focus"></span> перевода и преобразования</a> <a href="./text-to-speech.md" target="_blank">текста в речь <span class="docon docon-navigate-external x-hidden-focus"></span> </a> . Полный список доступных языков для перевода речи в текст см. в разделе [Поддерживаемые языки](language-support.md#speech-to-text).

В службе преобразования речи в текст по умолчанию используется универсальная языковая модель. Эта модель была обучена с использованием данных, принадлежащих корпорации Майкрософт, и развертывается в облаке. Это оптимальный вариант для сценариев взаимодействия и диктовки. При использовании преобразования речи в текст для распознавания и транскрипции в уникальной среде можно создавать и обучать пользовательские модели акустических, языковых и произношения. Настройка полезна для адресации окружающих шума или отраслевых словарей.

> [!NOTE]
> Распознавание речи Bing списано 15 октября 2019 г. Если приложения, средства или продукты используют Распознавание речи Bing API или Пользовательское распознавание речи, мы создали руководства, которые помогут вам перейти на службу распознавания речи.
> - [Миграция с Распознавание речи Bing в службу речи](how-to-migrate-from-bing-speech.md)
> - [Миграция с Пользовательское распознавание речи в службу речи](how-to-migrate-from-custom-speech-service.md)

## <a name="get-started-with-speech-to-text"></a>Начало работы с речью в текст

Служба преобразования речи в текст доступна через [речевой пакет SDK](speech-sdk.md). Существует несколько распространенных сценариев, доступных в качестве кратких руководств на различных языках и платформах:

 - [Краткое руководство. Распознавание речи при вводе с помощью микрофона](quickstarts/speech-to-text-from-microphone.md)
 - [Краткое руководство. Распознавание речи из файла](quickstarts/speech-to-text-from-file.md)
 - [Краткое руководство. Распознавание речи, хранящихся в хранилище BLOB-объектов](quickstarts/from-blob.md)

Если вы предпочитаете использовать службу текста для преобразования речи в текст, ознакомьтесь с разрядом с [интерфейсами API для интерфейса RESTful](rest-speech-to-text.md).

## <a name="tutorials-and-sample-code"></a>Учебники и примеры кода

После того как у вас будет возможность использовать службы "Речь", просмотрите наше руководство, которое научит вас распознавать намерения из речи с помощью пакета SDK для распознавания речи и LUIS.

- [Учебник. распознавание целей речи с помощью речевого пакета SDK и LUIS с помощьюC#](how-to-recognize-intents-from-speech-csharp.md)

Пример кода для пакета SDK для распознавания речи доступен на сайте GitHub. В этих примерах рассматриваются сценарии общего характера, такие как чтение аудио из файла или потока, непрерывное и одиночное распознавание, а также работа с пользовательскими моделями.

- [Примеры преобразования речи в текст (пакет SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Примеры транскрибирования с помощью Пакетной службы (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Настройка

Помимо стандартной модели речевой службы, можно создавать пользовательские модели. Настройка помогает преодолеть барьеры распознавания речи, такие как стиль речи, словарь и фоновый шум, см. [пользовательское распознавание речи](how-to-custom-speech.md). Параметры настройки зависят от языка и языкового стандарта. см. раздел [Поддерживаемые языки](supported-languages.md) для проверки поддержки.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Дальнейшие действия

- [Try the Speech service for free](get-started.md) (Бесплатное использование службы "Речь")
- [Получение пакета SDK для распознавания речи](speech-sdk.md)
