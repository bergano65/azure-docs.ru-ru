---
title: Установка речевых контейнеров
titleSuffix: Azure Cognitive Services
description: Подробная информация о речевом зонтике руль конфигурации конфигурации параметров.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73523752"
---
### <a name="speech-umbrella-chart"></a>Речь (зонтичная диаграмма)

Значения на верхнем уровне "зонтик" диаграммы переопределяют соответствующие значения поддиаграмми. Таким образом, все на месте индивидуальные значения должны быть добавлены здесь.

|Параметр|Описание|Значение по умолчанию|
| -- | -- | -- | -- |
| `speechToText.enabled` | Включена ли служба **речевого текста.** | `true` |
| `speechToText.verification.enabled` | Включена `helm test` ли возможность службы **речевого текста.** | `true` |
| `speechToText.verification.image.registry` | Репозиторий изображения докера, который `helm test` используется для проверки службы **речевого текста.** Helm создает отдельный стручок внутри кластера для тестирования и вытягивает изображение *тестового использования* из этого реестра. | `docker.io` |
| `speechToText.verification.image.repository` | Репозиторий изображения докера, который `helm test` используется для проверки службы **речевого текста.** Шлем тест стручка использует этот репозиторий для вытягивания *тест-использования* изображения. | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | Тег изображения докера, используемый для `helm test` службы от речи к **тексту.** Шлем тест стручка использует этот тег, чтобы вытащить *тест использования* изображения. | `latest` |
| `speechToText.verification.image.pullByHash` | Независимо от того, вытягивается ли изображение *докера тестового использования* хэшом. `true`Если, `speechToText.verification.image.hash` должны быть добавлены, с действительным значением хэша изображения. | `false` |
| `speechToText.verification.image.arguments` | Аргументы, используемые для выполнения изображения *докера докера с использованием тестового использования.* Шлем тест стручка передает эти аргументы в контейнер при запуске. `helm test` | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Включена ли служба **«текст-речевой текст».** | `true` |
| `textToSpeech.verification.enabled` | Включена `helm test` ли возможность службы **речевого текста.** | `true` |
| `textToSpeech.verification.image.registry` | Репозиторий изображения докера, который `helm test` используется для проверки службы **речевого текста.** Helm создает отдельный стручок внутри кластера для тестирования и вытягивает изображение *тестового использования* из этого реестра. | `docker.io` |
| `textToSpeech.verification.image.repository` | Репозиторий изображения докера, который `helm test` используется для проверки службы **речевого текста.** Шлем тест стручка использует этот репозиторий для вытягивания *тест-использования* изображения. | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | Тег изображения докера, используемый для `helm test` службы от речи к **тексту.** Шлем тест стручка использует этот тег, чтобы вытащить *тест использования* изображения. | `latest` |
| `textToSpeech.verification.image.pullByHash` | Независимо от того, вытягивается ли изображение *докера тестового использования* хэшом. `true`Если, `textToSpeech.verification.image.hash` должны быть добавлены, с действительным значением хэша изображения. | `false` |
| `textToSpeech.verification.image.arguments` | Аргументы для выполнения с *изображением докера докера с использованием тестового использования.* Стручок испытания руля передает эти `helm test`аргументы контейнеру при запуске. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |