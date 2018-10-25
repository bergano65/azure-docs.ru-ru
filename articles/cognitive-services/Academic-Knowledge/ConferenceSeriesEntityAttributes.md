---
title: Атрибуты сущности серии конференций Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Дополнительные сведения об атрибутах, которые можно использовать с сущностью серии конференций.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: f9f28afd7005d7a61aa0d2f4dba69ca598034b52
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900754"
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