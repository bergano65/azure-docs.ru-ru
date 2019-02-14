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
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864372"
---
# <a name="affiliation-entity"></a>Сущность принадлежности

<sub> *Следующие атрибуты относятся к сущностям принадлежности. (Ty = '5') </sub>

ИМЯ    |ОПИСАНИЕ                            |type       | Операции
------- | ------------------------------------- | --------- | ----------------------------
Идентификатор      |Идентификатор сущности                              |Int64      |Равно
AfN     |Нормализованное имя принадлежности        |Строка     |Равно
DAfN    |Отображаемое имя принадлежности       |Строка     |Нет
CC      |Общий показатель цитируемости принадлежности           |Int32      |Нет  
ECC     |Общий расчетный показатель цитируемости принадлежности |Int32      |Нет

## <a name="extended-metadata-attributes"></a>Расширенные атрибуты метаданных ##

ИМЯ    | ОПИСАНИЕ               
--------|---------------------------    
PC      |Количество публикаций по принадлежности
