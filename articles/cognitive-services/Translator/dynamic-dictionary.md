---
title: Динамический словарь API перевода текстов
titlesuffix: Azure Cognitive Services
description: Использование функции динамического словаря API перевода текстов.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-pawal
ms.openlocfilehash: 72c23270d6e8cdef55f07d0b702fd1858a7710f8
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389553"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-translator-text-api"></a>Использование функции динамического словаря API перевода текстов

Если вы уже знаете перевод, который хотите применить к слову или фразе, вы можете указать его в запросе как исправление. Динамический словарь безопасен только для составных существительных, таких как имена собственные и названия продуктов.

**Синтаксис**

<mstrans:dictionary translation="перевод фразы">фраза</mstrans:dictionary>

**Пример: en-de:**

Исходные входные данные: The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.

Целевые выходные данные: Das Wort "wordomatic" ist ein Wörterbucheintrag.

Эта возможность работает одинаково как в режиме HTML, так и без него.

Компонент должен использоваться только в случае необходимости. Соответствующий и гораздо лучший способ настройки перевода — это использование концентратора Custom Translator. Custom Translator обеспечивает полное использование контекста и статистические значения вероятности. Если вы имеете или можете создавать учебные данные, которые показывают вашу работу или фразу в контексте, полученные результаты будут гораздо лучше. Дополнительные сведения о Custom Translator можно найти по ссылке [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
