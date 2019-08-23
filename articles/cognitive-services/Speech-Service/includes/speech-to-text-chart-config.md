---
title: Установка речевых контейнеров
titleSuffix: Azure Cognitive Services
description: Подробные сведения о параметрах конфигурации диаграммы "речь-текст Helm".
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: dapine
ms.openlocfilehash: 3f390affe7badb401277aa86d1867c763aa0ae3b
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971317"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Преобразование речи в текст (диаграмма/Спичтотекст)

Чтобы переопределить диаграмму "тег", добавьте префикс `speechToText.` для любого параметра, чтобы сделать его более конкретным. Например, он переопределит соответствующий параметр, `speechToText.numberOfConcurrentRequest` например Overrides. `numberOfConcurrentRequest`

|Параметр|Описание|Значение по умолчанию|
| -- | -- | -- |
| `enabled` | Включена ли служба **распознавания речи в текст** . | `false` |
| `numberOfConcurrentRequest` | Количество одновременных запросов для службы преобразования **речи в текст** . Эта диаграмма автоматически вычисляет ресурсы ЦП и памяти на основе этого значения. | `2` |
| `optimizeForAudioFile`| Должна ли служба оптимизировать входные данные с помощью звуковых файлов. Если `true`значение равно, эта диаграмма будет выделять больше ресурсов ЦП для службы. | `false` |
| `image.registry`| Реестр образа DOCKER **для преобразования речи в текст** . | `containerpreview.azurecr.io` |
| `image.repository` | Репозиторий образов DOCKER **для преобразования речи в текст** . | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | Тег образа DOCKER **для преобразования речи в текст** . | `latest` |
| `image.pullSecrets` | Секреты образа для получения образа DOCKER для преобразования **речи в текст** . | |
| `image.pullByHash`| Указывает, извлекается ли образ DOCKER по хэшу. Если `true`значение—является обязательным.`image.hash` | `false` |
| `image.hash`| Хэш образа DOCKER **для преобразования речи в текст** . Используется, только `image.pullByHash: true`если.  | |
| `image.args.eula`необходимости | Указывает, что вы приняли условия лицензии. Единственное допустимое значение:`accept` | |
| `image.args.billing`необходимости | Значение URI конечной точки выставления счетов доступно на странице обзора речи портал Azure. | |
| `image.args.apikey`необходимости | Используется для отслеживания данных для выставлении счетов. ||
| `service.type` | Тип службы Kubernetes для службы преобразования **речи в текст** . Дополнительные сведения и проверка поддержки поставщика облачных служб см. в инструкциях по [типам службы Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) . | `LoadBalancer` |
| `service.port`|  Порт службы преобразования **речи в текст** . | `80` |
| `service.annotations` | Заметки преобразования **речи в текст** для метаданных службы. Аннотации — это пары "ключ — значение". <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Включен ли [Горизонтальный Автомасштабирование Pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) . Если `true`значение равно `speech-to-text-autoscaler` , то будет развернут в кластере Kubernetes. | `true` |
| `service.podDisruption.enabled` | Включен ли [бюджет на прерывание Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) . Если `true`значение равно `speech-to-text-poddisruptionbudget` , то будет развернут в кластере Kubernetes. | `true` |