---
title: 'Обзор распознавания намерения: служба речи'
titleSuffix: Azure Cognitive Services
description: Распознавание намерения позволяет распознать заранее определенные целевые показатели пользователей. В этой статье приводятся общие сведения о преимуществах и возможностях службы распознавания намерения.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: v-demjoh
keywords: Распознавание намерений
ms.openlocfilehash: 0d718459e0fd0ea410232d3a165b560aa8c59cd1
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92174644"
---
# <a name="what-is-intent-recognition"></a>Что такое распознавание намерения?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

В этом обзоре вы узнаете о преимуществах и возможностях распознавания намерения. Пакет SDK службы "Речь" в Cognitive Services интегрируется с Интеллектуальной службой распознавания речи (LUIS) для предоставления возможности распознавания намерений. Намерение — это действие, которое хочет выполнить пользователь, например забронировать билет на самолет, проверить прогноз погоды или совершить звонок.
Используя распознавание намерения, приложения, средства и устройства могут определить, что пользователь хочет инициировать или на основе параметров, определяемых в LUIS.

## <a name="luis-key-required"></a>Требуется ключ LUIS

* Чтобы распознать намерения в речи LUIS интегрируется в службу "Речь". Подписка на службу "Речь" не требуется.
* Распознавание речи интегрировано с пакетом SDK. Вы можете использовать ключ LUIS со службой распознавания речи.
* Распознавание намерений с помощью речевого пакета SDK [предлагается в подмножестве регионов, ПОДДЕРЖИВАЕМЫХ Luis](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#intent-recognition).

## <a name="get-started"></a>Начало работы

Чтобы приступить к распознаванию намерения, ознакомьтесь с [кратким](quickstarts/intent-recognition.md) руководством.

## <a name="sample-code"></a>Образец кода

Пример кода для распознавания намерений:

* [Краткое руководство. Использование предварительно созданного приложения для системы домашней автоматики](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app)
* [Распознавание намерений в речи с помощью пакета SDK службы "Речь" для C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)
* [Распознавание намерения и другие речевые службы с использованием Unity в C #](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/speechrecognizer)
* [Распознавание целей с помощью речевого пакета SDK для Python](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
* [Распознавание намерений и другие речевые службы с использованием речевого пакета SDK для C++ в Windows](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console)
* [Распознавание намерений и другие речевые службы с помощью речевого пакета SDK для Java в Windows или Linux](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/jre/console)
* [Распознавание намерений и другие речевые службы с использованием речевого пакета SDK для JavaScript в веб-браузере](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser)

## <a name="reference-docs"></a>Справочная документация

* [пакет SDK для службы "Речь"](speech-sdk-reference.md);

## <a name="next-steps"></a>Дальнейшие действия

* Завершение [краткого руководства](quickstarts/intent-recognition.md) по распознаванию намерения
* [Try the Speech service for free](overview.md#try-the-speech-service-for-free) (Бесплатное использование службы "Речь")
* [Получение пакета SDK для службы "Речь"](speech-sdk.md)
