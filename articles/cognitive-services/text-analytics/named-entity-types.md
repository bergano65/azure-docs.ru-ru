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
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: 77b75b1134bbc8366478b1f9f4d14e86e9684f70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91709538"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Поддерживаемые категории сущностей в API анализа текста v3

Используйте эту статью, чтобы найти категории сущностей, которые могут быть возвращены с помощью средства [распознавания сущностей](how-tos/text-analytics-how-to-entity-linking.md) (NER). Также доступна предварительная версия NER версии 3.1, которая включает возможность обнаружения персональных `PII` данных () и сведений о работоспособности ( `PHI` ). Кроме того, перейдите на вкладку **работоспособность** , чтобы просмотреть список поддерживаемых категорий в анализ текста для работоспособности.

## <a name="entity-categories"></a>Категории сущностей

#### <a name="general"></a>[Общие сведения](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="pii"></a>[ПЕРСОНАЛЬНЫЕ ДАННЫЕ](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[Здравоохранение](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

***

## <a name="next-steps"></a>Дальнейшие шаги

* [Как использовать распознавание именованных сущностей в Анализ текста](how-tos/text-analytics-how-to-entity-linking.md)
