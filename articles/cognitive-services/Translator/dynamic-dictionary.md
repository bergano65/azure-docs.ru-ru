---
title: Динамический словарь API перевода текстов
titleSuffix: Azure Cognitive Services
description: В этой статье объясняется, как использовать функцию динамического словаря API перевода текстов Cognitive Services Azure.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 944bca8644da6127e73af04eb75d01697cd34399
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446712"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Использование динамического словаря

Если вы уже знаете перевод, который хотите применить к слову или фразе, вы можете указать его в запросе как исправление. Динамический словарь является надежным только для составных существительных, таких как правильные имена и названия продуктов.

**Синтаксис**

< мстранс: словарный перевод = "перевод фразы" > фраза </мстранс: dictionary >

**Требования:**

* Языки `From` и `To` должны включать английский и другой поддерживаемый язык. 
* Необходимо включить параметр `From` в запрос перевода API, а не использовать функцию автоопределения. 

**Пример: en-de:**

Вход источника: `The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

Выходные данные целевого объекта: `Das Wort "wordomatic" ist ein Wörterbucheintrag.`

Эта возможность работает одинаково как в режиме HTML, так и без него.

Используйте эту функцию с осторожностью. Лучшим способом настройки перевода является использование настраиваемого транслятора. Custom Translator обеспечивает полное использование контекста и статистические значения вероятности. Если вы имеете или можете создавать учебные данные, которые показывают вашу работу или фразу в контексте, полученные результаты будут гораздо лучше. Дополнительные сведения о Custom Translator можно найти по ссылке [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
