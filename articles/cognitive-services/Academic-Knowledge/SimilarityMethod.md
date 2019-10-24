---
title: Метод сходства — Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Использование метода сходства, чтобы вычислить академическое сходство двух строк.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 01/18/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 855d29d2c55b841bbbe4e9eadce8c29ad85fad90
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704849"
---
# <a name="similarity-method"></a>Метод сходства

**Сходство** REST API используется для вычисления академического сходства между двумя строками. 
<br>

**Конечная точка REST:**
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?
```

## <a name="request-parameters"></a>Параметры запроса

Параметр        |Тип данных      |Обязательное значение | Описание
----------|----------|----------|------------
**s1**        |Строковое   |Да  |Строка* для сравнения
**s2**        |Строковое   |Да  |Строка* для сравнения

<sub> *Строки для сравнения имеют максимальную длину 1 МБ. </sub>
<br>

## <a name="response"></a>Отклик

Название | Описание
--------|---------
**SimilarityScore**        |Значение с плавающей запятой, представляющее косинус,подобный s1 и s2, со значениями, близкими к 1.0, означает более похожие и близкие значения к -1.0, что означает меньше

<br>

## <a name="successerror-conditions"></a>Условия успеха или ошибки

Состояние HTTP | Причина | Отклик
-----------|----------|--------
**200**         |Success | Число с плавающей запятой
**400**         | Ошибочный или недопустимый запрос | Сообщение об ошибке      
**500**         |Внутренняя ошибка сервера | Сообщение об ошибке
**Истекло время ожидания**     | Истекло время ожидания для запроса.  | Сообщение об ошибке

<br>

## <a name="example-calculate-similarity-of-two-partial-abstracts"></a>Пример рассчитать сходство двух частично выполненных тезисов
#### <a name="request"></a>Запрос:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?s1=Using complementary priors, we derive a fast greedy algorithm that can learn deep directed belief networks one layer at a time, provided the top two layers form an undirected associative memory
&s2=Deepneural nets with a large number of parameters are very powerful machine learning systems. However, overfitting is a serious problem in such networks
```
В этом примере мы создаем оценку сходства двух частично выполненных тезисов с помощью API **сходства**.
#### <a name="response"></a>Ответ:
```
0.520
```
#### <a name="remarks"></a>Примечания:
Оценка сходства определяется путем оценки академических концепций посредством внедрения слов. В этом примере 0.52 означает, что два частично выполненных тезиса несколько схожи.
<br>
