---
title: Анализ групп (API лингвистического анализа)
titlesuffix: Azure Cognitive Services
description: Дополнительные сведения об определении фраз в тексте с помощью анализа групп, известного также как "анализ структуры фразы".
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 7611f5f16111b5d8b0d2d293750f658125e50837
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60535448"
---
# <a name="constituency-parsing"></a>Анализ групп

> [!IMPORTANT]
> Поддержка предварительной версии Лингвистического анализа прекращена 9 августа 2018 г. Мы рекомендуем использовать [модули текстовой аналитики Машинного обучения Azure](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) для анализа и обработки текста.

Цель анализа групп (также известного как "анализ структуры фразы") заключается в определении фраз в тексте.
Это может быть полезно при извлечении информации из текста.
Например, если клиентам необходимо найти имена функций или ключевые фразы в большом объеме текста и просмотреть модификаторы и действия, связанные с каждой такой фразой.

## <a name="phrases"></a>Фразы

Для лингвиста *фраза* — это не просто последовательность слов.
Чтобы быть фразой, группа слов должна образовывать единое целое и выполнять определенную функцию в предложении.
Такую группу слов можно переместить или заменить как одно целое без нарушения легкости восприятия и грамматической структуры предложения.

Анализ предложения

> Я хочу найти новый гибридный автомобиль с Bluetooth.

Эта фраза содержит именное словосочетание: "новый гибридный автомобиль с Bluetooth".
Откуда мы знаем, что это фраза?
Чтобы придать предложению немного поэтичности, мы можем переписать предложение, переместив всю эту фразу в начало:

> Новый гибридный автомобиль с Bluetooth — найти его хочу я.

При этом мы можем заменить эту фразу на другую с аналогичной функцией и смыслом, например "новый роскошный автомобиль":

> Я хочу найти новый роскошный автомобиль.

Если бы мы выбрали разные подмножества слов, такие замены сделали бы предложения странными или нечитаемыми.
Давайте посмотрим, что произойдет, если мы переместим "найти новый" в начало:

> Найти новый я хочу гибридный автомобиль с Bluetooth.

Получившееся в результате предложение очень сложно прочитать и понять.

Цель анализатора — найти все такие фразы.
Интересно, что фразы на естественном языке, как правило, являются вложенными друг в друга.
Такие фразы можно естественным образом представить в виде дерева, например следующим образом:

![Дерево](./Images/tree.png)

В этом дереве ветви с надписью "NP" — это именные словосочетания.
У нас таких фраз несколько: *я*, *новый гибридный автомобиль*, *Bluetooth* и *новый гибридный автомобиль с Bluetooth*.

## <a name="phrase-types"></a>Типы фраз

| Метка | Описание | Пример |
|-------|-------------|---------|
|ADJP   | Адъективное словосочетание | "такой грубый" |
|ADVP   | Наречное словосочетание | "уточнить посредством" |
|CONJP  | Союзное словосочетание | "а также" |
|FRAG   | Отрывок, используемый для неполных или фрагментарных входных данных | "Настоятельно рекомендуется..." |
|INTJ   | Восклицание | "Ура" |
|LST    | Маркер списков, включая знаки препинания | "#4)" |
|NAC    | Не являющийся составляющей элемент, используемый для указания области, не являющейся составляющей фразы |  "и за хорошую сделку" в "вы получаете кое-что и за хорошую сделку" |
|NP | Именное словосочетание | "вкусная картофельная лепешка" |
|NX | Используется в определенных сложных NP для обозначения главной части| |
|PP | Пропозициональное словосочетание| "в бассейне" |
|PRN    | Заключенная в скобки часть| "(так называемые)" |
|PRT    | Частица| "бы" в "если бы" |
|QP | Количественное словосочетание (т. е. сложная мера или количество) в именном словосочетании| "около 75 долл. США" |
|RRC    | Сокращенное относительное предложение.| "все еще не решены" в "многие проблемы все еще не решены" |
|S  | Предложение. | "Это предложение."
|SBAR   | Подчиненное предложение, которое зачастую вводится подчиненным союзом | "когда уходил"в "я оглянулся, когда уходил."|
|SBARQ  | Прямой вопрос, который вводится вопросительным словом | "Какой смысл?" |
|SINV   | Обратное повествовательное предложение | "Не знали они об этом, вот и все." (Обратите внимание, что подлежащее "они" в этом предложении следует после сказуемого "знали".) |
|SQ | Вопрос с ответом "да" или "нет" или главное предложение с вопросительным словом | "Они получили автомобиль?" |
|UCP    | Несходная соподчиненная фраза| "маленький и с ошибками" (обратите внимание, как адъективное и пропозициональное словосочетания соединяются с помощью союза "и")|
|VP | Глагольное словосочетание | "побежал в лес" |
|WHADJP | Вопросительное адъективное словосочетание | "как неудобно" |
|WHADVP | Вопросительное наречное словосочетание| "когда" |
|WHNP   | Вопросительное именное словосочетание| "какой картофель", "сколько супа"|
|WHPP   | Вопросительное пропозициональное словосочетание| "в какой стране"|
|X  | Неизвестное, неопределенное словосочетание или словосочетание, которое нельзя взять в скобки.| Первое неприменимо для русского языка. В английском артикль "the" в "the... the soup" |


## <a name="specification"></a>Спецификация

В приведенных здесь деревьях используются S-выражения из [Penn Treebank](https://catalog.ldc.upenn.edu/LDC99T42).
