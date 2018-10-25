---
title: Атрибуты сущности области исследований — Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Ознакомьтесь с атрибутами, которые можно использовать совместно с сущностью области исследований в Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: 862fd6d506d5f1ca6f7f532f80f53a29200f33db
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900433"
---
# <a name="field-of-study-entity"></a>Сущность области исследований

<sub> *Следующие атрибуты относятся к сущности области исследований. (Ty = '6') </sub>

ИМЯ    |ОПИСАНИЕ                            |type       | Операции
------- | ------------------------------------- | --------- | ----------------------------
Идентификатор      |Идентификатор сущности                              |Int64      |Равно
FN      |Нормализованное имя области исследований         |Строка     |Равно
DFN     |Отображаемое имя области исследований            |Строка     |Нет
CC      |Общий показатель цитируемости области исследований    |Int32      |Нет  
ECC     |Общий расчетный показатель цитируемости области исследований|Int32      |Нет
FL      |Уровень в иерархии в области исследований     |Int32      |Равно, <br/>IsBetween
FP.FN   |Имя родительской области исследований             |Строка     |Равно
FP.FId  |Идентификатор родительской области исследований               |Int64      |Равно
FC.FN   |Имя дочерней области исследований              |Строка     |Равно
FC.FId  |Идентификатор дочерней области исследований                |Int64      |Равно