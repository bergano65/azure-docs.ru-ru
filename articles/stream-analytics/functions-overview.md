---
title: Определяемые пользователем функции в Azure Stream Analytics
description: В этой статье представлен обзор определяемых пользователем функций в Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 45e766c624ee96f7faa06fb07d00349e620a4c0a
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133485"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Определяемые пользователем функции в Azure Stream Analytics

Язык запросов, похожий на SQL, в Azure Stream Analytics упрощает реализацию логики аналитики в реальном времени для потоковой передачи данных. Stream Analytics предоставляет дополнительную гибкость через пользовательские функции, которые вызываются в запросе. Следующий пример кода представляет собой определяемую пользователем `sampleFunction` функцию, которая принимает один параметр, каждую входную запись, полученную заданием, и результат записывается в выходные данные в виде `sampleResult`.

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>Типы функций

Azure Stream Analytics поддерживает следующие четыре типа функций: 

* Определяемые пользователем функции JavaScript 
* Пользовательские статистические выражения JavaScript 
* Определяемые пользователем функции C# (с помощью Visual Studio) 
* Машинное обучение Azure 

Эти функции можно использовать для таких сценариев, как оценка в реальном времени с помощью моделей машинного обучения, операций со строками, сложных математических вычислений, кодирования и декодирования данных. 

## <a name="limitations"></a>Ограничения

Определяемые пользователем функции не имеют состояния, а возвращаемое значение может быть только скалярным значением. Вы не можете обращаться к внешним конечным точкам RESTFUL из этих определяемых пользователем функций, так как это, скорее всего, повлияет на производительность вашего задания. 

Azure Stream Analytics не сохраняет запись о всех вызовах функций и возвращаемых результатах. Для обеспечения повторяемости. Например, повторное выполнение задания из более старой метки времени приводит к тому же результату. не используйте такие функции, как `Date.GetData()` или `Math.random()`, так как эти функции не возвращают одинаковый результат для каждого вызова.  

## <a name="resource-logs"></a>Журналы ресурсов

Любые ошибки времени выполнения считаются неустранимыми и отображаются с помощью журналов действий и ресурсов. Рекомендуется, чтобы функция обрабатывала все исключения и ошибки и возвращала в запрос допустимый результат. Это предотвратит переход задания в [состояние сбоя](job-states.md).  


## <a name="next-steps"></a>Следующие шаги

* [Определяемые пользователем функции JavaScript в Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Azure Stream Analytics определяемые пользователем статистические функции JavaScript](stream-analytics-javascript-user-defined-aggregates.md)
* [Разработка .NET Standard определяемых пользователем функций для Azure Stream Analyticsных заданий](stream-analytics-edge-csharp-udf-methods.md)
* [Интеграция Azure Stream Analytics с Машинное обучение Azure](machine-learning-udf.md)

