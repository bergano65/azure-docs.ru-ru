---
title: Атрибуты сущности принадлежности в Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Изучите атрибуты, которые можно использовать совместно с сущностью принадлежности в Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 177fe9da8bbe821a69eae02d89a225e5d4009331
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900484"
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