---
title: Укажите версию модели в Анализ текста v3
titleSuffix: Azure Cognitive Services
description: Узнайте, как указать модель API анализа текста, используемую в данных.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 04/21/2020
ms.author: aahi
ms.openlocfilehash: c9c598d385768e604d9b42e3ea055629e6ae2866
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108555"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>Управление версиями модели в API анализа текста

Версия 3 API анализа текста позволяет выбрать версию модели, используемую для данных. Используйте необязательный `model-version` параметр, чтобы выбрать версию модели в запросах API. Например: `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`. Если этот параметр не указан, API по умолчанию будет иметь последнюю стабильную версию. 

## <a name="available-versions"></a>Доступные версии

Используйте приведенную ниже таблицу, чтобы узнать, какие версии модели поддерживаются каждой конечной точкой.


| Конечная точка                        | Поддерживаемые версии                       | Последняя версия |
|---------------------------------|------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`               | `2020-04-01`   |
| `/languages`                    | `2019-10-01`                             | `2019-10-01`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`               | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01` | `2020-04-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01` | `2020-04-01`   |
| `/keyphrases`                   | `2019-10-01`                             | `2019-10-01`   |


Сведения об обновлениях для этих моделей можно найти в статье [новые](../whats-new.md)возможности.

## <a name="text-analytics-for-health"></a>Анализ текста для работоспособности

[Анализ текста для контейнера работоспособности](../how-tos/text-analytics-for-health.md) использует отдельную версию модели, чем указанные выше конечные точки API.  Обратите внимание, что для каждого образа контейнера доступна только одна версия модели.

| Конечная точка                        | Тег образа контейнера                     | Версия модели |
|---------------------------------|-----------------------------------------|---------------|
| `/domains/health`               | `1.1.012640001-amd64-preview`или последняя версия | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |




## <a name="next-steps"></a>Дальнейшие действия

* [Text Analytics overview](../overview.md) (Общие сведения об анализе текста)
* [Пример. Как определить тональность с помощью Анализа текста](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Распознавание сущностей](../how-tos/text-analytics-how-to-entity-linking.md)
