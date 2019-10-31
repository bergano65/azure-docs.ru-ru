---
title: Динамический словарь API перевода текстов
titleSuffix: Azure Cognitive Services
description: Использование функции динамического словаря API перевода текстов.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: c268616c015c1c8735b2bdddc057f235d02cdc2a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161742"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Использование динамического словаря

Если вы уже знаете перевод, который хотите применить к слову или фразе, вы можете указать его в запросе как исправление. Динамический словарь безопасен только для составных существительных, таких как имена собственные и названия продуктов.

**Синтаксис**

< мстранс: словарный перевод = "перевод фразы" > фраза </мстранс: dictionary >

**Необходимость**

* Языки `From` и `To` должны быть разными. 
* Необходимо включить параметр `From` в запрос перевода API, а не использовать функцию автоматического обнаружения. 

**Пример: en-de:**

Источник входных данных: Слово <mstrans: dictionary translation = \"wordomatic\"> слово или фраза </mstrans: dictionary> — это словарная запись.

Целевые выходные данные: ein ist Das Wort "wordomatic" Wörterbucheintrag.

Эта возможность работает одинаково как в режиме HTML, так и без него.

Компонент должен использоваться только в случае необходимости. Соответствующий и гораздо лучший способ настройки перевода — это использование концентратора Custom Translator. Custom Translator обеспечивает полное использование контекста и статистические значения вероятности. Если вы имеете или можете создавать учебные данные, которые показывают вашу работу или фразу в контексте, полученные результаты будут гораздо лучше. Дополнительные сведения о Custom Translator можно найти по ссылке [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
