---
title: Атрибуты авторских сущностей Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Изучите атрибуты, которые вы можете использовать с помощью авторской сущности в Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 62e6da6d558a0494fb83115b1e307081099399d4
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900695"
---
# <a name="author-entity"></a>Авторские сущности
<sub> *Следующие атрибуты относятся к авторским сущностям. (Ty = '1') </sub>

ИМЯ    |ОПИСАНИЕ                            |type       | Операции
------- | ------------------------------------- | --------- | ----------------------------
Идентификатор      |Идентификатор сущности                              |Int64      |Равно
AuN     |Нормализованное имя автора                 |Строка     |Равно
DAuN    |Отображаемое имя автора                    |Строка     |Нет
CC      |Общий показатель цитируемости автора            |Int32      |Нет  
ECC     |Общий расчетный показатель цитируемости автора  |Int32      |Нет
E       |Расширенные метаданные (см. табл. "Атрибуты расширенных метаданных")  |Строка     |Нет  


## <a name="extended-metadata-attributes"></a>Расширенные атрибуты метаданных ##

ИМЯ    | ОПИСАНИЕ               
--------|---------------------------    
LKA.Afn     | отображаемое имя принадлежности, связанной с автором  
LKA.AfId        | идентификатор сущности принадлежности, связанной с автором