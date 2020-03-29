---
title: Динамический словарь API перевода текстов
titleSuffix: Azure Cognitive Services
description: В этой статье объясняется, как использовать динамическую функцию словаря API текста переводчика когнитивных служб Azure Cognitive Services.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 944bca8644da6127e73af04eb75d01697cd34399
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75446712"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Как использовать динамический словарь

Если вы уже знаете перевод, который хотите применить к слову или фразе, вы можете указать его в запросе как исправление. Динамический словарь безопасен только для составных существительным, таких как правильные названия и названия продуктов.

**Синтаксис**

<mstrans:словарный перевод"перевод фразы">фраза</mstrans:dictionary>

**Требования**

* `From` Языки `To` и языки должны включать английский и другой поддерживаемый язык. 
* Необходимо включить `From` параметр в запрос на перевод API вместо использования функции автоматического обнаружения. 

**Пример: en-de:**

Исходный вход:`The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

Целевой выход:`Das Wort "wordomatic" ist ein Wörterbucheintrag.`

Эта возможность работает одинаково как в режиме HTML, так и без него.

Используйте эту функцию экономно. Лучший способ настроить перевод с помощью пользовательского переводчика. Custom Translator обеспечивает полное использование контекста и статистические значения вероятности. Если вы имеете или можете создавать учебные данные, которые показывают вашу работу или фразу в контексте, полученные результаты будут гораздо лучше. Вы можете найти более подробную информацию о пользовательском переводчике на [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
