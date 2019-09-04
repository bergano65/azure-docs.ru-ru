---
title: Роли для сущностей — LUIS
titleSuffix: Azure Cognitive Services
description: Роли — это именованные, контекстно-зависимые подтипы сущности, которые используются только в шаблонах. Например, во фрагменте речи `buy a ticket from New York to London` New York и London являются городами, однако имеют разное значение в предложении. New York — это город отправления, а London — город назначения.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: eead9e0fec8ac4322bc7816de4a4774f8be8129c
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257955"
---
# <a name="entity-roles-for-contextual-subtypes"></a>Роли сущности для контекстных подтипов

Роли позволяют сущностям иметь именованные подтипы. Роли можно использовать с любым предварительно созданным или настраиваемым типом сущности. Они используются в примерах речевых фрагментов и шаблонов. 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>Пример ролей в сущности, занятой компьютером

В utterance «купите билет из **Нью-Йорка** в **Лондоне**, оба Нью-Йорк и Лондон являются городами, но каждый из них имеет свой смысл в предложении. New York — это город отправления, а London — город назначения. 

```
buy a ticket from New York to London
```

Роли присваивают имена этим различиям:

|Тип сущности|Имя сущности|Role|Цель|
|--|--|--|--|
|Простой|Местоположение|источник|откуда вылетает самолет|
|Простой|Местоположение|назначение|куда прилетает самолет|

## <a name="non-machine-learned-entity-example-of-roles"></a>Пример ролей для сущности, не являющейся машиной

В utterance «запланировать собрание с 8 до 9» оба числа указывают время, но каждый раз имеют разные значения в utterance. Роли предоставляют имена для различий. 

```
Schedule the meeting from 8 to 9
```

|Тип сущности|Имя роли|Значение|
|--|--|--|
|Предварительно созданная сущность datetimeV2|StartTime|8|
|Предварительно созданная сущность datetimeV2|Завершения|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>Несколько сущностей в utterance, то же самое, что и роли? 

В utterance может существовать несколько сущностей, которые можно извлечь без использования ролей. Если контекст предложения указывает, какая версия сущности имеет значение, то следует использовать роль. 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>Не используйте роли для дубликатов без смысла

Если utterance включает список расположений, `I want to travel to Seattle, Cairo, and London.`то это список, в котором у каждого элемента нет дополнительного значения. 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>Использовать роли, если дубликаты обозначают значение

Если utterance включает список расположений с указанием значения, `I want to travel from Seattle, with a layover in Londen, landing in Cairo.`то это значение происхождения, потом и назначение должны быть захвачены с помощью ролей.

### <a name="roles-can-indicate-order"></a>Роли могут указывать порядок

Если utterance изменился для указания порядка, который нужно извлечь, `I want to first start with Seattle, second London, then third Cairo`можно извлечь несколько способов. Можно пометить токены, которые указывают на роль `first start with`, `second`, `third`,. Можно также использовать предварительно построенный **порядковый номер** сущности и предварительно созданную сущность **GeographyV2** в составной сущности, чтобы получить представление о порядке и месте. 

## <a name="how-are-roles-used-in-example-utterances"></a>Как используются роли в примере фразы продолжительностью?

Если сущность имеет роль, а сущность помечена в примере utterance, можно выбрать только сущность или выбрать сущность и роль. 

В следующем примере фразы продолжительностью используются сущности и роли:

|Представление "токен"|Представление сущностей|
|--|--|
|Мне интересно узнать больше о **Сиэтле**|Мне интересно узнать больше о {Location}|
|Покупка билета из Сиэтле в Нью-Йорк|Купите билет из {Location: Origin} в {Location: Destination}|

## <a name="how-are-roles-used-in-patterns"></a>Использование ролей в шаблонах
Во фрагменте речи шаблона роли используются внутри фрагмента речи: 

|Шаблон с ролями сущности|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Синтаксис роли в шаблонах
Сущность и роль заключаются в круглые скобки, `{}`. Сущность и роль разделяются точкой с запятой. 

## <a name="entity-roles-versus-collaborator-roles"></a>Роли сущности и роли совместной работы

Роли сущности применяются к модели данных приложения LUIS. Роли [совместной работы или участников](luis-concept-keys.md#contributions-from-other-authors) применяются к уровням доступа для разработки. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Следующие шаги

* [Практическое руководство](tutorial-entity-roles.md) . Использование ролей сущностей с сущностями, которые не используются для компьютера
* Сведения о добавлении [ролей](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
