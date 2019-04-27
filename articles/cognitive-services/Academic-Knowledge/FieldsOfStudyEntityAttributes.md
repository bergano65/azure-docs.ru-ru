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
ms.openlocfilehash: e9d6badf76efd03c0520a728af7b3e47b25f200a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61339591"
---
# <a name="field-of-study-entity"></a>Сущность области исследований

<sub> *Следующие атрибуты относятся к сущности области исследований. (Ty = '6') </sub>

ИМЯ    |ОПИСАНИЕ                            |type       | Операции
------- | ------------------------------------- | --------- | ----------------------------
Идентификатор      |Идентификатор сущности                              |Int64      |Равно
FN      |Нормализованное имя области исследований         |String     |Равно
DFN     |Отображаемое имя области исследований            |String     |нет
CC      |Общий показатель цитируемости области исследований    |Int32      |нет  
ECC     |Общий расчетный показатель цитируемости области исследований|Int32      |нет
FL      |Уровень в иерархии в области исследований     |Int32      |Равно, <br/>IsBetween
FP.FN   |Имя родительской области исследований             |String     |Равно
FP.FId  |Идентификатор родительской области исследований               |Int64      |Равно
FC.FN   |Имя дочерней области исследований              |String     |Равно
FC.FId  |Идентификатор дочерней области исследований                |Int64      |Равно
