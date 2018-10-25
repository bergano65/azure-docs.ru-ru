---
title: Атрибуты сущностей Academic Graph (Academic Knowledge API)
titlesuffix: Azure Cognitive Services
description: Дополнительные сведения об атрибутах сущностей, которые можно использовать совместно с Academic Graph в Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: a203fdf6562dabb1b9d6e8ab5bb8f46ff6d5dc27
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902796"
---
# <a name="entity-attributes"></a>Атрибуты сущностей

Academic Graph содержит 7 типов сущностей. Все сущности будут иметь идентификатор сущности и тип сущности.

## <a name="common-entity-attributes"></a>Общие атрибуты сущностей
ИМЯ    |ОПИСАНИЕ                |type       | Операции
------- | ------------------------- | --------- | ----------------------------
Идентификатор      |Идентификатор сущности                  |Int64      |Равно
Ty      |Тип сущности                |enum   |Равно

## <a name="entity-type-enum"></a>Тип сущности enum
ИМЯ                                                            |value
----------------------------------------------------------------|-----
[Документ](PaperEntityAttributes.md)                               |0
[Автор](AuthorEntityAttributes.md)                             |1
[Журнал](JournalEntityAttributes.md)                           |2
[Серия конференций](JournalEntityAttributes.md)                 |3
[Экземпляр конференции](ConferenceInstanceEntityAttributes.md)    |4.
[Принадлежность](AffiliationEntityAttributes.md)                   |5
[Область исследований](FieldsOfStudyEntityAttributes.md)                      |6

