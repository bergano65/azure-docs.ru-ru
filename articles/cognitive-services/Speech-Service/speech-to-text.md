---
title: Речи в текст с помощью служб Azure речи
titleSuffix: Azure Cognitive Services
description: Речи в текст из служб речи Azure, также известный как речи в текст, позволяет в режиме реального времени транскрипцию аудиопотоков в текст, который может использовать приложения, средства или устройств, отображения и действий качестве входных данных команды. Эта служба основана на технологии распознавания, корпорация Майкрософт использует для продуктов Cortana и Office и без проблем работает с преобразования и преобразования текста в речь.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: afe69d396c42023df8fcf5e4a6772771afc75c76
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606254"
---
# <a name="what-is-speech-to-text"></a>Что такое речи в текст

Речи в текст из служб речи Azure, также известный как речи в текст, позволяет в режиме реального времени транскрипцию аудиопотоков в текст, который может использовать приложения, средства или устройств, отображения и действий качестве входных данных команды. Эта служба основана на технологии распознавания, корпорация Майкрософт использует для продуктов Cortana и Office и без проблем работает с преобразования и преобразования текста в речь.  Полный список доступных языков речи в текст, см. в разделе [поддерживаемые языки](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-to-text).

По умолчанию служба распознавания речи в текст использует универсальной языковую модель. Эта модель была обучена с использованием данных, принадлежащих корпорации Майкрософт и развертывается в облаке. Он является оптимальным выбором для общения и сценарии диктовки. Если вы используете преобразование речи в текст для распознавания и расшифровки в уникальной среде, вы можете создавать и обучать пользовательские акустические, языковые модели и модели произношения для учета окружающих помех или отраслевого словаря.

Можно легко запись звука с микрофона, прочитанных из потока или доступ к звуковые файлы из хранилища с помощью Speech SDK и REST API. Speech SDK поддерживает WAV/PCM 16-разрядная версия, кГц, 16 и 8 кГц, звук один канал для распознавания речи. Дополнительные форматы аудио поддерживаются с помощью [конечной точки REST речи в текст](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) или [пакетная служба транскрибирования](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

## <a name="core-features"></a>Основные функции

Ниже приведены возможности, доступные через Speech SDK и REST API:

| Вариант использования | SDK | REST |
|----------|-----|------|
| Транскрипция короткие фразы (< 15 секунд). Поддерживает только расшифровка дикторского текста для окончательного результата. | Да | Да |
| Непрерывная транскрипцию long фразы и потоковой передачи аудио (> 15 секунд). Поддерживает транскрипцию промежуточный и конечный результаты. | Да | Нет |
| Производными результаты распознавания с намерениями [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis). | Да | Нет\* |
| Асинхронно пакетного транскрипцию звуковые файлы. | Нет | Да\** |
| Создание и Управление моделями распознавания речи. | Нет | Да\** |
| Создание и настройка развертывания пользовательской модели. | Нет | Да\** |
| Создайте тесты точности для измерения точности модели базовых показателей и пользовательские модели. | Нет | Да\** |
| Управление подписками. | Нет | Да\** |

\* *Для получения сведений о намерениях и сущностях через LUIS потребуется отдельная подписка LUIS. С этой подпиской пакет SDK можно вызвать LUIS для вас и предоставить сущностей и намерений результаты. Вы можете самостоятельно вызывать LUIS с помощью REST API для получения сведений о намерениях и сущностях, используя подписку LUIS.*

\** *Эти службы доступны cris.ai конечной точки. См. в разделе [Swagger ссылку](https://westus.cris.ai/swagger/ui/index).*

## <a name="get-started-with-speech-to-text"></a>Начало работы с речи в текст

Мы предлагаем руководства на большинстве популярных языках программирования, каждый из которых позволяет получить рабочий код менее чем за 10 минут. Эта таблица содержит полный список Speech SDK кратких руководств, упорядоченных по языка.

| Краткое руководство | платформа | Справочник по API |
|------------|----------|---------------|
| [C#, .NET Core](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnetcore-windows) | Windows | [Обзор](https://aka.ms/csspeech/csharpref) |
| [C#, .NET framework](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnet-windows) | Windows | [Обзор](https://aka.ms/csspeech/csharpref) |
| [C#, УНИВЕРСАЛЬНОЙ ПЛАТФОРМЫ WINDOWS](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-uwp) | Windows | [Обзор](https://aka.ms/csspeech/csharpref) |
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-windows) | Windows | [Обзор](https://aka.ms/csspeech/cppref)|
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-linux) | Linux | [Обзор](https://aka.ms/csspeech/cppref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-android) | Android | [Обзор](https://aka.ms/csspeech/javaref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-jre) | Windows, Linux, macOS | [Обзор](https://aka.ms/csspeech/javaref) |
| [JavaScript в браузере](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-browser) | Веб-браузер, Windows, Linux, macOS | [Обзор](https://aka.ms/AA434tv) |
| [JavaScript, Node.js](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-node) | Windows, Linux, macOS | [Обзор](https://aka.ms/AA434tv) |
| [Objective-C](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-objectivec-ios) | iOS | [Обзор](https://aka.ms/csspeech/objectivecref) |
| [Python](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-python) | Windows, Linux, macOS | [Обзор](https://aka.ms/AA434tr)  |

Если вы предпочитаете использовать службы REST речи в текст, см. в разделе [интерфейсов REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

## <a name="tutorials-and-sample-code"></a>Учебники и образцы кода

После того, как у вас будет возможность использовать службы речи, просмотрите наше руководство, которое научит вас распознавать намерения из речи с помощью пакета SDK для распознавания речи и LUIS.

* [Учебник. Распознавание намерений в речи с помощью пакета SDK для распознавания речи и LUIS, C#](how-to-recognize-intents-from-speech-csharp.md)

Пример кода для Speech SDK доступен на сайте GitHub. В этих примерах рассматриваются сценарии общего характера, такие как чтение аудио из файла или потока, непрерывное и одиночное распознавание, а также работа с пользовательскими моделями.

* [Примеры преобразования речи в текст (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Примеры транскрибирования с помощью Пакетной службы (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Настройка

В дополнение к универсальной модели, используемой службами распознавания речи можно создать определенных пользовательских акустических, язык и произношение моделей с вашей работой. Ниже приведен список параметров настройки:

| Модель | Описание |
|-------|-------------|
| [Акустическая модель](how-to-customize-acoustic-models.md) | Создание пользовательской акустической модели полезна в том случае, если ваши приложения, средства или устройств используются в конкретной среде, как в автомобиль или фабрики с условиями определенной записи. В качестве примера можно привести речь с акцентом, наличие фонового шума или использование определенного микрофона для записи. |
| [Языковая модель](how-to-customize-language-model.md) | Создайте пользовательскую языковую модель для улучшения транскрибирования специализированных слов и грамматических конструкций, включая медицинскую терминологию и IT-жаргон. |
| [Модель произношения](how-to-customize-pronunciation.md) | Используя модель пользовательского произношения, вы можете определить фонетическую и отображаемую форму слова или термина. Это удобно для обработки настраиваемых терминов, например имен продуктов или аббревиатур. Все, что требуется для начала работы — это файл с записями произношения (простой TXT-файл). |

> [!NOTE]
> Параметры настройки зависят от языка и языкового стандарта (см. в разделе [поддерживаемые языки](supported-languages.md)).

## <a name="migration-guides"></a>Руководства по переносу

> [!WARNING]
> 15 октября 2019 г., будет списан распознавания речи Bing.

Если приложения, средств или продуктов при использовании API-интерфейсы распознавания речи Bing или Custom речи, мы создали руководства помогут вам выполнить миграцию в службы распознавания речи.

* [Перенос из распознавания речи Bing в службы речи](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
* [Перенос из настраиваемое преобразование речи в службы речи](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Справочная документация

* [пакет SDK для службы "Речь"](speech-sdk-reference.md);
* [Пакет SDK для речевых устройств](speech-devices-sdk.md)
* [REST API: Преобразование речи в текст](rest-speech-to-text.md)
* [REST API: Преобразование текста в речь](rest-text-to-speech.md)
* [REST API: Пакетное транскрибирование и настройка](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Дальнейшие действия

* [Получите ключ подписки для Служб речи бесплатно](get-started.md)
* [Получить Speech SDK](speech-sdk.md)
