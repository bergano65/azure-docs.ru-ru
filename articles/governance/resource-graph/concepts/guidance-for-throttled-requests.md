---
title: Руководство по регулируемым запросам
description: Научитесь группировать, шататься, paginate и запрашивать параллельно, чтобы избежать задушения запросов графиком ресурсов Azure.
ms.date: 12/02/2019
ms.topic: conceptual
ms.openlocfilehash: fbd4bec715b187bcc643fe32b8452b0e062e7713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259855"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Руководство для запросов, задушенных в графике ресурсов Azure

При создании программных и частое использование данных Azure Resource Graph следует учитывать, как регулирование влияет на результаты запросов. Изменение способа запроса данных может помочь вам и вашей организации избежать задушения и поддерживать поток своевременных данных о ресурсах Azure.

В этой статье рассматриваются четыре области и шаблоны, связанные с созданием запросов в графике ресурсов Azure:

- Общие сведения о заголовках регулирования
- Запросы группирования
- Поочередная отправка запросов
- Влияние пагинации

## <a name="understand-throttling-headers"></a>Общие сведения о заголовках регулирования

Graph ресурсAzры выделяет номер квоты для каждого пользователя на основе временного окна. Например, пользователь может отправлять не более 15 запросов в течение каждого 5-секундного окна без задушения. Значение квоты определяется многими факторами и может быть изменено.

В каждом ответе запроса Azure Resource Graph добавляет два заголовка для регулирования:

- `x-ms-user-quota-remaining`(int): Оставшаяся квота ресурсов для пользователя. Это значение соответствует количеству запросов.
- `x-ms-user-quota-resets-after`(hh:mm:ss): Продолжительность времени до сбросить потребление квоты пользователя.

Чтобы проиллюстрировать, как работают заголовки, давайте посмотрим на ответ запроса, который имеет заголовок и значения `x-ms-user-quota-remaining: 10` и `x-ms-user-quota-resets-after: 00:00:03`.

- В течение следующих 3 секунд, не более 10 запросов могут быть представлены без регулирования.
- Через 3 секунды значения `x-ms-user-quota-remaining` `x-ms-user-quota-resets-after` и будут сброситься `15` и `00:00:05` соответственно.

Чтобы увидеть пример использования заголовков для _резервного копирования_ запросов запросов, см. образец в [запросе в Parallel.](#query-in-parallel)

## <a name="grouping-queries"></a>Запросы группирования

Группировка запросов по подписке, группе ресурсов или отдельному ресурсу эффективнее, чем параллелизацию запросов. Стоимость квоты более крупного запроса зачастую ниже стоимости квоты для многих небольших и целевых запросов. Размер группы рекомендуется не менее _300._

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

- Пример #1 оптимизированного подхода к группировке

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int groupSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / groupSize; ++i)
  {
      var currSubscriptionGroup = subscriptionIds.Skip(i * groupSize).Take(groupSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionGroup,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Пример #2 оптимизированного подхода к группированию для получения нескольких ресурсов в одном запросе

  ```kusto
  Resources | where id in~ ({resourceIdGroup}) | project name, type
  ```

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int groupSize = 100;
  for (var i = 0; i <= resourceIds.Count / groupSize; ++i)
  {
      var resourceIdGroup = string.Join(",",
          resourceIds.Skip(i * groupSize).Take(groupSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIdGroup}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Поочередная отправка запросов

Из-за того, как обеспечивается регулирование, мы рекомендуем, чтобы запросы были пошатнулись. То есть вместо отправки 60 запросов одновременно, пошатнуть запросы в четыре 5-секундных окна:

- Расписание запросов без застаханий

  | Число запросов         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Интервал времени (сек) | 0-5 | 5-10 | 10-15 | 15-20 |

- Расписание застых запросов

  | Число запросов         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Интервал времени (сек) | 0-5 | 5-10 | 10-15 | 15-20 |

Ниже приведен пример соблюдения заголовок регулирования при запросе графика ресурсов Azure:

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

### <a name="query-in-parallel"></a>Запрос в параллельном

Несмотря на то, что группировка рекомендуется в течение параллелизации, бывают случаи, когда запросы не могут быть легко сгруппированы. В этих случаях можно заставить запрашивать график ресурсов Azure, отправив несколько запросов параллельно. Ниже приведен пример того, как _отступить_ на основе регулирования заголовки в таких сценариях:

```csharp
IEnumerable<IEnumerable<string>> queryGroup = /* Groups of queries  */
// Run groups in parallel.
await Task.WhenAll(queryGroup.Select(ExecuteQueries)).ConfigureAwait(false);

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

Поскольку график ресурсов Azure возвращает не более 1000 записей в одном ответе запроса, возможно, потребуется [paginate](./work-with-data.md#paging-results) ваши запросы, чтобы получить полный набор данных, который вы ищете. Однако некоторые клиенты Azure Resource Graph обрабатывают pagination иначе, чем другие.

- Пакет SDK для C#

  При использовании ResourceGraph SDK необходимо обрабатывать pagination, передавая токен skip, возвращающийся из предыдущего ответа запроса на следующий paginated query. Эта конструкция означает, что вам нужно собрать результаты от всех paginated вызовов и объединить их вместе в конце. В этом случае каждый paginated запрос, который вы отправляете, требует одну квоту запроса:

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

- Azure CLI / Azure PowerShell

  При использовании Azure CLI или Azure PowerShell запросы в График ресурсов Azure автоматически запрашиваются для получения не более 5000 записей. Результаты запроса возвращают комбинированный список записей из всех paginated вызовов. В этом случае, в зависимости от количества записей в результате запроса, один paginated запрос может потреблять более одной квоты запроса. Например, в приведенном ниже примере один запуск запроса может потреблять до пяти квот запросов:

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>Все еще задушить?

Если вы получаете задушил после осуществления вышеуказанных [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com)рекомендаций, свяжитесь с группой по .

Предоставьте следующие сведения:

- Ваш конкретный случай использования и бизнес-драйвер нуждается в более высоком пределе регулирования.
- Сколько ресурсов у вас есть доступ? Сколько из возвращенных из одного запроса?
- Какие ресурсы вас интересуют?
- Какой у вас шаблон запроса? X запросы на Y секунд и т.д.

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь с языком, который используется в [запросах Starter.](../samples/starter.md)
- Просмотрите расширенные запросы в [Расширенных запросах.](../samples/advanced.md)
- Узнайте больше о том, как [изучать ресурсы](explore-resources.md).