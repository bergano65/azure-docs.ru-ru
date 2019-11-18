---
title: Атрибуты сущности области исследований — Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Ознакомьтесь с атрибутами, которые можно использовать совместно с сущностью области исследований в Academic Knowledge API.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: b6bb5e0fd822541a0a4c10ef5f275d46664f8e10
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146470"
---
# <a name="field-of-study-entity"></a>Сущность области исследований

> [!NOTE]
> Следующие атрибуты являются специфичными для поля сущности "исследование". (Ty = ' 6 ')

имя | ОПИСАНИЕ | введите | Операции
--- | --- | --- | ---
CC      |Общий показатель цитируемости области исследований    |Int32      |Нет  
DFN     |Отображаемое имя области исследований            |Строка,     |Нет
ECC     |Общий расчетный показатель цитируемости области исследований|Int32      |Нет
FL      |Уровень в иерархии в области исследований     |Int32      |Equals, Between
FN      |Нормализованное имя области исследований         |Строка,     |Равно
FC.FId  |Идентификатор дочерней области исследований                |Int64      |Равно
FC.FN   |Имя дочерней области исследований              |Строка,     |Равно
FP.FId  |Идентификатор родительской области исследований               |Int64      |Равно
FP.FN   |Имя родительской области исследований             |Строка,     |Равно
id      |Идентификатор сущности                              |Int64      |Равно
PC    | Поле общего числа публикаций | Int32 | Нет
