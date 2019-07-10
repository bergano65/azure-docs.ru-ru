---
title: Установить контейнеры речи
titleSuffix: Azure Cognitive Services
description: Сведения о конфигурации параметры диаграммы helm преобразования текста в речь.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: e6c7dcd3015b0b8ab5b3c719ebd2397bc814b81a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717211"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Преобразование текста в речь (вложенные диаграммы: диаграммы/textToSpeech)

Чтобы переопределить диаграмма «общий», добавьте префикс `textToSpeech.` на любой параметр, чтобы сделать его более конкретным. Например, она переопределит соответствующего параметра например `textToSpeech.numberOfConcurrentRequest` переопределяет `numberOfConcurrentRequest`.

|Параметр|Описание|Значение по умолчанию|
| -- | -- | -- |
| `enabled` | Ли **преобразования текста в речь** включена служба. | `false` |
| `numberOfConcurrentRequest` | Число одновременных запросов для **преобразования текста в речь** службы. Эта диаграмма автоматически вычисляет ресурсы ЦП и памяти, на основе этого значения. | `2` |
| `optimizeForTurboMode`| Должен ли служба для оптимизации для текстового ввода через текстовые файлы. Если `true`, эта диаграмма будет выделять дополнительные ресурсы ЦП для службы. | `false` |
| `image.registry`| **Преобразования текста в речь** реестр образов docker. | `containerpreview.azurecr.io` |
| `image.repository` | **Преобразования текста в речь** репозиторий образов docker. | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | **Преобразования текста в речь** тега образа docker. | `latest` |
| `image.pullSecrets` | Секреты образ за получение **преобразования текста в речь** образа docker. | |
| `image.pullByHash`| Ли по хэшу будет отображен образ docker. Если `true`, `image.hash` является обязательным. | `false` |
| `image.hash`| **Преобразования текста в речь** хэш изображения docker. Используется, только если `image.pullByHash: true`.  | |
| `image.args.eula` (обязательно) | Указывает, что вы приняли лицензии. Единственным допустимым значением является `accept` | |
| `image.args.billing` (обязательно) | Выставления счетов значение URI конечная точка доступна на странице обзора речи на портале Azure. | |
| `image.args.apikey` (обязательно) | Используется для отслеживания данных для выставлении счетов. ||
| `service.type` | Тип объекта службы Kubernetes **преобразования текста в речь** службы. См. в разделе [службы Kubernetes типы инструкции](https://kubernetes.io/docs/concepts/services-networking/service/) подробности и сведения о поддержке поставщика облака. | `LoadBalancer` |
| `service.port`|  Порт **преобразования текста в речь** службы. | `80` |
| `service.autoScaler.enabled` | Ли [горизонтальной автомасштабирования Pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) включен. Если `true`, `text-to-speech-autoscaler` будут развернуты в кластере Kubernetes. | `true` |
| `service.podDisruption.enabled` | Ли [бюджета перебоев в работе Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) включен. Если `true`, `text-to-speech-poddisruptionbudget` будут развернуты в кластере Kubernetes. | `true` |