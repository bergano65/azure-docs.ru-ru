---
title: Метод анализаторов — API лингвистического анализа
titlesuffix: Azure Cognitive Services
description: Analyzers REST API предоставляет ряд диагностических анализаторов, которые в настоящее время поддерживаются API лингвистического анализа.
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: conceptual
ms.date: 06/30/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 762ebf50999a88251dcd05824f2ed450cec97f04
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237405"
---
# <a name="analyzers-method"></a>Метод Analyzers

> [!IMPORTANT]
> Поддержка предварительной версии Лингвистического анализа прекращена 9 августа 2018 г. Мы рекомендуем использовать [модули текстовой аналитики Машинного обучения Azure](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) для анализа и обработки текста.

**Analyzers** REST API предоставляет список диагностических анализаторов, которые в настоящее время поддерживаются этой службой.
Ответ содержит их [имена](Analyzer-Names.md) и языки, поддерживаемые каждым из них (например, "en" для английского языка).

## <a name="request-parameters"></a>Параметры запроса
None

<br>

## <a name="response-parameters"></a>Параметры ответа
ИМЯ | type | ОПИСАНИЕ
-----|------|--------------
языки | Список строк | список двухбуквенных ISO-кодов языка, для которых этот диагностический анализатор можно использовать.
id   | строка | уникальный идентификатор для этого анализатора
kind | строка | здесь широкий шрифт анализатора
спецификация | строка | имя спецификации, используемой для этого анализатора
реализация | строка | описание модели и/или алгоритм этого анализатора

<br>
## <a name="example"></a>Пример
GET/Analyzers

Ответ: JSON
```json
[
    {
        "id": "22A6B758-420F-4745-8A3C-46835A67C0D2",
        "languages": ["en"],
        "kind": "Constituency_Tree",  
        "specification": "PennTreebank3",
        "implementation": "SplitMerge"
    },
    {
        "id" : "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
        "languages": ["en"],
        "kind": "POS_Tags",
        "specification": "PennTreebank3",
        "implementation": "cmm"
    },
    {
        "id" : "08EA174B-BFDB-4E64-987E-602F85DA7F72",
        "languages": ["en"],
        "kind": "Tokens",
        "specification":"PennTreebank3",
        "implementation": "regexes"
    }
]
```
