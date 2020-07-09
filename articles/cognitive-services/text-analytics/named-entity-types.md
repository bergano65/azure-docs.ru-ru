---
title: Поддерживаемые категории для распознавания именованных сущностей
titleSuffix: Azure Cognitive Services
description: Сведения о поддерживаемых категориях сущностей в API анализа текста.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: e36a69be19844a75562f87d3c195494e3ef148a9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108513"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Поддерживаемые категории сущностей в API анализа текста v3

Используйте эту статью, чтобы найти категории сущностей, которые могут быть возвращены с помощью средства [распознавания сущностей](how-tos/text-analytics-how-to-entity-linking.md) (NER). Также доступна предварительная версия NER версии 3.1, которая включает возможность обнаружения персональных `PII` данных () и сведений о работоспособности ( `PHI` ). Кроме того, перейдите на вкладку **работоспособность** , чтобы просмотреть список поддерживаемых категорий в анализ текста для работоспособности.

## <a name="entity-categories"></a>Категории сущностей

#### <a name="general"></a>[Общие сведения](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="personal"></a>[Индивидуальный](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[Здравоохранение](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

***

## <a name="next-steps"></a>Дальнейшие действия

* [Как использовать распознавание именованных сущностей в Анализ текста](how-tos/text-analytics-how-to-entity-linking.md)
