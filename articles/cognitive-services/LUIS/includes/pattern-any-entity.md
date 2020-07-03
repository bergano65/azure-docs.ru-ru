---
title: включить файл
description: включить файл
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 8aef4a5eeeec27bfb2ca3edf9ff6823b88431319
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591847"
---
Сущность Pattern.any позволяет найти данные произвольной формы, в которых формулировка сущности затрудняет определение ее окончание из остальной части высказывания.

Приложение для управления персоналом помогает сотрудникам находить формы организации.

|Фраза|
|--|
|Где находится **HRF-123456**?|
|Кто написал **HRF-123234**?|
|Было ли **HRF-456098** опубликовано на французском языке?|

Тем не менее в каждой из форм присутствует как форматированное имя, используемое в предыдущей таблице, так и такое понятное имя, как `Request relocation from employee new to the company 2018 version 5`.

Фразы с понятным названием формы выглядят следующим образом.

|Фраза|
|--|
|Где находится **Request relocation from employee new to the company 2018 version 5**?|
|Кто написал **Request relocation from employee new to the company 2018 version 5**?|
|Был ли **Request relocation from employee new to the company 2018 version 5** опубликован во Франции?|

Заголовки переменной длины включают машинные слова, из-за которых LUIS может неправильно определить окончание сущности. С помощью шаблонной сущности Pattern.any можно указать начало и конец названия формы, что позволит LUIS правильно извлекать ее имя.

|Пример шаблона высказывания|
|--|
|Где находится {FormName} [?]|
|Кто создатель {FormName} [?]|
|Было ли {FormName} опубликовано на французском языке[?]|

См. справочник по [Pattern.any](../reference-entity-pattern-any.md).