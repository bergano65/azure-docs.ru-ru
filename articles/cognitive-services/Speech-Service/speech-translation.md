---
title: Трансляция речи с помощью речевых служб
titleSuffix: Azure Cognitive Services
description: Служба распознавания речи позволяет добавлять в приложения, средства и устройства в реальном времени многоязыковый перевод речи. Один API можно использовать как для преобразования речи в речь, так и для преобразования речи в текст.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: a05a2bf81a278322bc4e07ed959aedb828c39b73
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815458"
---
# <a name="what-is-speech-translation"></a>Что такое перевод речи?

Трансляция речи из службы распознавания речи обеспечивает преобразование речевых потоков в реальном времени, многоязыковую речь и преобразования речи в текст. С помощью пакета SDK для распознавания речи приложения, средства и устройства имеют доступ к транскрипции источника и выходам перевода для предоставленного звука. Промежуточные записи и результаты перевода возвращаются при обнаружении речи, а окончательные результаты могут быть преобразованы в синтезированный речевой режим.

Модуль перевода Майкрософт работает на основе двух разных подходов: статистического преобразования машин (SMT) и машинного перевода (НМТ). SMT использует расширенный статистический анализ для оценки лучших возможных переводов, учитывая контекст нескольких слов. С помощью НМТ нейронные сети используются для предоставления более точных и естественных переводов с помощью полного контекста предложений для перевода слов.

Сегодня Корпорация Майкрософт использует НМТ для перевода на большинство популярных языков. Все [языки, доступные для преобразования речи в речь](language-support.md#speech-translation), обрабатываются на платформе NMT. Преобразование речи в текст может выполняться на SMT или NMT в зависимости от конкретной пары языков. Если целевой язык поддерживается НМТ, то полный перевод НМТ. Если целевой язык не поддерживается НМТ, перевод является гибридом НМТ и SMT с использованием английского языка в качестве "Pivot" между двумя языками.

## <a name="core-features"></a>Основные компоненты

Ниже приведены функции, доступные через пакет SDK для распознавания речи и интерфейсы API-интерфейсов.

| Вариант использования | SDK | REST |
|----------|-----|------|
| Преобразование речи в текст с результатами распознавания. | ДА | Нет |
| Преобразование речи в речь. | ДА | Нет |
| Промежуточные результаты распознавания и перевода. | ДА | Нет |

## <a name="get-started-with-speech-translation"></a>Начало работы с переводом речи

Мы предлагаем краткие руководства, предназначенные для выполнения кода менее чем через 10 минут. В этой таблице содержится список кратких руководств по языку для перевода речи.

| Краткое руководство | платформа | Справочные материалы по API |
|------------|----------|---------------|
| [C#, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [Обзор](https://aka.ms/csspeech/csharpref) |
| [C#.NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Обзор](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Обзор](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Обзор](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Обзор](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Пример кода

Пример кода для пакета SDK для распознавания речи доступен на сайте GitHub. В этих примерах рассматриваются распространенные сценарии, такие как чтение звука из файла или потока, непрерывное распознавание и преобразование с одним снимком, а также работа с пользовательскими моделями.

* [Примеры преобразования речи в текст и перевода (пакет SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Руководства по переносу

Если приложения, средства или продукты используют [API перевода речи](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview), мы создали руководства, которые помогут вам перейти на службу речи.

* [Миграция из API перевода речи в службу речи](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Справочные документы

* [пакет SDK для службы "Речь"](speech-sdk-reference.md);
* [Пакет SDK для речевых устройств](speech-devices-sdk.md)
* [REST API: преобразование речи в текст](rest-speech-to-text.md)
* [REST API: преобразование текста в речь](rest-text-to-speech.md)
* [REST API: транскрипция и настройка пакета](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Дальнейшие действия

* [Try the Speech service for free](get-started.md) (Бесплатное использование службы "Речь")
* [Получение пакета SDK для распознавания речи](speech-sdk.md)
