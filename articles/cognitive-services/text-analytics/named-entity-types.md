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
ms.openlocfilehash: f66ed9a7d26a0c2d7def7d17820379b8e3460460
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373081"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Поддерживаемые категории сущностей в API анализа текста v3

Используйте эту статью, чтобы найти категории сущностей, которые могут быть возвращены с помощью средства [распознавания сущностей](how-tos/text-analytics-how-to-entity-linking.md) (NER). Также доступна предварительная версия NER версии 3.1, которая включает возможность обнаружения персональных `PII` данных () и сведений о работоспособности ( `PHI` ). Кроме того, перейдите на вкладку **работоспособность** , чтобы просмотреть список поддерживаемых категорий в анализ текста для работоспособности.

## <a name="entity-categories"></a>Категории сущностей

#### <a name="general"></a>[Общие сведения](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="personal"></a>[Личный](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[Здравоохранение](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

***

## <a name="next-steps"></a>Дальнейшие действия

* [Как использовать распознавание именованных сущностей в Анализ текста](how-tos/text-analytics-how-to-entity-linking.md)
