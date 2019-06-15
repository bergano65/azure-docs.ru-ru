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
ms.openlocfilehash: 38b4aa4c899668a68041f042ce6981ddd8c58219
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61340202"
---
# <a name="conference-series-entity"></a>Сущность серии конференций

<sub> *Следующие атрибуты относятся к сущности серии конференций. (Ty = '3') </sub>

Name    |Описание                            |type       | Операции
------- | ------------------------------------- | --------- | ----------------------------
Идентификатор      |Идентификатор сущности                              |Int64      |Равно
CN      |Нормализованное имя серии конференций      |String     |Равно
DCN     |Отображаемое имя серии конференций         |String     |нет
CC      |Общий показатель цитируемости серии конференций         |Int32      |нет  
ECC     |Общий расчетный показатель цитируемости серии конференций   |Int32      |нет
F.FId   |Идентификатор сущности области исследований, связанный с серией конференций |Int64  | Равно
F.FN    |Имя сущности области исследований, связанное с серией конференций  | Равно,<br/>StartsWith;
