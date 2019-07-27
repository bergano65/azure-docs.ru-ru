---
title: Установка речевых контейнеров
titleSuffix: Azure Cognitive Services
description: Сведения о параметрах конфигурации диаграммы Helm текста в речь.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: e6c7dcd3015b0b8ab5b3c719ebd2397bc814b81a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "67717211"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Преобразование текста в речь (во вспомогательной диаграмме: Charts/Тексттоспич)

Чтобы переопределить диаграмму "тег", добавьте префикс `textToSpeech.` для любого параметра, чтобы сделать его более конкретным. Например, он переопределит соответствующий параметр, например `textToSpeech.numberOfConcurrentRequest` Overrides. `numberOfConcurrentRequest`

|Параметр|Описание|Значение по умолчанию|
| -- | -- | -- |
| `enabled` | Включена ли служба преобразования **текста в речь** . | `false` |
| `numberOfConcurrentRequest` | Количество одновременных запросов для службы преобразования **текста в речь** . Эта диаграмма автоматически вычисляет ресурсы ЦП и памяти на основе этого значения. | `2` |
| `optimizeForTurboMode`| Должна ли служба оптимизировать ввод текста через текстовые файлы. Если `true`значение равно, эта диаграмма будет выделять больше ресурсов ЦП для службы. | `false` |
| `image.registry`| Реестр образов DOCKER **для преобразования текста в речь** . | `containerpreview.azurecr.io` |
| `image.repository` | Репозиторий изображений DOCKER **для преобразования текста в речь** . | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | Тег изображения DOCKER **для преобразования текста в речь** . | `latest` |
| `image.pullSecrets` | Секреты образа для получения образа DOCKER для преобразования **текста в речь** . | |
| `image.pullByHash`| Указывает, извлекается ли образ DOCKER по хэшу. Если `true`значение—является обязательным.`image.hash` | `false` |
| `image.hash`| Хэш образа DOCKER **для преобразования текста в речь** . Используется, только `image.pullByHash: true`если.  | |
| `image.args.eula`необходимости | Указывает, что вы приняли условия лицензии. Единственное допустимое значение:`accept` | |
| `image.args.billing`необходимости | Значение URI конечной точки выставления счетов доступно на странице обзора речи портал Azure. | |
| `image.args.apikey`необходимости | Используется для отслеживания данных для выставлении счетов. ||
| `service.type` | Тип службы Kubernetes для службы преобразования **текста в речь** . Дополнительные сведения и проверка поддержки поставщика облачных служб см. в инструкциях по [типам службы Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) . | `LoadBalancer` |
| `service.port`|  Порт службы преобразования **текста в речь** . | `80` |
| `service.autoScaler.enabled` | Включен ли [Горизонтальный Автомасштабирование Pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) . Если `true`значение равно `text-to-speech-autoscaler` , то будет развернут в кластере Kubernetes. | `true` |
| `service.podDisruption.enabled` | Включен ли [бюджет на прерывание Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) . Если `true`значение равно `text-to-speech-poddisruptionbudget` , то будет развернут в кластере Kubernetes. | `true` |