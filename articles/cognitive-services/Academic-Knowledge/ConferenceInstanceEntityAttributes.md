---
title: Атрибуты сущности экземпляра конференций Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Изучите атрибуты, которые вы можете использовать с экземпляром сущности конференции в Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 37a353fbb86ca199b2316dcfba5904f4b46b0276
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705056"
---
# <a name="conference-instance-entity"></a>Экземпляр сущности конференции

<sub> *Следующие атрибуты относятся к экземпляру сущности конференции. (Ty = '4') </sub>

Имя    |Описание                            |Type       | Операции
------- | ------------------------------------- | --------- | ----------------------------
Id      |Идентификатор сущности                              |Int64      |Равно
CIN     |Нормализованное имя экземпляра конференции ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |Строковое     |Равно
DCN     |Отображаемое имя экземпляра конференции ({ConferenceSeriesName} : {ConferenceInstanceYear})       |Строковое     |none
CIL     |Расположение экземпляра конференции    |Строковое     |Равно,<br/>StartsWith;
CISD    |Дата начала экземпляра конференции  |Дата       |Равно,<br/>IsBetween
CIED    |Дата окончания экземпляра конференции    |Дата       |Равно,<br/>IsBetween
CIARD   |Дата выполнения абстрактной регистрации экземпляра конференции  |Дата       |Равно,<br/>IsBetween
CISDD   |Дата выполнения отправки экземпляра конференции     |Дата       |Равно,<br/>IsBetween
CIFVD   |Дата выполнения окончательной версии экземпляра конференции  |Дата       |Равно,<br/>IsBetween
CINDD   |Дата уведомления экземпляра конференции   |Дата       |Равно,<br/>IsBetween
CD.T    |Заголовок события экземпляра конференции   |Дата       |Равно,<br/>IsBetween
CD.D    |Дата события экземпляра конференции    |Дата       |Равно,<br/>IsBetween
PCS.CN  |Имя ряда конференции экземпляра |Строковое     |Равно
PCS.CId |Идентификатор ряда конференции экземпляра |Int64    |Равно
Копия      |Общее количество цитируемых экземпляров конференции           |Int32      |none  
ECC     |Общее количество подсчитанного цитирования экземпляров конференции |Int32      |none


## <a name="extended-metadata-attributes"></a>Расширенные атрибуты метаданных ##

Имя    | Описание               
--------|---------------------------    
FN      | Полное имя экземпляра конференции
