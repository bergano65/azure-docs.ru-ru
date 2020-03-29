---
title: Установка речевых контейнеров
titleSuffix: Azure Cognitive Services
description: Подробная информация о вариантах конфигурации диаграммы диаграммы речевого руля speech-to-text.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: dapine
ms.openlocfilehash: 3f390affe7badb401277aa86d1867c763aa0ae3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "69971317"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Речевой текст (подчарт: диаграммы/speechToText)

Чтобы переопределить диаграмму "зонтик", `speechToText.` добавьте префикс на любом параметре, чтобы сделать ее более конкретной. Например, он переопределяет соответствующий параметр, например, `speechToText.numberOfConcurrentRequest` переопределяет. `numberOfConcurrentRequest`

|Параметр|Описание|Значение по умолчанию|
| -- | -- | -- |
| `enabled` | Включена ли служба **речевого текста.** | `false` |
| `numberOfConcurrentRequest` | Количество одновременных запросов на службу **речевого текста.** Эта диаграмма автоматически вычисляет ресурсы процессора и памяти, основанные на этом значении. | `2` |
| `optimizeForAudioFile`| Нужно ли оптимизировать сервис для ввода звука с помощью аудиофайлов. Если `true`эта диаграмма выделит больше ресурсов процессора для обслуживания. | `false` |
| `image.registry`| Реестр **изображений** докера от речи к тексту. | `containerpreview.azurecr.io` |
| `image.repository` | Репозиторий изображения док-докера **от речи к тексту.** | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | Тег изображения докера **от речи к тексту.** | `latest` |
| `image.pullSecrets` | Секреты изображения для вытягивать изображение докера **речи к тексту.** | |
| `image.pullByHash`| Независимо от того, вытягивается ли изображение докера хэшом. Если `true` `image.hash` это необходимо. | `false` |
| `image.hash`| Хэш изображения изображения **от речи к тексту.** Используется только `image.pullByHash: true`при .  | |
| `image.args.eula` (обязательный) | Означает, что вы приняли лицензию. Единственное допустимое значение`accept` | |
| `image.args.billing` (обязательный) | Значение конечных точек выставления счетов доступно на странице обзора речи портала Azure. | |
| `image.args.apikey` (обязательный) | Используется для отслеживания данных для выставлении счетов. ||
| `service.type` | Тип обслуживания Kubernetes службы **речи к тексту.** Для получения более подробной информации и проверки поддержки облачных провайдеров ознакомьтесь с инструкциями по [типу службkkk.](https://kubernetes.io/docs/concepts/services-networking/service/) | `LoadBalancer` |
| `service.port`|  Порт **службы речевого текста.** | `80` |
| `service.annotations` | Аннотации **от речи к тексту** для метаданных службы. Аннотации являются ключевыми парами значений. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Включен ли [«Горизонтальный под автоскалер».](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) Если, `true` `speech-to-text-autoscaler` будет развернута в кластере Kubernetes. | `true` |
| `service.podDisruption.enabled` | Включен ли [бюджет Pod Disruption.](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) Если, `true` `speech-to-text-poddisruptionbudget` будет развернута в кластере Kubernetes. | `true` |