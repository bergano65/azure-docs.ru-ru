---
title: Установка речевых контейнеров
titleSuffix: Azure Cognitive Services
description: Сведения о параметрах конфигурации диаграммы Helm "речь".
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523752"
---
### <a name="speech-umbrella-chart"></a>Речь (схема-тег)

Значения на диаграмме "тег-элемент" верхнего уровня переопределяют соответствующие значения во вспомогательной диаграмме. Поэтому необходимо добавить все локальные настраиваемые значения.

|Параметр|Description (Описание)|значение по умолчанию|
| -- | -- | -- | -- |
| `speechToText.enabled` | Включена ли служба **распознавания речи в текст** . | `true` |
| `speechToText.verification.enabled` | Включена ли функция `helm test` для службы преобразования **речи в текст** . | `true` |
| `speechToText.verification.image.registry` | Репозиторий образов DOCKER, который `helm test` использует для тестирования службы преобразования **речи в текст** . Helm создает отдельный модуль Pod внутри кластера для тестирования и извлекает из этого реестра образ, *используемый для тестирования* . | `docker.io` |
| `speechToText.verification.image.repository` | Репозиторий образов DOCKER, который `helm test` использует для тестирования службы преобразования **речи в текст** . Модуль тестирования Helm использует этот репозиторий для извлечения образа *тестового использования* . | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | Тег образа DOCKER, используемый с `helm test` для службы преобразования **речи в текст** . Модуль тестирования Helm использует этот тег для извлечения изображения, *используемого для тестирования* . | `latest` |
| `speechToText.verification.image.pullByHash` | Указывает, извлекается ли в *тесте* образ DOCKER с помощью хэша. Если `true`, необходимо добавить `speechToText.verification.image.hash` с допустимым значением хэша изображения. | `false` |
| `speechToText.verification.image.arguments` | Аргументы, используемые для выполнения *теста — использовать* образ DOCKER. Модуль тестирования Helm передает эти аргументы в контейнер при выполнении `helm test`. | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Включена ли служба преобразования **текста в речь** . | `true` |
| `textToSpeech.verification.enabled` | Включена ли функция `helm test` для службы преобразования **речи в текст** . | `true` |
| `textToSpeech.verification.image.registry` | Репозиторий образов DOCKER, который `helm test` использует для тестирования службы преобразования **речи в текст** . Helm создает отдельный модуль Pod внутри кластера для тестирования и извлекает из этого реестра образ, *используемый для тестирования* . | `docker.io` |
| `textToSpeech.verification.image.repository` | Репозиторий образов DOCKER, который `helm test` использует для тестирования службы преобразования **речи в текст** . Модуль тестирования Helm использует этот репозиторий для извлечения образа *тестового использования* . | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | Тег образа DOCKER, используемый с `helm test` для службы преобразования **речи в текст** . Модуль тестирования Helm использует этот тег для извлечения изображения, *используемого для тестирования* . | `latest` |
| `textToSpeech.verification.image.pullByHash` | Указывает, извлекается ли в *тесте* образ DOCKER с помощью хэша. Если `true`, необходимо добавить `textToSpeech.verification.image.hash` с допустимым значением хэша изображения. | `false` |
| `textToSpeech.verification.image.arguments` | Аргументы для выполнения *с помощью образа DOCKER.* Модуль тестирования Helm передает эти аргументы в контейнер при запуске `helm test`. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |