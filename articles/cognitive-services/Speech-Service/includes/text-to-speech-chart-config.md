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
ms.date: 08/22/2019
ms.author: dapine
ms.openlocfilehash: c87132c3ae41dec82e3493f9a0ee2397455ff881
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971353"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Преобразование текста в речь (во вспомогательной диаграмме: Charts/Тексттоспич)

Чтобы переопределить диаграмму "тег", добавьте префикс `textToSpeech.` для любого параметра, чтобы сделать его более конкретным. Например, он переопределит соответствующий параметр, `textToSpeech.numberOfConcurrentRequest` например Overrides. `numberOfConcurrentRequest`

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
| `service.annotations` | Заметки преобразования **текста в речь** для метаданных службы. Аннотации — это пары "ключ — значение". <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Включен ли [Горизонтальный Автомасштабирование Pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) . Если `true`значение равно `text-to-speech-autoscaler` , то будет развернут в кластере Kubernetes. | `true` |
| `service.podDisruption.enabled` | Включен ли [бюджет на прерывание Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) . Если `true`значение равно `text-to-speech-poddisruptionbudget` , то будет развернут в кластере Kubernetes. | `true` |