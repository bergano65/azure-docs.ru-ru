---
title: Установить контейнеры речи
titleSuffix: Azure Cognitive Services
description: Сведения о параметрах конфигурации диаграмма helm речи в текст.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1b46c58d3f3c804052e637f7bde2e1a456764dba
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717223"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Преобразование речи в текст (вложенные диаграммы: диаграммы/speechToText)

Чтобы переопределить диаграмма «общий», добавьте префикс `speechToText.` на любой параметр, чтобы сделать его более конкретным. Например, она переопределит соответствующего параметра например `speechToText.numberOfConcurrentRequest` переопределяет `numberOfConcurrentRequest`.

|Параметр|Описание|Значение по умолчанию|
| -- | -- | -- |
| `enabled` | Ли **речи в текст** включена служба. | `false` |
| `numberOfConcurrentRequest` | Число одновременных запросов для **речи в текст** службы. Эта диаграмма автоматически вычисляет ресурсы ЦП и памяти, на основе этого значения. | `2` |
| `optimizeForAudioFile`| Должен ли служба для оптимизации для звукового ввода через звуковые файлы. Если `true`, эта диаграмма будет выделять дополнительные ресурсы ЦП для службы. | `false` |
| `image.registry`| **Речи в текст** реестр образов docker. | `containerpreview.azurecr.io` |
| `image.repository` | **Речи в текст** репозиторий образов docker. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | **Речи в текст** тега образа docker. | `latest` |
| `image.pullSecrets` | Секреты образ за получение **речи в текст** образа docker. | |
| `image.pullByHash`| Ли по хэшу будет отображен образ docker. Если `true`, `image.hash` является обязательным. | `false` |
| `image.hash`| **Речи в текст** хэш изображения docker. Используется, только если `image.pullByHash: true`.  | |
| `image.args.eula` (обязательно) | Указывает, что вы приняли лицензии. Единственным допустимым значением является `accept` | |
| `image.args.billing` (обязательно) | Выставления счетов значение URI конечная точка доступна на странице обзора речи на портале Azure. | |
| `image.args.apikey` (обязательно) | Используется для отслеживания данных для выставлении счетов. ||
| `service.type` | Тип объекта службы Kubernetes **речи в текст** службы. См. в разделе [службы Kubernetes типы инструкции](https://kubernetes.io/docs/concepts/services-networking/service/) подробности и сведения о поддержке поставщика облака. | `LoadBalancer` |
| `service.port`|  Порт **речи в текст** службы. | `80` |
| `service.autoScaler.enabled` | Ли [горизонтальной автомасштабирования Pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) включен. Если `true`, `speech-to-text-autoscaler` будут развернуты в кластере Kubernetes. | `true` |
| `service.podDisruption.enabled` | Ли [бюджета перебоев в работе Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) включен. Если `true`, `speech-to-text-poddisruptionbudget` будут развернуты в кластере Kubernetes. | `true` |