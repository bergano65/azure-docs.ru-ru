---
title: Атрибуты сущности серии конференций Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Дополнительные сведения об атрибутах, которые можно использовать с сущностью серии конференций.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: feed324202f6a75ceb7e9089875b899c51cd8ae6
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705043"
---
# <a name="conference-series-entity"></a>Сущность серии конференций

<sub> *Следующие атрибуты относятся к сущности серии конференций. (Ty = '3') </sub>

Имя    |Описание                            |Тип       | Операции
------- | ------------------------------------- | --------- | ----------------------------
Id      |Идентификатор сущности                              |Int64      |Равно
CN      |Нормализованное имя серии конференций      |Строковое     |Равно
DCN     |Отображаемое имя серии конференций         |Строковое     |none
Копия      |Общий показатель цитируемости серии конференций         |Int32      |none  
ECC     |Общий расчетный показатель цитируемости серии конференций   |Int32      |none
F.FId   |Идентификатор сущности области исследований, связанный с серией конференций |Int64  | Равно
F.FN    |Имя сущности области исследований, связанное с серией конференций  | Равно,<br/>StartsWith;
