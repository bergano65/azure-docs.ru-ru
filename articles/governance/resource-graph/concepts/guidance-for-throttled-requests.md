---
title: Руководство по регулируемые запросы
description: Узнайте, как создавать запросы, лучше избегать запросов, чтобы график ресурсов Azure из регулируется.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: c644d230846d9c644c3845348431eef36c8279c8
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276903"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Руководство по регулируемые запросы в график ресурсов Azure

При создании программный и частое использование данных график ресурсов Azure, необходимо рассмотреть возможность как регулирования последствий результаты запросов. Изменение данных запрашивается, помогут вам и вашей организации не вызвать ограничение и управления потоком ввода данных о ресурсах Azure.

В этой статье рассматриваются четыре области и шаблонов, связанных с созданием запросов в Graph ресурсов Azure:

- Понять регулирования заголовки
- Пакетные запросы
- Огромным количеством запросов
- Влияние разбиение на страницы

## <a name="understand-throttling-headers"></a>Понять регулирования заголовки

График Azure ресурсов выделяет номер квоты для каждого пользователя, в зависимости от определенного интервала. Например пользователь может отправить не более 15 запросов в пределах окна каждые 5 секунд не регулируется. Значение квоты определяется множество факторов и подлежит изменению.

В каждом ответе на запрос график ресурсов Azure добавляет два заголовка регулирования:

- `x-ms-user-quota-remaining` (целое число): оставшаяся квота ресурсов для пользователя. Это значение соответствует количеству запросов.
- `x-ms-user-quota-resets-after` (чч:мм:сс): Период времени, пока потребление квоты пользователя будет сброшен.

Чтобы проиллюстрировать, как работают заголовки, давайте взглянем на ответ на запрос, который содержит заголовок и значения `x-ms-user-quota-remaining: 10` и `x-ms-user-quota-resets-after: 00:00:03`.

- В следующих 3 секунд не более 10 запросов может отправляться не регулируется.
- В 3 секунды, значения `x-ms-user-quota-remaining` и `x-ms-user-quota-resets-after` будут сброшены до `15` и `00:00:05` соответственно.

Пример использования заголовков для _выдержкой_ на запросы, ознакомьтесь с примером в [запроса в параллельном режиме](#query-in-parallel).

## <a name="batching-queries"></a>Пакетные запросы

Пакетные запросы по подписке, группе ресурсов или отдельного ресурса является более эффективным, чем параллелизации запросов. Стоимость квоты большего размера запроса часто меньше затрат на квоту многих небольших и целевых запросов. Размер пакета, рекомендуемых для менее чем _300_.

- Пример за плохо оптимизированного подхода

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- Пример #1 оптимизированный алгоритм пакетной обработки

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int batchSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / batchSize; ++i)
  {
      var currSubscriptionBatch = subscriptionIds.Skip(i * batchSize).Take(batchSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionBatch,
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Пример #2 оптимизированный алгоритм пакетной обработки

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int batchSize = 100;
  for (var i = 0; i <= resourceIds.Count / batchSize; ++i)
  {
      var resourceIdBatch = string.Join(",",
          resourceIds.Skip(i * batchSize).Take(batchSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"where id in~ ({resourceIds}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Огромным количеством запросов

Из-за способа регулирования применяется принудительно, мы рекомендуем запросы сервиса. То есть вместо того чтобы отправлять 60 запросов в то же время, управлять временем запросы в четыре окна 5 секунд:

- Расписание не в шахматном порядке запроса

  | Число запросов         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Временной интервал (сек) | 0-5 | 5-10 | 10-15 | 15-20 |

- По ширине расписание запроса

  | Число запросов         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Временной интервал (сек) | 0-5 | 5-10 | 10-15 | 15-20 |

Ниже приведен пример соблюдение регулирования заголовков, при запросе график ресурсов Azure.

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>Запросы в параллельном режиме

Несмотря на то, что Пакетная обработка предпочтительнее, чем параллелизации, бывают случаи где запросы нельзя производить пакетную обработку легко. В этих случаях может потребоваться запросить график ресурсов Azure, отправляя несколько запросов параллельно. Ниже приведен пример того, как _выдержкой_ основании регулирования заголовки в таких случаях:

```csharp
IEnumerable<IEnumerable<string>> queryBatches = /* Batches of queries  */
// Run batches in parallel.
await Task.WhenAll(queryBatches.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);
        
        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>Разбиение на страницы

Так как график ресурсов Azure возвращает не более 1000 записей в ответ на один запрос, может потребоваться [разбивать на страницы](./work-with-data.md#paging-results) запросы, чтобы получить полный набор данных, вы ищете. Тем не менее некоторые клиенты график ресурсов Azure обрабатывать разбиение на страницы по-другому, чем другие.

- Пакет SDK для C#

  При использовании пакета SDK графике ресурсов, необходимо обрабатывать разбиение на страницы, передав токен пропуска, возвращаемых из предыдущего ответа запроса на следующий запрос с разбиением на страницы. Такой подход означает, что вам нужно собрать результаты по всем вызовам с разбивкой на страницы и объединить их в конце. В этом случае каждый запрос с разбиением на страницы, отправляемым принимает один запрос квоты:

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "project id, name, type | top 5000");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

- Azure CLI или Azure PowerShell

  При использовании Azure CLI или Azure PowerShell, запросы на график ресурсов Azure автоматически с разбиением на страницы для выборки не более 5000 записей. Результаты запроса возвращает объединенный список записей по всем вызовам с разбивкой на страницы. В этом случае в зависимости от количества записей в результатах запроса, один запрос с разбиением на страницы может потребовать более одного запроса квоты. Например в приведенном ниже примере одного запуска запроса может использовать до пяти запроса квоты:

  ```azurecli-interactive
  az graph query -q 'project id, name, type' -top 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'project id, name, type' -Top 5000
  ```

## <a name="still-get-throttled"></a>По-прежнему выполняется регулирование?

Если вам регулирование после выполнения вышеприведенных рекомендаций, обратитесь в службу в [ resourcegraphsupport@microsoft.com ](mailto:resourcegraphsupport@microsoft.com).

Укажите такие сведения:

- Конкретный драйвер вариантов использования и бизнес-требования выше предела регулирования.
- Количество ресурсов, у вас есть доступ к? Сколько они возвращаются из одного запроса?
- Типы ресурсов, вас интересует?
- Что такое оптимизировать схему запросов? X запросов в секунду Y и т.д.

## <a name="next-steps"></a>Дальнейшие действия

- См. в разделе язык используется в [запросы Starter](../samples/starter.md).
- См. Дополнительные использует в [расширенных запросов](../samples/advanced.md).
- Узнайте, как [ознакомьтесь с ресурсами](explore-resources.md).