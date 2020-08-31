---
title: Единицы запросов в цифровом двойников Azure
titleSuffix: Azure Digital Twins
description: Основные сведения о расчете количества единиц запросов в Azure Digital двойников
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: c68cb8cc0ecf759b9af0e313e09663cdbc327917
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89067711"
---
# <a name="query-units-in-azure-digital-twins"></a>Единицы запросов в цифровом двойников Azure 

Модуль запроса цифровых двойников Azure **(qu)** — это единица вычислений по требованию, которая используется для выполнения [запросов к цифровым двойников Azure](how-to-query-graph.md) с помощью [API запросов](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query). 

Он абстрагирует системные ресурсы, такие как ЦП, операции ввода-вывода и память, необходимые для выполнения операций запросов, поддерживаемых цифровым двойников Azure, что позволяет отключать использование в единицах запросов.

Количество единиц запросов, потребляемых для выполнения запроса, зависит от...
* сложность запроса 
* размер результирующего набора (поэтому запрос, возвращающий 10 результатов будет потреблять больше кус, чем запрос подобной сложности, возвращающей только один результат).

В этой статье объясняется, как определить единицы запросов и отслеживание потребления единиц запросов.

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>Поиск потребления единиц запросов в Azure Digital двойников 

При выполнении запроса с помощью [API запросов](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query)к цифровым двойников Azure можно просмотреть заголовок ответа, чтобы отвести количество кус, потребляемых запросом. В ответе, отправленном из Azure Digital двойников, выполните поиск по запросу "плата за запрос". 

[Пакеты SDK](how-to-use-apis-sdks.md) для цифровых двойников Azure позволяют извлекать заголовку запроса из отклика с поддержкой страничных запросов. В этом разделе показано, как запросить цифровые двойников и как выполнить итерацию по отбираемому отклику, чтобы извлечь заголовок расхода запросов. 

В следующем фрагменте кода показано, как можно извлечь расходы на запрос, вызванные при вызове API запроса. Сначала он перебирает страницы ответа, чтобы получить доступ к заголовку запроса, а затем просматривает результаты цифровых двойника в каждой странице. 
 
```csharp
AsyncPageable<string> asyncPageableResponseWithCharge = client.QueryAsync("SELECT * FROM digitaltwins"); 
int pageNum = 0; 

// The "await" keyword here is required, as a call is made when fetching a new page. 

await foreach (Page<string> page in asyncPageableResponseWithCharge.AsPages()) 
{ 
    Console.WriteLine($"Page {++pageNum} results:"); 

    // Extract the query-charge header from the page 

    if (QueryChargeHelper.TryGetQueryCharge(page, out float queryCharge)) 
    { 
        Console.WriteLine($"Query charge was: {queryCharge}"); 
    } 

    // Iterate over the twin instances. 

    // The "await" keyword is not required here, as the paged response is local. 

    foreach (string response in page.Values) 
    { 
        BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(response); 
        Console.WriteLine($"Found digital twin '{twin.Id}'"); 
    } 
} 
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о запросах Azure Digital двойников см. по адресу:
* [*Основные понятия: язык запросов*](concepts-query-language.md)
* [*Пошаговое руководство. запрос графа двойника*](how-to-query-graph.md)
* [Справочная документация по API запроса](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query/querytwins)

Базовые ограничения для запросов Azure Digital двойников см. в статье [*ограничения службы в общедоступной предварительной версии*](reference-service-limits.md).