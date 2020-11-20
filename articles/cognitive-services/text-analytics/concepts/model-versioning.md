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
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: 7ab2e037cc7593431a668a216fcc152c0511c410
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965091"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>Управление версиями модели в API анализа текста

Версия 3 API анализа текста позволяет выбрать версию модели, используемую для данных. Используйте необязательный `model-version` параметр, чтобы выбрать версию модели в запросах API. Например: `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`. Если этот параметр не указан, API по умолчанию будет иметь последнюю стабильную версию. 

## <a name="available-versions"></a>Доступные версии

Используйте приведенную ниже таблицу, чтобы определить, какие версии модели поддерживаются каждой размещенной конечной точкой.


| Конечная точка                        | Поддерживаемые версии                                     | Последняя версия |
|---------------------------------|--------------------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`                             | `2020-04-01`   |
| `/languages`                    | `2019-10-01`, `2020-07-01`, `2020-09-01`               | `2020-09-01`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`                             | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01`,              | `2020-04-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2020-07-01`  | `2020-07-01`   |
| `/entities/health`              | `2020-09-03`                           | `2020-09-03`   |
| `/keyphrases`                   | `2019-10-01`, `2020-07-01`                             | `2020-07-01`   |


Сведения об обновлениях для этих моделей можно найти в статье [новые](../whats-new.md)возможности.

## <a name="text-analytics-for-health"></a>Анализ текста для сферы здравоохранения

[Анализ текста для контейнера работоспособности](../how-tos/text-analytics-for-health.md) использует отдельную версию модели, чем указанные выше конечные точки API.  Обратите внимание, что для каждого образа контейнера доступна только одна версия модели.

| Конечная точка                        | Тег образа контейнера                     | Версия модели |
|---------------------------------|-----------------------------------------|---------------|
| `/entities/health`              | `1.1.013530001-amd64-preview` или последняя версия          | `2020-09-03`  |
| `/entities/health`              | `1.1.013150001-amd64-preview`           | `2020-07-24`  |
| `/domains/health`               | `1.1.012640001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |


## <a name="next-steps"></a>Следующие шаги

* [Text Analytics overview](../overview.md) (Общие сведения об анализе текста)
* [Пример. Как определить тональность с помощью Анализа текста](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Распознавание сущностей](../how-tos/text-analytics-how-to-entity-linking.md)
