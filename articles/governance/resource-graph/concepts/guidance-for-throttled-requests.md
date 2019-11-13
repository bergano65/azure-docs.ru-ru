---
title: Руководство по регулируемым запросам
description: Узнайте, как создавать более совершенные запросы, чтобы избежать регулирования запросов к графу ресурсов Azure.
ms.date: 10/18/2019
ms.topic: conceptual
ms.openlocfilehash: 46af11bfea47e37b97fa9492f71be8b5fe1817e3
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73959206"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Руководство по регулируемым запросам в графе ресурсов Azure

При создании программных и часто используемых данных графа ресурсов Azure следует учитывать, как регулирование влияет на результаты запросов. Изменение способа запроса данных может помочь вам и вашей организации избежать регулирования и поддержания потока своевременных данных о ресурсах Azure.

В этой статье рассматриваются четыре области и шаблоны, связанные с созданием запросов в графе ресурсов Azure.

- Общие сведения о заголовках регулирования
- Пакетная обработка запросов
- Сдвиг запросов
- Влияние разбивки на страницы

## <a name="understand-throttling-headers"></a>Общие сведения о заголовках регулирования

Граф ресурсов Azure выделяет номер квоты для каждого пользователя в зависимости от временного интервала. Например, пользователь может отправить не более 15 запросов в течение каждого 5-секундного окна без регулирования. Значение квоты определяется многими факторами и может быть изменено.

В каждом ответе на запрос граф ресурсов Azure добавляет два заголовка регулирования:

- `x-ms-user-quota-remaining` (int): оставшиеся квоты ресурсов для пользователя. Это значение соответствует количеству запросов.
- `x-ms-user-quota-resets-after` (чч: мм: СС): период времени до сброса потребления квоты пользователем.

Чтобы проиллюстрировать работу заголовков, рассмотрим ответ на запрос с заголовком и значениями `x-ms-user-quota-remaining: 10` и `x-ms-user-quota-resets-after: 00:00:03`.

- В течение следующих 3 секунд можно отправить не более 10 запросов без регулирования.
- В течение 3 секунд значения `x-ms-user-quota-remaining` и `x-ms-user-quota-resets-after` будут сброшены в `15` и `00:00:05` соответственно.

Пример использования заголовков для отработки _запросов к_ запросам см. в примере в [Query параллельно](#query-in-parallel).

## <a name="batching-queries"></a>Пакетная обработка запросов

Пакетная обработка запросов подпиской, группой ресурсов или отдельным ресурсом более эффективна, чем параллелизации запросов. Затраты на квоту более крупного запроса часто меньше, чем квота на множество небольших и целевых запросов. Размер пакета рекомендуется меньше _300_.

- Пример плохо оптимизированного подхода

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "Resoures | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- Пример #1 оптимизированного пакетного подхода

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
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Пример #2 оптимизированного пакетного подхода

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
          query: $"Resources | where id in~ ({resourceIds}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Сдвиг запросов

Из-за способа принудительного регулирования рекомендуется использовать запросы в шахматном порядке. То есть вместо отправки запросов 60 в одно и то же время разнесите запросы в четыре 5-секундных окна:

- Расписание запросов без раскрывающегося отчета

  | Число запросов         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Интервал времени (сек) | 0-5 | 5-10 | 10-15 | 15-20 |

- Расписание запросов в шахматном порядке

  | Число запросов         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Интервал времени (сек) | 0-5 | 5-10 | 10-15 | 15-20 |

Ниже приведен пример использования заголовков регулирования при запросе к графу ресурсов Azure.

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

### <a name="query-in-parallel"></a>Запрос в параллельном режиме

Несмотря на то, что для параллелизации рекомендуется использовать пакетную обработку, бывают случаи, когда запросы не могут быть просто пакетированы. В таких случаях может потребоваться выполнить запрос к графу ресурсов Azure, отправив несколько запросов параллельно. Ниже приведен пример _того, как выполнять_ откладывание на основе заголовков регулирования в таких сценариях:

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

Так как граф ресурсов Azure возвращает не более 1000 записей в одном ответе на запрос, может потребоваться выполнить [разбиение](./work-with-data.md#paging-results) запросов на страницы, чтобы получить полный набор данных, который вы ищете. Однако некоторые клиенты графа ресурсов Azure выполняют разбиение на страницы иначе, чем другие.

- Пакет SDK для C#

  При использовании Ресаурцеграф SDK необходимо выполнить обработку разбивки на страницы, передав возвращенный токен пропуска из предыдущего ответа на запрос в следующий запрос на разбивку на страницы. Это означает, что необходимо собрать результаты из всех вызовов с разбивкой на страницы и объединить их в конце. В этом случае каждый отправленный запрос с разбивкой на страницы принимает одну квоту запроса:

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "Resources | project id, name, type | top 5000");
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

- Azure CLI и Azure PowerShell

  При использовании Azure CLI или Azure PowerShell запросы к графу ресурсов Azure автоматически размещаются, чтобы получить не более 5000 записей. Результаты запроса возвращают объединенный список записей из всех вызовов с разбивкой на страницы. В этом случае, в зависимости от числа записей в результатах запроса, один запрос разбивки на страницы может использовать более одной квоты запроса. Например, в приведенном ниже примере один запуск запроса может использовать до пяти квот запросов:

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' -top 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -Top 5000
  ```

## <a name="still-get-throttled"></a>Все еще регулируется?

Если вы регулируете приведенные выше рекомендации, обратитесь к группе по адресу [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com).

Укажите следующие сведения:

- Для вашего конкретного варианта использования и бизнес-драйвера требуется более высокий предел регулирования.
- Сколько ресурсов у вас есть доступ? Сколько из них возвращается из одного запроса?
- Какие типы ресурсов вас интересуют?
- Что такое шаблон запроса? X запросов на Y секунд и т. д.

## <a name="next-steps"></a>Дополнительная информация

- См. язык, используемый в [начальных запросах](../samples/starter.md).
- См. Дополнительные сведения о расширенном использовании в [расширенных запросах](../samples/advanced.md).
- Узнайте больше о том, как [исследовать ресурсы](explore-resources.md).