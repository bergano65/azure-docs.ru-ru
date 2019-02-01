---
title: Атрибуты сущности серии конференций Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Дополнительные сведения об атрибутах, которые можно использовать с сущностью серии конференций.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: ff71b489cce01d8d6ea29e09905d7d3ac8429e34
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155612"
---
# <a name="conference-series-entity"></a>Сущность серии конференций

<sub> *Следующие атрибуты относятся к сущности серии конференций. (Ty = '3') </sub>

ИМЯ    |ОПИСАНИЕ                            |type       | Операции
------- | ------------------------------------- | --------- | ----------------------------
Идентификатор      |Идентификатор сущности                              |Int64      |Равно
CN      |Нормализованное имя серии конференций      |Строка     |Равно
DCN     |Отображаемое имя серии конференций         |Строка     |Нет
CC      |Общий показатель цитируемости серии конференций         |Int32      |Нет  
ECC     |Общий расчетный показатель цитируемости серии конференций   |Int32      |Нет
F.FId   |Идентификатор сущности области исследований, связанный с серией конференций |Int64  | Равно
F.FN    |Имя сущности области исследований, связанное с серией конференций  | Равно,<br/>StartsWith;
