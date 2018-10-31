---
title: Динамический словарь API перевода текстов
titlesuffix: Azure Cognitive Services
description: Использование функции динамического словаря API перевода текстов.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 0b96325b2d29abd230e4c389b176e97542a70282
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648253"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-translator-text-api"></a>Использование функции динамического словаря API перевода текстов

Если вы уже знаете перевод, который хотите применить к слову или фразе, вы можете указать его в запросе как исправление. Динамический словарь безопасен только для составных существительных, таких как имена собственные и названия продуктов.

**Синтаксис**

<mstrans:dictionary translation="перевод фразы">фраза</mstrans:dictionary>

**Пример: en-de:**

Источник входных данных: Слово <mstrans: dictionary translation = \"wordomatic\"> слово или фраза </mstrans: dictionary> — это словарная запись.

Целевые выходные данные: ein ist Das Wort "wordomatic" Wörterbucheintrag.

Эта возможность работает одинаково как в режиме HTML, так и без него.

Компонент должен использоваться только в случае необходимости. Соответствующий и гораздо лучший способ настройки перевода — это использование концентратора Custom Translator. Custom Translator обеспечивает полное использование контекста и статистические значения вероятности. Если вы имеете или можете создавать учебные данные, которые показывают вашу работу или фразу в контексте, полученные результаты будут гораздо лучше. Дополнительные сведения о Custom Translator можно найти по ссылке [http://aka.ms/CustomTranslator](http://aka.ms/CustomTranslator).
