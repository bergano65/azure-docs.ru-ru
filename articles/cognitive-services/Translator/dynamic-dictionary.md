---
title: Динамический словарь API перевода текстов
titlesuffix: Azure Cognitive Services
description: Использование функции динамического словаря API перевода текстов.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 2ef1e474dd5d36f1967501ea7bdedc4736954a2b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67436009"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Как использовать динамический словарь

Если вы уже знаете перевод, который хотите применить к слову или фразе, вы можете указать его в запросе как исправление. Динамический словарь безопасен только для составных существительных, таких как имена собственные и названия продуктов.

**Синтаксис**

<mstrans:dictionary translation="перевод фразы">фраза</mstrans:dictionary>

**Пример: en-de:**

Исходные входные данные: The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.

Целевые выходные данные: Das Wort "wordomatic" ist ein Wörterbucheintrag.

Эта возможность работает одинаково как в режиме HTML, так и без него.

Компонент должен использоваться только в случае необходимости. Соответствующий и гораздо лучший способ настройки перевода — это использование концентратора Custom Translator. Custom Translator обеспечивает полное использование контекста и статистические значения вероятности. Если вы имеете или можете создавать учебные данные, которые показывают вашу работу или фразу в контексте, полученные результаты будут гораздо лучше. Дополнительные сведения о Custom Translator можно найти по ссылке [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
