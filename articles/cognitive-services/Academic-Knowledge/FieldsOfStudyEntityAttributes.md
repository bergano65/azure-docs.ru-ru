---
title: Атрибуты сущности области исследований — Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Ознакомьтесь с атрибутами, которые можно использовать совместно с сущностью области исследований в Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: d54f3c72462e6702b09068092b7c18ea50f12048
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704996"
---
# <a name="field-of-study-entity"></a>Сущность области исследований

<sub> *Следующие атрибуты относятся к сущности области исследований. (Ty = '6') </sub>

Имя    |Описание                            |Тип       | Операции
------- | ------------------------------------- | --------- | ----------------------------
Id      |Идентификатор сущности                              |Int64      |Равно
FN      |Нормализованное имя области исследований         |Строковое     |Равно
DFN     |Отображаемое имя области исследований            |Строковое     |none
Копия      |Общий показатель цитируемости области исследований    |Int32      |none  
ECC     |Общий расчетный показатель цитируемости области исследований|Int32      |none
FL      |Уровень в иерархии в области исследований     |Int32      |Равно, <br/>IsBetween
FP.FN   |Имя родительской области исследований             |Строковое     |Равно
FP.FId  |Идентификатор родительской области исследований               |Int64      |Равно
FC.FN   |Имя дочерней области исследований              |Строковое     |Равно
FC.FId  |Идентификатор дочерней области исследований                |Int64      |Равно
