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
ms.openlocfilehash: 9431ff862dd987a1a806087053014e7c880bf801
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/28/2020
ms.locfileid: "84143307"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>Управление версиями модели в API анализа текста

Версия 3 API анализа текста позволяет выбрать версию модели, используемую для данных. Используйте необязательный `model-version` параметр, чтобы выбрать версию модели в запросах API. Например: `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01` Если этот параметр не указан, API по умолчанию будет иметь последнюю стабильную версию. 

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

## <a name="next-steps"></a>Дальнейшие шаги

* [Text Analytics overview](../overview.md) (Общие сведения об анализе текста)
* [Пример. Как определить тональность с помощью Анализа текста](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Распознавание сущностей](../how-tos/text-analytics-how-to-entity-linking.md)