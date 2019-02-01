---
title: Атрибуты сущности области исследований — Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Ознакомьтесь с атрибутами, которые можно использовать совместно с сущностью области исследований в Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: 793b35d9c6412c40a87f3f91fcd772476d57584f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154573"
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
