---
title: Атрибуты сущностей Academic Graph (Academic Knowledge API)
titlesuffix: Azure Cognitive Services
description: Дополнительные сведения об атрибутах сущностей, которые можно использовать совместно с Academic Graph в Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 596ce35a0d744691edd75d6071d4758691e0031b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705019"
---
# <a name="entity-attributes"></a>Атрибуты сущностей

Academic Graph содержит 7 типов сущностей. Все сущности будут иметь идентификатор сущности и тип сущности.

## <a name="common-entity-attributes"></a>Общие атрибуты сущностей
Название    |Описание                |Тип       | Операции
------- | ------------------------- | --------- | ----------------------------
Id      |Идентификатор сущности                  |Int64      |Равно
Ty      |Тип сущности                |enum   |Равно

## <a name="entity-type-enum"></a>Тип сущности enum
Название                                                            |value
----------------------------------------------------------------|-----
[Документ](PaperEntityAttributes.md)                               |0
[Автор](AuthorEntityAttributes.md)                             |1
[Журнал](JournalEntityAttributes.md)                           |2
[Серия конференций](JournalEntityAttributes.md)                 |3
[Экземпляр конференции](ConferenceInstanceEntityAttributes.md)    |4
[Принадлежность](AffiliationEntityAttributes.md)                   |5
[Область исследований](FieldsOfStudyEntityAttributes.md)                      |6

