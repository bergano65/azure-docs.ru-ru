---
title: Роли сущностей
titleSuffix: Azure Cognitive Services
description: Роли — это именованные, контекстно-зависимые подтипы сущности, которые используются только в шаблонах. Например, во фрагменте речи `buy a ticket from New York to London` New York и London являются городами, однако имеют разное значение в предложении. New York — это город отправления, а London — город назначения.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: diberry
ms.openlocfilehash: 958194d49cd403caeaf9dd21dd90a02cab098e45
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881463"
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


[!INCLUDE [H2 Roles versus hierarchical entities](../../../includes/cognitive-services-luis-hier-roles.md)] 

## <a name="example-role-for-entities"></a>Пример роли для сущностей

Роль представляет собой лишь контекстно определяемое расположение сущности в высказывании. Это наиболее эффективно в том случае, если высказывание содержит несколько типов сущностей. Самый простой пример для любого типа сущности — провести различие между исходным и целевым расположениями. Расположение может быть представлено в виде сущностей различных типов. 

Примеры использования — это перевод сотрудника из одного отдела в другой, причем каждый отдел представляет собой элемент списка. Например:  

`Move [PersonName] from [Department:from] to [Department:to]`. 

В возвращенном прогнозе обе сущности будут возвращены в ответе JSON и каждая сущность будет включать имя роли. 

## <a name="roles-with-prebuilt-entities"></a>Роли с предварительно созданными сущностями

Используйте роли с предварительно созданными сущностями, чтобы придать смысл различным экземплярам предварительно созданной сущности во фразе. 

### <a name="roles-with-datetimev2"></a>Роли с datetimeV2

Предварительно созданная сущность datetimeV2 хорошо понимает различные упоминания даты и времени во фразах. Возможно, вам необходимо указать даты и диапазоны дат не так, как они записаны в параметрах по умолчанию предварительно созданной сущности. 

## <a name="next-steps"></a>Дополнительная информация

* Сведения о добавлении [ролей](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
