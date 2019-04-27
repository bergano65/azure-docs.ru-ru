---
title: Атрибуты авторских сущностей Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Изучите атрибуты, которые вы можете использовать с помощью авторской сущности в Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: d4b33c06ab023023aadf403cf0ef0b08c2bafc5f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60609760"
---
# <a name="author-entity"></a>Авторские сущности
<sub> *Следующие атрибуты относятся к авторским сущностям. (Ty = '1') </sub>

ИМЯ    |ОПИСАНИЕ                            |type       | Операции
------- | ------------------------------------- | --------- | ----------------------------
Идентификатор      |Идентификатор сущности                              |Int64      |Равно
AuN     |Нормализованное имя автора                 |String     |Равно
DAuN    |Отображаемое имя автора                    |String     |нет
CC      |Общий показатель цитируемости автора            |Int32      |нет  
ECC     |Общий расчетный показатель цитируемости автора  |Int32      |нет
E       |Расширенные метаданные (см. табл. "Атрибуты расширенных метаданных")  |String     |нет  


## <a name="extended-metadata-attributes"></a>Расширенные атрибуты метаданных ##

ИМЯ    | ОПИСАНИЕ               
--------|---------------------------    
LKA.Afn     | отображаемое имя принадлежности, связанной с автором  
LKA.AfId        | идентификатор сущности принадлежности, связанной с автором
