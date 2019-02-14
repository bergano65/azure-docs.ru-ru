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
ms.openlocfilehash: 183a307159adb5dfdb248eb0cf4862462a626db6
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879196"
---
# <a name="conference-instance-entity"></a>Экземпляр сущности конференции

<sub> *Следующие атрибуты относятся к экземпляру сущности конференции. (Ty = '4') </sub>

ИМЯ    |ОПИСАНИЕ                            |type       | Операции
------- | ------------------------------------- | --------- | ----------------------------
Идентификатор      |Идентификатор сущности                              |Int64      |Равно
CIN     |Нормализованное имя экземпляра конференции ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |Строка     |Равно
DCN     |Отображаемое имя экземпляра конференции ({ConferenceSeriesName} : {ConferenceInstanceYear})       |Строка     |Нет
CIL     |Расположение экземпляра конференции    |Строка     |Равно,<br/>StartsWith;
CISD    |Дата начала экземпляра конференции  |Дата       |Равно,<br/>IsBetween
CIED    |Дата окончания экземпляра конференции    |Дата       |Равно,<br/>IsBetween
CIARD   |Дата выполнения абстрактной регистрации экземпляра конференции  |Дата       |Равно,<br/>IsBetween
CISDD   |Дата выполнения отправки экземпляра конференции     |Дата       |Равно,<br/>IsBetween
CIFVD   |Дата выполнения окончательной версии экземпляра конференции  |Дата       |Равно,<br/>IsBetween
CINDD   |Дата уведомления экземпляра конференции   |Дата       |Равно,<br/>IsBetween
CD.T    |Заголовок события экземпляра конференции   |Дата       |Равно,<br/>IsBetween
CD.D    |Дата события экземпляра конференции    |Дата       |Равно,<br/>IsBetween
PCS.CN  |Имя ряда конференции экземпляра |Строка     |Равно
PCS.CId |Идентификатор ряда конференции экземпляра |Int64    |Равно
CC      |Общее количество цитируемых экземпляров конференции           |Int32      |Нет  
ECC     |Общее количество подсчитанного цитирования экземпляров конференции |Int32      |Нет


## <a name="extended-metadata-attributes"></a>Расширенные атрибуты метаданных ##

ИМЯ    | ОПИСАНИЕ               
--------|---------------------------    
FN      | Полное имя экземпляра конференции
