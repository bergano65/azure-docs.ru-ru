---
title: Метод сходства — Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Использование метода сходства, чтобы вычислить академическое сходство двух строк.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 01/18/2017
ms.author: alch
ms.openlocfilehash: 0e42d5e05268d4a572376f2bdf25d0ce6f8f4be7
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490130"
---
# <a name="similarity-method"></a>Метод сходства

**Сходство** REST API используется для вычисления академического сходства между двумя строками. 
<br>

**Конечная точка REST:**
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?
```

## <a name="request-parameters"></a>Параметры запроса
Параметр        |Тип данных      |Обязательно | ОПИСАНИЕ
----------|----------|----------|------------
**s1**        |Строка   |Yes  |Строка* для сравнения
**s2**        |Строка   |Yes  |Строка* для сравнения
<sub> *Строки для сравнения имеют максимальную длину 1 МБ. </sub>
<br>
## <a name="response"></a>Ответ
ИМЯ | ОПИСАНИЕ
--------|---------
**SimilarityScore**        |Значение с плавающей запятой, представляющее косинус,подобный s1 и s2, со значениями, близкими к 1.0, означает более похожие и близкие значения к -1.0, что означает меньше
<br>

## <a name="successerror-conditions"></a>Условия успеха или ошибки
Состояние HTTP | Причина | Ответ
-----------|----------|--------
**200**         |Успешно | Число с плавающей запятой
**400**         | Ошибочный или недопустимый запрос | Сообщение об ошибке      
**500**         |Внутренняя ошибка сервера | Сообщение об ошибке
**Истекло время ожидания**     | Истекло время ожидания для запроса.  | Сообщение об ошибке
<br>
## <a name="example-calculate-similarity-of-two-partial-abstracts"></a>Пример: рассчитать сходство двух частично выполненных тезисов
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
