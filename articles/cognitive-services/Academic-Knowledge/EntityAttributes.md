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
ms.openlocfilehash: accc2803895f3892075cdd9877ca98344ab88bd1
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884823"
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

