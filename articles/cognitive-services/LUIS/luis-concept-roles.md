---
title: Использование ролей в сущностях на основе шаблонов
titleSuffix: Azure Cognitive Services
description: Роли — это именованные, контекстно-зависимые подтипы сущности, которые используются только в шаблонах. Например, во фразе "купить билет из Нью-Йорка в Лондон" Нью-Йорк и Лондон являются городами, но имеют разное значение в предложении. New York — это город отправления, а London — город назначения.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 8a92852a2721bd391ddf7c3cf3489b820c4a1400
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277622"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>Роли сущностей в шаблонах — это контекстно-зависимые подтипы
Роли — это именованные, контекстно-зависимые подтипы сущности, которые используется только в [шаблонах](luis-concept-patterns.md).

Например, во фрагменте речи `buy a ticket from New York to London` New York и London являются городами, однако имеют разное значение в предложении. New York — это город отправления, а London — город назначения. 

Роли присваивают имена этим различиям:

|Сущность|Роль|Назначение|
|--|--|--|
|Расположение|origin|откуда вылетает самолет|
|Расположение|ресурс destination|куда прилетает самолет|
|Предварительно созданная сущность datetimeV2|значение|дата окончания|
|Предварительно созданная сущность datetimeV2|from|дата начала|

## <a name="how-are-roles-used-in-patterns"></a>Использование ролей в шаблонах
Во фрагменте речи шаблона роли используются внутри фрагмента речи: 

|Шаблон с ролями сущности|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Синтаксис роли в шаблонах
Сущность и роль заключаются в круглые скобки, `{}`. Сущность и роль разделяются точкой с запятой. 


[!INCLUDE[H2 Roles versus hierarchical entities](../../../includes/cognitive-services-luis-hier-roles.md)] 

## <a name="roles-with-prebuilt-entities"></a>Роли с предварительно созданными сущностями

Используйте роли с предварительно созданными сущностями, чтобы придать смысл различным экземплярам предварительно созданной сущности во фразе. 

### <a name="roles-with-datetimev2"></a>Роли с datetimeV2

Предварительно созданная сущность datetimeV2 хорошо понимает различные упоминания даты и времени во фразах. Возможно, вам необходимо указать даты и диапазоны дат не так, как они записаны в параметрах по умолчанию предварительно созданной сущности. 

## <a name="next-steps"></a>Дополнительная информация

* Сведения о добавлении [ролей](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
