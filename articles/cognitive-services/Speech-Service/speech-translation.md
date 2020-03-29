---
title: Перевод речи с речевой службой
titleSuffix: Azure Cognitive Services
description: Служба речи позволяет добавлять сквозной многоязычный перевод речи в приложения, инструменты и устройства в режиме реального времени. Один API можно использовать как для преобразования речи в речь, так и для преобразования речи в текст.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: f51288da6af3580ba7592950cde4f17d7adad529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052621"
---
# <a name="what-is-speech-translation"></a>Что такое перевод речи?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Перевод речи с речевого сервиса позволяет в режиме реального времени, многоязычный речевой перевод и перевод аудиопотоков в режиме реального времени. С помощью Speech SDK ваши приложения, инструменты и устройства имеют доступ к исходным транскрипциям и выводам перевода для предоставленного аудио. Промежуточные результаты транскрипции и перевода возвращаются по мере обнаружения речи, а результаты финала могут быть преобразованы в синтезированную речь.

Механизм перевода Microsoft работает на двух различных подходах: статистический машинный перевод (SMT) и нейронный машинный перевод (NMT). СМТ использует передовой статистический анализ для оценки наилучших возможных переводов с учетом контекста нескольких слов. С NMT нейронные сети используются для обеспечения более точного, естественного звучания переводов, используя полный контекст предложений для перевода слов.

Сегодня корпорация Майкрософт использует NMT для перевода на самые популярные языки. Все [языки, доступные для преобразования речи в речь](language-support.md#speech-translation), обрабатываются на платформе NMT. Преобразование речи в текст может выполняться на SMT или NMT в зависимости от конкретной пары языков. Когда целевой язык поддерживается NMT, полный перевод работает на базе NMT. Когда целевой язык не поддерживается NMT, перевод представляет собой гибрид NMT и SMT, используя английский язык в качестве "поворота" между двумя языками.

## <a name="core-features"></a>Основные функции

Вот функции, доступные через речь SDK и REST AIS:

| Вариант использования | SDK | REST |
|----------|-----|------|
| Перевод речи к тексту с результатами распознавания. | Да | нет |
| Перевод речи к речи. | Да | нет |
| Промежуточные результаты распознавания и перевода. | Да | нет |

## <a name="get-started-with-speech-translation"></a>Начало с речевого перевода

Мы предлагаем quickstarts разработан, чтобы вы работаете код менее чем за 10 минут. Эта таблица включает в себя список речевого перевода quickstarts организованы по языку.

| Краткое руководство | Платформа | Справочник по интерфейсам API |
|------------|----------|---------------|
| [C, Ядро .NET](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [Обзор](https://aka.ms/csspeech/csharpref) |
| [СЗ, Рамочная программа .NET](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Обзор](https://aka.ms/csspeech/csharpref) |
| [СЗ, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Обзор](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Обзор](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Обзор](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Образец кода

Пример кода для речи SDK доступен на GitHub. Эти образцы охватывают общие сценарии, такие как чтение аудио из файла или потока, непрерывное распознавание/перевод с одним выстрелом и работа с пользовательскими моделями.

* [Образцы речи к тексту и перевода (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Руководства по переносу

Если приложения, инструменты или продукты используют [API-сообщение Translator Speech,](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)мы создали руководства, которые помогут вам перейти на службу speech.

* [Миграция из API речи переводчика в службу речи](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Справочная документация

* [Пакет SDK для службы "Речь"](speech-sdk-reference.md)
* [Пакет SDK для устройств, подключаемых к службе "Речь"](speech-devices-sdk.md)
* [REST API: От речи к тексту](rest-speech-to-text.md)
* [REST API: Текст к речи](rest-text-to-speech.md)
* [REST API: Транскрипция и настройка пакета](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Дальнейшие действия

* [Try the Speech service for free](get-started.md) (Бесплатное использование службы "Речь")
* [Получение пакета SDK для службы "Речь"](speech-sdk.md)
