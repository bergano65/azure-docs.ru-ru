---
title: Установка речевых контейнеров
titleSuffix: Azure Cognitive Services
description: Подробные сведения о параметрах конфигурации диаграммы "речь-текст Helm".
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.openlocfilehash: 267c2fb72b38053429019746a573c740d812c38c
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82608389"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Преобразование речи в текст (диаграмма/Спичтотекст)

Чтобы переопределить диаграмму "тег", добавьте префикс `speechToText.` для любого параметра, чтобы сделать его более конкретным. Например, он переопределит соответствующий параметр, `speechToText.numberOfConcurrentRequest` например Overrides. `numberOfConcurrentRequest`

|Параметр|Описание|По умолчанию|
| -- | -- | -- |
| `enabled` | Включена ли служба **распознавания речи в текст** . | `false` |
| `numberOfConcurrentRequest` | Количество одновременных запросов для службы преобразования **речи в текст** . Эта диаграмма автоматически вычисляет ресурсы ЦП и памяти на основе этого значения. | `2` |
| `optimizeForAudioFile`| Должна ли служба оптимизировать входные данные с помощью звуковых файлов. Если `true`значение равно, эта диаграмма будет выделять больше ресурсов ЦП для службы. | `false` |
| `image.registry`| Реестр образа DOCKER **для преобразования речи в текст** . | `containerpreview.azurecr.io` |
| `image.repository` | Репозиторий образов DOCKER **для преобразования речи в текст** . | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | Тег образа DOCKER **для преобразования речи в текст** . | `latest` |
| `image.pullSecrets` | Секреты образа для получения образа DOCKER для преобразования **речи в текст** . | |
| `image.pullByHash`| Указывает, извлекается ли образ DOCKER по хэшу. Если `true` `image.hash` значение — является обязательным. | `false` |
| `image.hash`| Хэш образа DOCKER **для преобразования речи в текст** . Используется, только `image.pullByHash: true`если.  | |
| `image.args.eula` (обязательный) | Указывает, что вы приняли условия лицензии. Единственное допустимое значение:`accept` | |
| `image.args.billing` (обязательный) | Значение URI конечной точки выставления счетов доступно на странице обзора речи портал Azure. | |
| `image.args.apikey` (обязательный) | Используется для отслеживания данных для выставлении счетов. ||
| `service.type` | Тип службы Kubernetes для службы преобразования **речи в текст** . Дополнительные сведения и проверка поддержки поставщика облачных служб см. в [инструкциях по типам службы Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) . | `LoadBalancer` |
| `service.port`|  Порт службы преобразования **речи в текст** . | `80` |
| `service.annotations` | Заметки преобразования **речи в текст** для метаданных службы. Аннотации — это пары "ключ — значение". <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Включен ли [Горизонтальный Автомасштабирование Pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) . Если `true`значение равно `speech-to-text-autoscaler` , то будет развернут в кластере Kubernetes. | `true` |
| `service.podDisruption.enabled` | Включен ли [бюджет на прерывание Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) . Если `true`значение равно `speech-to-text-poddisruptionbudget` , то будет развернут в кластере Kubernetes. | `true` |

#### <a name="sentiment-analysis-sub-chart-chartsspeechtotext"></a>Анализ тональности (во вспомогательной диаграмме: диаграммы/Спичтотекст)

Начиная с v 2.2.0 контейнера преобразования речи в текст для анализа тональности с помощью API анализа текста используются следующие параметры.

|Параметр|Описание|Значения|По умолчанию|
| --- | --- | --- | --- |
|`textanalytics.enabled`| Включена ли служба **Text-Analytics**| true/false| `false`|
|`textanalytics.image.registry`| Реестр образов DOCKER для **Text-Analytics**| допустимый реестр образов DOCKER| |
|`textanalytics.image.repository`| Репозиторий образов DOCKER для **Text-Analytics**| допустимый репозиторий образов DOCKER| |
|`textanalytics.image.tag`| Тег Image для **Text-Analytics** DOCKER| допустимый тег образа DOCKER| |
|`textanalytics.image.pullSecrets`| Секреты образа для получения образа DOCKER **Text-Analytics**| допустимое имя секреты| |
|`textanalytics.image.pullByHash`| Указывает, извлекает ли образ DOCKER по хэшу.  Если `yes`значение `image.hash` равно, необходимо также указать. Если `no`задано значение, задайте для него значение false. Значение по умолчанию — `false`.| true/false| `false`|
|`textanalytics.image.hash`| Хэш образа DOCKER для **Text-Analytics** . Используйте его только с `image.pullByHash:true`.| допустимый хэш образа DOCKER | |
|`textanalytics.image.args.eula`| Один из обязательных аргументов контейнера **Text-Analytics** , который указывает, что вы приняли условия лицензии. Значение этого параметра должно быть следующим: `accept`.| `accept`, если вы хотите использовать контейнер | |
|`textanalytics.image.args.billing`| Один из обязательных аргументов контейнера **Text-Analytics** , который указывает URI конечной точки выставления счетов. Значение URI конечной точки выставления счетов доступно на странице обзора речи портал Azure.|допустимый URI конечной точки выставления счетов||
|`textanalytics.image.args.apikey`| Один из обязательных аргументов контейнера **Text-Analytics** , который используется для трассировки сведений о выставлении счетов.| допустимый apiKey||
|`textanalytics.cpuRequest`| Запрошенный ЦП для контейнера **Text-Analytics**| INT| `3000m`|
|`textanalytics.cpuLimit`| Ограниченный ЦП для контейнера **Text-Analytics**| | `8000m`|
|`textanalytics.memoryRequest`| Запрошенная память для контейнера **Text-Analytics**| | `3Gi`|
|`textanalytics.memoryLimit`| Ограниченная память для контейнера **Text-Analytics**| | `8Gi`|
|`textanalytics.service.sentimentURISuffix`| Суффикс URI анализа тональности, полный URI имеет формат "`<service>`http://:`<port>`/`<sentimentURISuffix>`". | | `text/analytics/v3.0-preview/sentiment`|
|`textanalytics.service.type`| Тип службы **Text-Analytics** в Kubernetes. См. раздел [типы служб Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) | допустимый тип службы Kubernetes | `LoadBalancer` |
|`textanalytics.service.port`| Порт службы **Text-Analytics**| INT| `50085`|
|`textanalytics.service.annotations`| Заметки, которые пользователи могут добавлять в метаданные службы **Text-Analytics** . например<br/> **Примечания**<br/>`   `**Some/annotation1: значение1**<br/>`  `**Some/annotation2: значение2** | заметки, по одному на каждую строку| |
|`textanalytics.serivce.autoScaler.enabled`| Включен ли [Горизонтальный Автомасштабирование Pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) . Если параметр включен `text-analytics-autoscaler` , будет развернут в кластере Kubernetes | true/false| `true`|
|`textanalytics.service.podDisruption.enabled`| Включен ли [бюджет на прерывание Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) . Если параметр включен `text-analytics-poddisruptionbudget` , будет развернут в кластере Kubernetes| true/false| `true`|
