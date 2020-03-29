---
title: Установка речевых контейнеров
titleSuffix: Azure Cognitive Services
description: Подробная информация о вариантах конфигурации диаграммы диаграммы от текста к речи.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: dapine
ms.openlocfilehash: c87132c3ae41dec82e3493f9a0ee2397455ff881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "69971353"
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