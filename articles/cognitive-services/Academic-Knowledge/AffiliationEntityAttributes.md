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
ROBOTS: NOINDEX
ms.openlocfilehash: 333875472d9b859196c4d828061301b9d32c4d5a
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705115"
---
# <a name="affiliation-entity"></a>Сущность принадлежности

<sub> *Следующие атрибуты относятся к сущностям принадлежности. (Ty = '5') </sub>

Имя    |Описание                            |Тип       | Операции
------- | ------------------------------------- | --------- | ----------------------------
Id      |Идентификатор сущности                              |Int64      |Равно
AfN     |Нормализованное имя принадлежности        |Строковое     |Равно
DAfN    |Отображаемое имя принадлежности       |Строковое     |none
Копия      |Общий показатель цитируемости принадлежности           |Int32      |none  
ECC     |Общий расчетный показатель цитируемости принадлежности |Int32      |none

## <a name="extended-metadata-attributes"></a>Расширенные атрибуты метаданных ##

Название    | Описание               
--------|---------------------------    
ПК      |Количество публикаций по принадлежности
