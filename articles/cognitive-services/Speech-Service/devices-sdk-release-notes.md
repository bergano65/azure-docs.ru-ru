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
ms.date: 08/07/2018
ms.author: gracez
ms.openlocfilehash: 4d802b9f71edee1eec4b2c92881e2a8796db2865
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58005508"
---
# <a name="release-notes-of-cognitive-services-speech-devices-sdk"></a>Заметки о выпуске пакета SDK для речевых устройств в Cognitive Services

В следующих разделах перечислены изменения в последних выпусках.

## <a name="cognitive-services-speech-devices-sdk-131-2019-mar-release"></a>Устройства речи Cognitive Services SDK 1.3.1: Выпуск марта 2019 г. 

* Обновить [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) компонента до версии 1.3.1. Дополнительные сведения см. в [заметках о выпуске](https://aka.ms/csspeech/whatsnew). 
*   Обновленные настроить слово для обработки, см. в разделе Критические изменения.
*   Пример приложения добавляет Выбор языка для распознавания речи и перевода.

**Критические изменения** 

*   [Установка слово пробуждения](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg#run-a-sample-application) была упрощена, он теперь является частью приложения и не требуется отдельная Установка на устройстве.
*   Поддерживаются два события, и распознавания слова пробуждения был изменен.
    - RecognizingKeyword, указывает, что результат распознавания речи содержит текст ключевого слова (не проверено).
    - RecognizedKeyword, указывает, что ключевое слово распознавания завершения распознавание заданное ключевое слово.


## <a name="cognitive-services-speech-devices-sdk-110-2018-nov-release"></a>Выпуск пакета SDK для речевых устройств в Cognitive Services 1.1.0, ноябрь 2018 г. 

* Обновленный компонент [пакета SDK для распознавания речи](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) до версии 1.1.0. Дополнительные сведения см. в [заметках о выпуске](https://aka.ms/csspeech/whatsnew). 
* Повышена точность распознавания речи из дальней зоны за счет улучшенного алгоритма обработки аудио.
* В пример приложения добавлена поддержка распознавания речи на китайском языке.

## <a name="cognitive-services-speech-devices-sdk-101-2018-oct-release"></a>Выпуск пакета SDK для речевых устройств в Cognitive Services 1.0.1, октябрь 2018 г. 

* Обновленный компонент [пакета SDK для распознавания речи](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) до версии 1.0.1. Дополнительные сведения см. в [заметках о выпуске](https://aka.ms/csspeech/whatsnew). 
* Повышение точности распознавания речи за счет улучшенного алгоритма обработки аудио  
* Исправлена одна ошибка для звукового сеанса непрерывного распознавания.

**Критические изменения** 

* В этом выпуске представлен ряд критических изменений. Дополнительные сведения о соответствующих API приведены на [этой странице](https://aka.ms/csspeech/breakingchanges_1_0_0). 
* Файлы модели KWS несовместимы с пакетом SDK для речевых устройств версии 1.0.1. После записи новых файлов слов пробуждения на устройство аналогичные существующие файлы будут удалены. 

## <a name="cognitive-services-speech-devices-sdk-050-2018-aug-release"></a>Выпуск пакета SDK для речевых устройств в Cognitive Services 0.5.0, август 2018 г.

* Повышенная точность распознавания речи благодаря исправлению ошибки в коде обработки звука.
* Обновленный компонент [пакета SDK для распознавания речи](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) до версии 0.5.0. Дополнительные сведения см. в [заметках о выпуске](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="cognitive-services-speech-devices-sdk-0212733-2018-may-release"></a>Выпуск пакета SDK для речевых устройств в Cognitive Services 0.2.12733, май 2018 г.

Первый выпуск общедоступной предварительной версии пакета SDK для речевых устройств в Cognitive Services.
