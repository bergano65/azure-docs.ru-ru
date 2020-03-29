---
title: Документация по пакету SDK для речевых устройств
titleSuffix: Azure Cognitive Services
description: Примечания к выпуску предоставляют журнал обновлений, улучшений, исправлений ошибок и изменений в SDK устройств речи. Эта статья обновляется с каждым выпуском речевых устройств SDK.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: 29dcb3c0e74482fd6670d1a0983e751043379d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80371619"
---
# <a name="release-notes-speech-devices-sdk"></a>Примечания к выпуску: Речевые устройства SDK

В следующих разделах перечислены изменения в последних выпусках.

## <a name="speech-devices-sdk-190"></a>Речевые устройства SDK 1.9.0:

- Предоставляются первоначальные файлы для [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter) (Linux ARM64).
- Roobo v1 теперь использует Maven для речи SDK
- Обновлен компонент [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) до версии 1.9.0. Дополнительные сведения см. в [заметках о выпуске](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-170"></a>Речевые устройства SDK 1.7.0:

- Linux ARM теперь поддерживается.
- Первоначальные бинарные файлы для [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2) предоставляются (Linux ARM64).
- Пользователи Windows `AudioConfig.fromDefaultMicrophoneInput()` `AudioConfig.fromMicrophoneInput(deviceName)` могут использовать или указывать микрофон для использования.
- Размер библиотеки был оптимизирован.
- Поддержка распознавания нескольких оборотов с использованием одного и того же объекта распознавания речи/намерения.
- Исправить случайные повесить, что будет происходить при остановке распознавания.
- Пример приложений теперь содержит пример файла participants.properties, чтобы продемонстрировать формат файла.
- Обновлен компонент [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) до версии 1.7.0. Дополнительные сведения см. в [заметках о выпуске](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-160"></a>Речевые устройства SDK 1.6.0:

- Поддержка [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) на Windows и Linux с помощью общего [примерного приложения](https://aka.ms/sdsdk-download)
- Обновлен компонент [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) до версии 1.6.0. Дополнительные сведения см. в [заметках о выпуске](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-151"></a>Речевые устройства SDK 1.5.1:

- Включите [Транскрипцию разговоров](conversation-transcription-service.md) в пример приложения.
- Обновлен компонент [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) до версии 1.5.1. Дополнительные сведения см. в [заметках о выпуске](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-150-2019-may-release"></a>Речевые устройства SDK 1.5.0: 2019-Май релиз

- Речевые устройства SDK теперь ГА и больше не закрытый предварительный просмотр.
- Обновлен компонент [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) до версии 1.5.0. Дополнительные сведения см. в [заметках о выпуске](https://aka.ms/csspeech/whatsnew).
- Новая технология ключевых слов приносит значительные улучшения качества, см. Breaking Changes.
- Новый конвейер обработки аудио для улучшения распознавания дальнего поля.

**Критические изменения**

- Благодаря новой технологии ключевых слов все ключевые слова должны быть воссозданы на нашем улучшенном портале ключевых слов. Чтобы полностью удалить старые ключевые слова с устройства удалить старое приложение.
  - adb удалить com.microsoft.cognitiveservices.speech.samples.sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Речевые устройства SDK 1.4.0: 2019-Апрель релиз

- Обновлен компонент [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) до версии 1.4.0. Дополнительные сведения см. в [заметках о выпуске](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Речевые устройства SDK 1.3.1: 2019-Mar релиз

- Обновлен компонент [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) до версии 1.3.1. Дополнительные сведения см. в [заметках о выпуске](https://aka.ms/csspeech/whatsnew).
- Обновлено обработка ключевых слов, см. Breaking Changes.
- Пример приложения добавляет выбор языка как для распознавания речи, так и для перевода.

**Критические изменения**

- [Установка ключевого слова](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) была упрощена, теперь она является частью приложения и не нуждается в отдельной установке на устройстве.
- Распознавание ключевых слов изменилось, и два события поддерживаются.
  - `RecognizingKeyword,`указывает на то, что результат речи содержит (непроверенный) текст ключевых слов.
  - `RecognizedKeyword`, указывает на то, что распознавание ключевых слов завершило признание данного ключевого слова.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Речевые устройства SDK 1.1.0: 2018-Ноябрь релиз

- Обновленный компонент [пакета SDK для распознавания речи](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) до версии 1.1.0. Дополнительные сведения см. в [заметках о выпуске](https://aka.ms/csspeech/whatsnew).
- Повышена точность распознавания речи из дальней зоны за счет улучшенного алгоритма обработки аудио.
- В пример приложения добавлена поддержка распознавания речи на китайском языке.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Речевые устройства SDK 1.0.1: 2018-Октябрь релиз

- Обновленный компонент [пакета SDK для распознавания речи](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) до версии 1.0.1. Дополнительные сведения см. в [заметках о выпуске](https://aka.ms/csspeech/whatsnew).
- Повышение точности распознавания речи за счет улучшенного алгоритма обработки аудио
- Исправлена одна ошибка для звукового сеанса непрерывного распознавания.

**Критические изменения**

- В этом выпуске представлен ряд критических изменений. Дополнительные сведения о соответствующих API приведены на [этой странице](https://aka.ms/csspeech/breakingchanges_1_0_0).
- Файлы модели KWS несовместимы с пакетом SDK для речевых устройств версии 1.0.1. Существующие файлы ключевых слов будут удалены после того, как новые файлы ключевых слов будут записаны на устройство.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Речевые устройства SDK 0.5.0: 2018-Август релиз

- Повышенная точность распознавания речи благодаря исправлению ошибки в коде обработки звука.
- Обновленный компонент [пакета SDK для распознавания речи](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) до версии 0.5.0. Дополнительные сведения см. в [заметках о выпуске](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Речевые устройства SDK 0.2.12733: 2018-Май релиз

Первый выпуск общедоступной предварительной версии пакета SDK для речевых устройств в Cognitive Services.
