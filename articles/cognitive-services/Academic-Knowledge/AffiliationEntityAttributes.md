---
title: Атрибуты сущности принадлежности в Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Изучите атрибуты, которые можно использовать совместно с сущностью принадлежности в Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 344b26b16f74cd44982e3c93fa69295792daa9a0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190651"
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
