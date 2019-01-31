---
title: включение файла
description: включение файла
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 80503ad154a9fc4d01614ffd2816f9d5fd497fdb
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228913"
---
<a name="paths"></a>
## <a name="paths"></a>Пути

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>Обнаружение точек аномалий среди запрошенных точек данных временных рядов
```
POST /anomalydetection
```


#### <a name="parameters"></a>Параметры

|type|ИМЯ|ОПИСАНИЕ|Схема|
|---|---|---|---|
|**Текст**|**body**  <br>*обязательный параметр*|Точки данных временного ряда и период, если требуется.|[request](#request)|


#### <a name="responses"></a>Ответы

|Код HTTP|ОПИСАНИЕ|Схема|
|---|---|---|
|**200**|Успешная операция.|Массив объектов < [response](#response) >|
|**400**|Не удается проанализировать запрос JSON.|Нет содержимого|
|**403**|Сертификат, который вы указали, не принимается сервером.|Нет содержимого|
|**405**|Недопустимый метод.|Нет содержимого|
|**500**|Внутренняя ошибка сервера.|Нет содержимого|


#### <a name="consumes"></a>Использует

* `application/json`


#### <a name="produces"></a>Выдает

* `application/json`


#### <a name="tags"></a>Теги

* anomalydetection



