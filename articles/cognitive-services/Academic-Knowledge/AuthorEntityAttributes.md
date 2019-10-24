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
ROBOTS: NOINDEX
ms.openlocfilehash: e63e9d3f8f17a2473caf3d31b83e318ddb132b43
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705089"
---
# <a name="author-entity"></a>Авторские сущности
<sub> *Следующие атрибуты относятся к авторским сущностям. (Ty = '1') </sub>

Название    |Описание                            |Type       | Операции
------- | ------------------------------------- | --------- | ----------------------------
Id      |Идентификатор сущности                              |Int64      |Равно
AuN     |Нормализованное имя автора                 |Строковое     |Равно
DAuN    |Отображаемое имя автора                    |Строковое     |none
Копия      |Общий показатель цитируемости автора            |Int32      |none  
ECC     |Общий расчетный показатель цитируемости автора  |Int32      |none
E       |Расширенные метаданные (см. табл. "Атрибуты расширенных метаданных")  |Строковое     |none  


## <a name="extended-metadata-attributes"></a>Расширенные атрибуты метаданных ##

Название    | Описание               
--------|---------------------------    
LKA.Afn     | отображаемое имя принадлежности, связанной с автором  
LKA.AfId        | идентификатор сущности принадлежности, связанной с автором
