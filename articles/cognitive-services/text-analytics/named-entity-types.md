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
ms.date: 01/22/2021
ms.author: aahi
ms.openlocfilehash: 883c5a20612f4dab44c0d06776ee287b27174ab9
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097309"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Поддерживаемые категории сущностей в API анализа текста v3

Используйте эту статью, чтобы найти категории сущностей, которые могут быть возвращены с помощью средства [распознавания сущностей](how-tos/text-analytics-how-to-entity-linking.md) (NER). NER запускает прогнозную модель для определения и категоризации именованных сущностей из входного документа.

Также доступна предварительная версия NER версии 3.1, которая включает возможность обнаружения персональных `PII` данных () и сведений о работоспособности ( `PHI` ). Кроме того, перейдите на вкладку **работоспособность** , чтобы просмотреть список поддерживаемых категорий в анализ текста для работоспособности. 

Список типов, возвращаемых версией 2,1, можно найти в разделе [руководств по миграции](migration-guide.md?tabs=named-entity-recognition) .

## <a name="entity-categories"></a>Категории сущностей

#### <a name="general"></a>[Общие сведения](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="pii"></a>[ПЕРСОНАЛЬНЫЕ ДАННЫЕ](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[Здравоохранение](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

**_

## <a name="next-steps"></a>Дальнейшие действия

_ [Использование распознавания именованных сущностей в анализ текста](how-tos/text-analytics-how-to-entity-linking.md)
