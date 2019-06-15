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
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 318e71b68bbabeeef34c75a412f9fdd5b6db754a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65073022"
---
# <a name="entity-roles-for-contextual-subtypes"></a>Сущности роли для контекстных подтипы

Роли дают сущностей иметь именованные подтипы. Роли можно использовать с любым предварительно созданным или настраиваемым типом сущности. Они используются в примерах речевых фрагментов и шаблонов. 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>Пример сущности результаты обучения компьютера ролей

В utterance «купить билет **Нью-Йорк** для **Лондон**, Нью-Йорке и Лондоне, города, но каждая имеет разное значение в предложении. New York — это город отправления, а London — город назначения. 

```
buy a ticket from New York to London
```

Роли присваивают имена этим различиям:

|Тип сущности|Имя сущности|Роль|Назначение|
|--|--|--|--|
|Простая|Расположение|origin|откуда вылетает самолет|
|Простая|Расположение|destination|куда прилетает самолет|

## <a name="non-machine-learned-entity-example-of-roles"></a>Пример сущности без машин узнали ролей

В utterance «Schedule собрания от 8 до 9,» оба числа указывают время, но каждый раз имеет разное значение в utterance. Роли предоставляют имя различия. 

```
Schedule the meeting from 8 to 9
```

|Тип сущности|Имя роли|Значение|
|--|--|--|
|Предварительно созданная сущность datetimeV2|время начала|8|
|Предварительно созданная сущность datetimeV2|время окончания|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>— Несколько сущностей в utterance то же самое, что роли? 

Несколько сущностей могут существовать в utterance и могут быть извлечены без использования ролей. Если контекст предложения указывает с версии сущности имеет значение, то следует использовать роль. 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>Не использовать ролей дубликатов без значения

Если utterance включает список расположений, `I want to travel to Seattle, Cairo, and London.`, это список, где каждый элемент не имеет дополнительное значение. 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>Роли следует использовать, если значение указано, дубликаты

Если utterance включает список расположений со значением, `I want to travel from Seattle, with a layover in Londen, landing in Cairo.`, такое значение origin, этой однодневной остановки и назначение, записываемых с ролями.

### <a name="roles-can-indicate-order"></a>Роли можно указать порядок

Если utterance меняются, чтобы показать порядок, которой необходимо извлечь, `I want to first start with Seattle, second London, then third Cairo`, можно извлечь двумя способами. Токены, которые указывают роли, можно пометить `first start with`, `second`, `third`. Можно также использовать предварительно созданные сущности **порядковый номер** и **GeographyV2** предварительно созданных сущностей в составной сущности для захвата идея порядок и место. 

## <a name="how-are-roles-used-in-example-utterances"></a>Использование ролей в примере фразы

Если у сущности есть роли, а объект помечен как в примере utterance, вы можете выбрать выбрать только что сущность, или выберите сущности и роли. 

Следующий пример фразы используйте сущностями и ролями:

|Представления токенов|Просмотр объекта|
|--|--|
|Мне интересно узнать больше о **Сиэтл**|Я хочу узнать больше о {Location}|
|Купить билет из Сиэтла в Нью-Йорк|Купить билет из {расположение: Origin} до {расположение: Destination}|

## <a name="how-are-roles-used-in-patterns"></a>Использование ролей в шаблонах
Во фрагменте речи шаблона роли используются внутри фрагмента речи: 

|Шаблон с ролями сущности|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Синтаксис роли в шаблонах
Сущность и роль заключаются в круглые скобки, `{}`. Сущность и роль разделяются точкой с запятой. 

## <a name="entity-roles-versus-collaborator-roles"></a>Сущности роли и роли участника совместной работы

Сущности роли применяются к модели данных приложения LUIS. [Участник совместной работы](luis-concept-collaborator.md) роли применяются к уровней доступом. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Дальнейшие действия

* Используйте [практические руководства](tutorial-entity-roles.md) использование ролей сущности и узнали без машин сущностей
* Сведения о добавлении [ролей](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
