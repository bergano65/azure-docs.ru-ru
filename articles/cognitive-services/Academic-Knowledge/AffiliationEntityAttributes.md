---
title: Атрибуты сущности принадлежности в Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Изучите атрибуты, которые можно использовать совместно с сущностью принадлежности в Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 82e6a5b66342e58e62da029d617cbd1d74c28149
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61340525"
---
# <a name="affiliation-entity"></a>Сущность принадлежности

<sub> *Следующие атрибуты относятся к сущностям принадлежности. (Ty = '5') </sub>

Name    |Описание                            |type       | Операции
------- | ------------------------------------- | --------- | ----------------------------
Идентификатор      |Идентификатор сущности                              |Int64      |Равно
AfN     |Нормализованное имя принадлежности        |String     |Равно
DAfN    |Отображаемое имя принадлежности       |String     |нет
CC      |Общий показатель цитируемости принадлежности           |Int32      |нет  
ECC     |Общий расчетный показатель цитируемости принадлежности |Int32      |нет

## <a name="extended-metadata-attributes"></a>Расширенные атрибуты метаданных ##

Name    | Описание               
--------|---------------------------    
PC      |Количество публикаций по принадлежности
