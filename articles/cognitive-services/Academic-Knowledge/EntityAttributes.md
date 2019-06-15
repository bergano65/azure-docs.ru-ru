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
ms.openlocfilehash: 485775660ecfdf2291365ab98c9188295ea2cbde
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61340185"
---
# <a name="entity-attributes"></a>Атрибуты сущностей

Academic Graph содержит 7 типов сущностей. Все сущности будут иметь идентификатор сущности и типа сущности.

## <a name="common-entity-attributes"></a>Общие атрибуты сущностей
ИМЯ    |Описание                |type       | Операции
------- | ------------------------- | --------- | ----------------------------
Идентификатор      |Идентификатор сущности                  |Int64      |Равно
Ty      |Тип сущности                |enum   |Равно

## <a name="entity-type-enum"></a>Тип сущности enum
Name                                                            |value
----------------------------------------------------------------|-----
[Документ](PaperEntityAttributes.md)                               |0
[Автор](AuthorEntityAttributes.md)                             |1
[Журнал](JournalEntityAttributes.md)                           |2
[Серия конференций](JournalEntityAttributes.md)                 |3
[Экземпляр конференции](ConferenceInstanceEntityAttributes.md)    |4\.
[Принадлежность](AffiliationEntityAttributes.md)                   |5
[Область исследований](FieldsOfStudyEntityAttributes.md)                      |6

