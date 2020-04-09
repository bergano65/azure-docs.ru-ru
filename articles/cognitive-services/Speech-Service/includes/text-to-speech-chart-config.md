---
title: Установка речевых контейнеров
titleSuffix: Azure Cognitive Services
description: Подробная информация о вариантах конфигурации диаграммы диаграммы от текста к речи.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 22168974ab8b285413b4fa6e947c05f65a73ae12
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874362"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Текст к речи (подчарт: диаграммы/textToSpeech)

Чтобы переопределить диаграмму "зонтик", `textToSpeech.` добавьте префикс на любом параметре, чтобы сделать ее более конкретной. Например, он переопределяет соответствующий параметр, например, `textToSpeech.numberOfConcurrentRequest` переопределяет. `numberOfConcurrentRequest`

|Параметр|Описание|Значение по умолчанию|
| -- | -- | -- |
| `enabled` | Включена ли служба **«текст-речевой текст».** | `false` |
| `numberOfConcurrentRequest` | Количество одновременных запросов на службу **текстовых сообщений.** Эта диаграмма автоматически вычисляет ресурсы процессора и памяти, основанные на этом значении. | `2` |
| `optimizeForTurboMode`| Нужно ли оптимизировать ввод текста через текстовые файлы. Если `true`эта диаграмма выделит больше ресурсов процессора для обслуживания. | `false` |
| `image.registry`| Реестр изображений докера **от текста к речи.** | `containerpreview.azurecr.io` |
| `image.repository` | Репозиторий изображения док-докера **от текста к речи.** | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | Тег изображения докера **текста к речи.** | `latest` |
| `image.pullSecrets` | Секреты изображения для вытягивать изображение докера **текста к речи.** | |
| `image.pullByHash`| Независимо от того, вытягивается ли изображение докера хэшом. Если `true` `image.hash` это необходимо. | `false` |
| `image.hash`| **Хэш** изображения от текста к речи докера. Используется только `image.pullByHash: true`при .  | |
| `image.args.eula` (обязательный) | Означает, что вы приняли лицензию. Единственное допустимое значение`accept` | |
| `image.args.billing` (обязательный) | Значение конечных точек выставления счетов доступно на странице обзора речи портала Azure. | |
| `image.args.apikey` (обязательный) | Используется для отслеживания данных для выставлении счетов. ||
| `service.type` | Тип обслуживания Kubernetes службы **текст-к-речи.** Для получения более подробной информации и проверки поддержки облачных провайдеров ознакомьтесь с инструкциями по [типу службkkk.](https://kubernetes.io/docs/concepts/services-networking/service/) | `LoadBalancer` |
| `service.port`|  Порт **службы текст-к-речи.** | `80` |
| `service.annotations` | Аннотации **от текста к речи** для метаданных службы. Аннотации являются ключевыми парами значений. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Включен ли [«Горизонтальный под автоскалер».](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) Если, `true` `text-to-speech-autoscaler` будет развернута в кластере Kubernetes. | `true` |
| `service.podDisruption.enabled` | Включен ли [бюджет Pod Disruption.](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) Если, `true` `text-to-speech-poddisruptionbudget` будет развернута в кластере Kubernetes. | `true` |