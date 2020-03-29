---
title: Работа с большими наборами данных
description: Поймите, как получать, форматировать, страницу и пропускать записи в больших наборах данных во время работы с графиком ресурсов Azure.
ms.date: 03/20/2020
ms.topic: conceptual
ms.openlocfilehash: be15a6234935627ca748276e6330c50c3ee5a775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064738"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Работа с большими наборами данных ресурса Azure

Azure Resource Graph предназначен для работы и получения сведений о ресурсах в вашей среде Azure. Resource Graph дает возможность получать эти данные быстро, даже при запросе тысяч записей. Resource Graph имеет несколько параметров для работы с этими большими наборами данных.

Для руководства по работе с запросами на высокой частоте [см.](./guidance-for-throttled-requests.md)

## <a name="data-set-result-size"></a>Размер результата набора данных

По умолчанию Resource Graph ограничивает любой запрос возвращением до **100** записей. Этот элемент управления защищает как пользователя, так и службу от непреднамеренных запросов, которые могут привести к большим наборам данных. Это событие чаще всего происходит, когда клиент экспериментирует с запросами, чтобы найти и отфильтровать ресурсы в соответствии с их конкретными потребностями. Этот элемент управления отличается от использования операторов языка Azure Data Explorer [top](/azure/kusto/query/topoperator) или [limit](/azure/kusto/query/limitoperator) для ограничения результатов.

> [!NOTE]
> При использовании **First**рекомендуется заказывать результаты по крайней мере одним столбцом с `asc` или `desc`. Без сортировки возвращенные результаты являются случайными и не повторяются.

Ограничение по умолчанию может быть отменено всеми методами взаимодействия с Resource Graph. Следующие примеры демонстрируют изменение ограничения размера набора данных на _200_.

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

В [REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources) элемент управления является **$top** и частью **QueryRequestOptions**.

Элемент управления, который является _наиболее строгим_, победит. Например, если ваш запрос использует операторы **top** или **limit** и приводит к большему количеству записей, чем **First**, максимальное количество возвращаемых записей будет равно **First**. Аналогичным образом, если **top** или **limit** меньше, чем **First**, возвращенный набор записей будет меньшим значением, настроенным с помощью **top** или **limit**.

**First** сейчас имеет максимальное значение _5000_.

## <a name="skipping-records"></a>Пропуск записей

Следующий параметр для работы с большими наборами данных — элемент управления **Skip**. Этот элемент управления позволяет пропустить или перейти определенное число записей перед возвратом результатов. Параметр **Skip** полезен для запросов, которые сортируют результаты удобным образом, где намерением является получение записей в середине результирующего набора. Если необходимые результаты находятся в конце возвращенного набора данных, более эффективно использовать другую конфигурацию сортировки и извлекать результаты из верхней части набора данных.

> [!NOTE]
> При использовании **Skip**рекомендуется заказывать результаты по крайней мере одним столбцом с `asc` или `desc`. Без сортировки возвращенные результаты являются случайными и не повторяются.

В следующих примерах показано, как пропустить первые _10_ записей, в результате которых будет получен запрос, вместо этого начиная возвращаемый набор результатов с 11-й записи.

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

В [REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources) элемент управления является **$skip** и частью **QueryRequestOptions**.

## <a name="paging-results"></a>Разбиение результатов по страницам

Когда необходимо разбить результат, установленный на более мелкие наборы записей для обработки, или потому, что набор результатов превысит максимально допустимое значение _1000_ возвращенных записей, используйте paging. [REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources) **QueryResponse** предоставляет значения, указывающие, что набор результатов разбит на **resultTruncated** и **$skipToken**.
**resultTruncated** является логическим значением, которое оповещает потребителя, если существуют дополнительные записи, которые не возвращаются в ответе. Это условие также можно определить, когда свойство **count** меньше, чем свойство **totalRecords**. **totalRecords** определяет, сколько записей соответствует запросу.

 **resultTruncated** **верно,** когда либо paging отключен или `id` невозможен из-за отсутствия столбца или когда ресурсов меньше, чем запрашивает запрос. Когда **resultTruncated** **истинен,** **$skipToken** свойство не устанавливается.

Ниже приведены следующие примеры, как **пропустить** первые 3000 записей и вернуть **первые** 1000 записей после того, как эти записи были пропущены с Azure CLI и Azure PowerShell:

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> Запрос должен **проецировать** поле **id**, чтобы сработала нумерация страниц. Если он отсутствует в запросе, ответ не будет включать **$skipToken**.

Пример см. в разделе [Запрос следующей страницы](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources#next-page-query) в документации REST API.

## <a name="formatting-results"></a>Результаты форматирования

Результаты запроса Resource Graphпредставлению предоставляются в двух форматах: _Table_ и _ObjectArray._ Формат настроен с параметром **resultFormat** как часть вариантов запроса. Формат _таблицы_ — это значение по умолчанию для **resultFormat.**

Результаты Azure CLI предоставляются в JSON по умолчанию. Результаты в Azure PowerShell по умолчанию являются **PSCustomObject,** но `ConvertTo-Json` они могут быть быстро преобразованы в JSON с помощью cmdlet. Для других SDK результаты запроса могут быть настроены для вывода формата _ObjectArray._

### <a name="format---table"></a>Формат - Таблица

Формат по умолчанию, _таблица_, возвращает результаты в формате JSON, предназначенный для выделения значения конструкции столбца и строки свойств, возвращенных запросом. Этот формат очень напоминает данные, определенные в структурированной таблице или таблице с столбцов, идентифицированных сначала, а затем каждой строки, представляющей данные, выровненные с этими столбиками.

Вот пример результата запроса с форматированием _таблицы:_

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": {
        "columns": [{
                "name": "name",
                "type": "string"
            },
            {
                "name": "type",
                "type": "string"
            },
            {
                "name": "location",
                "type": "string"
            },
            {
                "name": "subscriptionId",
                "type": "string"
            }
        ],
        "rows": [
            [
                "veryscaryvm2-nsg",
                "microsoft.network/networksecuritygroups",
                "eastus",
                "11111111-1111-1111-1111-111111111111"
            ]
        ]
    },
    "facets": [],
    "resultTruncated": "true"
}
```

### <a name="format---objectarray"></a>Формат - ObjectArray

Формат _ObjectArray_ также возвращает результаты в формате JSON. Тем не менее, эта конструкция выравнивается с отношениями пары ключей/значений, общими в JSON, где столбец и данные строки сопоставляются в группах массивов.

Вот пример результата запроса с форматированием _ObjectArray:_

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": [{
        "name": "veryscaryvm2-nsg",
        "type": "microsoft.network/networksecuritygroups",
        "location": "eastus",
        "subscriptionId": "11111111-1111-1111-1111-111111111111"
    }],
    "facets": [],
    "resultTruncated": "true"
}
```

Вот несколько примеров настройки **resultFormat** для использования формата _ObjectArray:_

```csharp
var requestOptions = new QueryRequestOptions( resultFormat: ResultFormat.ObjectArray);
var request = new QueryRequest(subscriptions, "Resources | limit 1", options: requestOptions);
```

```python
request_options = QueryRequestOptions(
    result_format=ResultFormat.object_array
)
request = QueryRequest(query="Resources | limit 1", subscriptions=subs_list, options=request_options)
response = client.resources(request)
```

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь с языком, который используется в [запросах Starter.](../samples/starter.md)
- Просмотрите расширенные запросы в [Расширенных запросах.](../samples/advanced.md)
- Узнайте больше о том, как [изучать ресурсы](explore-resources.md).