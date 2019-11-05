---
title: Документация по пакету SDK для речевых устройств
titleSuffix: Azure Cognitive Services
description: Заметки о выпуске — что изменилось в последних версиях
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: wellsi
ms.openlocfilehash: ba1db1ccba6a1849756e75c9b9f7078371da5bfb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464745"
---
# <a name="release-notes-of-cognitive-services-speech-devices-sdk"></a>Заметки о выпуске пакета SDK для речевых устройств в Cognitive Services
В следующих разделах перечислены изменения в последних выпусках.

## <a name="speech-devices-sdk-160"></a>1\.6.0 пакета SDK для речевых устройств:

*   Поддержка [Azure KINECT DK](https://azure.microsoft.com/services/kinect-dk/) в Windows и Linux с общим [примером приложения](https://aka.ms/sdsdk-download)
*   Обновлен компонент [речевого пакета SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) до версии 1.6.0. Дополнительные сведения см. в [заметках о выпуске](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-151"></a>1\.5.1 пакета SDK для речевых устройств:

*   Включить [транскрипцию беседы](conversation-transcription-service.md) в примере приложения.
*   Обновлен компонент [речевого пакета SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) до версии 1.5.1. Дополнительные сведения см. в [заметках о выпуске](https://aka.ms/csspeech/whatsnew).

## <a name="cognitive-services-speech-devices-sdk-150-2019-may-release"></a>Cognitive Services пакета SDK для речевых устройств 1.5.0:2019-Май

*   Пакет SDK для речевых устройств теперь является общедоступным и больше не является предварительным.
*   Обновлен компонент [речевого пакета SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) до версии 1.5.0. Дополнительные сведения см. в [заметках о выпуске](https://aka.ms/csspeech/whatsnew).
*   Новая ключевая технология обеспечивает значительные улучшения качества, см. раздел критические изменения.
*   Новый конвейер обработки звука для улучшенного распознавания полей.

**Критические изменения**

*   В связи с новым ключевым словом технология на улучшенном портале ключевых слов необходимо создать повторно все ключевые слова. Чтобы полностью удалить старые ключевые слова с устройства, удалите старое приложение.
    - ADB удаление com. Microsoft. когинитивесервицес. Speech. Samples. сдсдкстартерапп

## <a name="cognitive-services-speech-devices-sdk-140-2019-apr-release"></a>Cognitive Services пакета SDK для речевых устройств 1.4.0:2019-Апр

* Обновлен компонент [речевого пакета SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) до версии 1.4.0. Дополнительные сведения см. в [заметках о выпуске](https://aka.ms/csspeech/whatsnew).

## <a name="cognitive-services-speech-devices-sdk-131-2019-mar-release"></a>Cognitive Services пакета SDK для речевых устройств 1.3.1:2019-Мар

* Обновлен компонент [речевого пакета SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) до версии 1.3.1. Дополнительные сведения см. в [заметках о выпуске](https://aka.ms/csspeech/whatsnew).
*   Обновлена обработка ключевых слов, см. раздел критические изменения.
*   Пример приложения добавляет выбор языка для распознавания речи и перевода.

**Критические изменения**

*   [Установка ключевого слова](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) упрощена, теперь она является частью приложения и не требует отдельной установки на устройстве.
*   Распознавание ключевых слов изменилось, и поддерживаются два события.
    - Рекогнизингкэйворд указывает, что в речевом результате содержится текст ключевого слова (не проверено).
    - Рекогнизедкэйворд указывает, что распознавание ключевых слов завершено для распознавания заданного ключевого слова.


## <a name="cognitive-services-speech-devices-sdk-110-2018-nov-release"></a>Выпуск пакета SDK для речевых устройств в Cognitive Services 1.1.0, ноябрь 2018 г.

* Обновленный компонент [пакета SDK для распознавания речи](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) до версии 1.1.0. Дополнительные сведения см. в [заметках о выпуске](https://aka.ms/csspeech/whatsnew).
* Повышена точность распознавания речи из дальней зоны за счет улучшенного алгоритма обработки аудио.
* В пример приложения добавлена поддержка распознавания речи на китайском языке.

## <a name="cognitive-services-speech-devices-sdk-101-2018-oct-release"></a>Выпуск пакета SDK для речевых устройств в Cognitive Services 1.0.1, октябрь 2018 г.

* Обновленный компонент [пакета SDK для распознавания речи](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) до версии 1.0.1. Дополнительные сведения см. в [заметках о выпуске](https://aka.ms/csspeech/whatsnew).
* Повышение точности распознавания речи за счет улучшенного алгоритма обработки аудио  
* Исправлена одна ошибка для звукового сеанса непрерывного распознавания.

**Критические изменения**

* В этом выпуске представлен ряд критических изменений. Дополнительные сведения о соответствующих API приведены на [этой странице](https://aka.ms/csspeech/breakingchanges_1_0_0).
* Файлы модели KWS несовместимы с пакетом SDK для речевых устройств версии 1.0.1. Существующие файлы ключевых слов будут удалены после того, как на устройство будет записано новое ключевое слово.

## <a name="cognitive-services-speech-devices-sdk-050-2018-aug-release"></a>Выпуск пакета SDK для речевых устройств в Cognitive Services 0.5.0, август 2018 г.

* Повышенная точность распознавания речи благодаря исправлению ошибки в коде обработки звука.
* Обновленный компонент [пакета SDK для распознавания речи](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) до версии 0.5.0. Дополнительные сведения см. в [заметках о выпуске](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="cognitive-services-speech-devices-sdk-0212733-2018-may-release"></a>Выпуск пакета SDK для речевых устройств в Cognitive Services 0.2.12733, май 2018 г.

Первый выпуск общедоступной предварительной версии пакета SDK для речевых устройств в Cognitive Services.
