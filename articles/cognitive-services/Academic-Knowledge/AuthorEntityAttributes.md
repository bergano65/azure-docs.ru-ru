---
title: Атрибуты авторских сущностей Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Изучите атрибуты, которые вы можете использовать с помощью авторской сущности в Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 48758ac9ec8c993bbdb490229ae20fcce1fb0a49
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175185"
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
