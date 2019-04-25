---
title: Метод анализаторов — API лингвистического анализа
titlesuffix: Azure Cognitive Services
description: Analyzers REST API предоставляет ряд диагностических анализаторов, которые в настоящее время поддерживаются API лингвистического анализа.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 06/30/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 1b33b60f674eebb15fdc6112e1d630b93b98494b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60404729"
---
# <a name="analyzers-method"></a>Метод Analyzers

> [!IMPORTANT]
> Поддержка предварительной версии Лингвистического анализа прекращена 9 августа 2018 г. Мы рекомендуем использовать [модули текстовой аналитики Машинного обучения Azure](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) для анализа и обработки текста.

**Analyzers** REST API предоставляет список диагностических анализаторов, которые в настоящее время поддерживаются этой службой.
Ответ содержит их [имена](Analyzer-Names.md) и языки, поддерживаемые каждым из них (например, "en" для английского языка).

## <a name="request-parameters"></a>Параметры запроса
Нет

<br>

## <a name="response-parameters"></a>Параметры ответа

Name | Тип | ОПИСАНИЕ
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
