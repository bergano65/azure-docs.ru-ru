---
title: Установить контейнеры речи
titleSuffix: Azure Cognitive Services
description: Сведения о конфигурации параметры диаграммы helm обобщающий речи.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711816"
---
### <a name="speech-umbrella-chart"></a>Речи (обобщающий диаграмма)

Значения в диаграмме верхнего уровня «общий» переопределить соответствующие значения вложенные диаграммы. Таким образом все значения в локальной настройки здесь должны быть добавлены.

|Параметр|Описание|значение по умолчанию|
| -- | -- | -- | -- |
| `speechToText.enabled` | Ли **речи в текст** включена служба. | `true` |
| `speechToText.verification.enabled` | Ли `helm test` возможность для **речи в текст** включена служба. | `true` |
| `speechToText.verification.image.registry` | Репозиторий образов docker, `helm test` использует для проверки **речи в текст** службы. Helm создает отдельный pod в кластере для тестирования и извлекает *теста используйте* образ из этого реестра. | `docker.io` |
| `speechToText.verification.image.repository` | Репозиторий образов docker, `helm test` использует для проверки **речи в текст** службы. Helm pod теста использует этот репозиторий для извлечения *теста используйте* изображения. | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | Тег образа docker, используемый с `helm test` для **речи в текст** службы. Helm pod теста использует этот тег для извлечения *теста используйте* изображения. | `latest` |
| `speechToText.verification.image.pullByHash` | Ли *теста используйте* будет отображен образ docker, по хэшу. Если `true`, `speechToText.verification.image.hash` должны быть добавлены с допустимым изображением хэш-значение. | `false` |
| `speechToText.verification.image.arguments` | Аргументы, используемые для выполнения *теста используйте* образа docker. Helm теста pod передает эти аргументы в контейнер, при выполнении `helm test`. | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Ли **преобразования текста в речь** включена служба. | `true` |
| `textToSpeech.verification.enabled` | Ли `helm test` возможность для **речи в текст** включена служба. | `true` |
| `textToSpeech.verification.image.registry` | Репозиторий образов docker, `helm test` использует для проверки **речи в текст** службы. Helm создает отдельный pod в кластере для тестирования и извлекает *теста используйте* образ из этого реестра. | `docker.io` |
| `textToSpeech.verification.image.repository` | Репозиторий образов docker, `helm test` использует для проверки **речи в текст** службы. Helm pod теста использует этот репозиторий для извлечения *теста используйте* изображения. | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | Тег образа docker, используемый с `helm test` для **речи в текст** службы. Helm pod теста использует этот тег для извлечения *теста используйте* изображения. | `latest` |
| `textToSpeech.verification.image.pullByHash` | Ли *теста используйте* будет отображен образ docker, по хэшу. Если `true`, `textToSpeech.verification.image.hash` должны быть добавлены с допустимым изображением хэш-значение. | `false` |
| `textToSpeech.verification.image.arguments` | Аргументы для выполнения с *теста используйте* образа docker. Модуль теста helm передает эти аргументы контейнера при выполнении `helm test`. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |