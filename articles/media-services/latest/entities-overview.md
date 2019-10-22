---
title: Фильтрация, упорядочение и подкачка сущностей служб мультимедиа в Azure | Документация Майкрософт
description: В этой статье рассматриваются фильтрация, упорядочение и разбиение по страницам сущностей Служб мультимедиа Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/11/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: d13ff3944e53f103c03a92e03d217b0066bc97df
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693311"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Фильтрация, упорядочение и разбиение на страницы объектов служб мультимедиа

В этом разделе обсуждаются параметры запроса OData и поддержка разбиения на страницы, доступные при перечислении сущностей служб мультимедиа Azure v3.

## <a name="considerations"></a>Рекомендации

* Свойства сущностей, которые относятся к типу `Datetime`, всегда имеют формат UTC.
* Перед отправкой запроса пробелы в строке запроса должны быть закодированы в виде URL-адреса.

## <a name="comparison-operators"></a>Операторы сравнения

Для сравнения поля с постоянным значением можно использовать следующие операторы:

Операторы равенства:

- `eq`. проверяет, *равно* ли поле значению константы.
- `ne`: Проверьте, *не равно* ли поле значению константы.

Операторы диапазона:

- `gt`. проверяет *, превышает ли* поле постоянное значение.
- `lt`: проверяет, меньше ли поле, *чем* постоянное значение.
- `ge`: Проверка того, что поле *больше или равно* константе. value
- `le`: Проверка того, что поле *меньше или равно* значению константы.

## <a name="filter"></a>Фильтр

Используйте `$filter`, чтобы указать параметр фильтра OData, чтобы найти только интересующие вас объекты.

В приведенных ниже примерах фильтрации по значению `alternateId` ресурса.

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

В следующем C# примере производится фильтрация по дате создания ресурса:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```    

## <a name="order-by"></a>Упорядочить по

Используйте `$orderby`, чтобы отсортировать возвращенные объекты по указанному параметру. Пример.    

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Чтобы отсортировать результаты по возрастанию или по убыванию, добавьте в имя поля `asc` или `desc`, разделяя их пробелом. Например, `$orderby properties/created desc`.

## <a name="skip-token"></a>Пропустить токен

Если ответ запроса содержит много элементов, служба возвращает значение `$skiptoken` (`@odata.nextLink`), которое используется для получения следующей страницы результатов. Используйте его для постраничного просмотра всего результирующего набора.

В службах мультимедиа v3 нельзя настроить размер страницы. Размер страницы зависит от типа сущности. Дополнительные сведения см. в следующих разделах.

Если при разбиении по коллекциям создаются или удаляются сущности, изменения отражаются в возвращенных результатах (если эти изменения находятся в части коллекции, которая не была загружена). 

> [!TIP]
> Следует всегда использовать `nextLink` для перечисления коллекции и не зависеть от конкретного размера страницы.
>
> Значение `nextLink` будет представлено только в том случае, если имеется более одной страницы сущностей.

Рассмотрим следующий пример использования `$skiptoken`. Замените *amstestaccount* именем своей учетной записи и установите для значения *api-version* последнюю версию.

Если вы запрашиваете список ресурсов следующим образом:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Вы получите ответ, аналогичный следующему:

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Необходимо затем запросить следующую страницу, отправив запрос get.

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

В следующем C# примере показано, как перечислить все указатели потоковой передачи в учетной записи.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Использование логических операторов для объединения параметров запроса

Службы мультимедиа v3 поддерживают логические операторы **or** **и and.** 

В следующем примере для проверки состояния задания выполняется проверка:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

Один и тот же запрос создается C# следующим образом: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Фильтрация и сортировка параметров сущностей

В следующей таблице показано, как можно применить параметры фильтрации и упорядочивания к разным сущностям.

|Имя сущности|Имя свойства|Фильтр|Заказ|
|---|---|---|---|
|[Ресурсы](https://docs.microsoft.com/rest/api/media/assets/)|name|`eq`, `gt`, `lt`, `ge`, `le`|`asc` и `desc`|
||properties.alternateId |`eq`||
||properties.assetId |`eq`||
||properties.created| `eq`, `gt`, `lt`| `asc` и `desc`|
|[Политики ключей содержимого](https://docs.microsoft.com/rest/api/media/contentkeypolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` и `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` и `desc`|
||properties.description    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||properties.lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` и `desc`|
||properties.policyId|`eq`, `ne`||
|[Задания](https://docs.microsoft.com/rest/api/media/jobs)| name  | `eq`            | `asc` и `desc`|
||properties.state        | `eq`, `ne`        |                         |
||properties.created      | `gt`, `ge`, `lt`, `le`| `asc` и `desc`|
||properties.lastModified | `gt`, `ge`, `lt`, `le` | `asc` и `desc`| 
|[Указатели потоковой передачи](https://docs.microsoft.com/rest/api/media/streaminglocators)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` и `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` и `desc`|
||properties.endTime    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` и `desc`|
|[Политики потоковой передачи](https://docs.microsoft.com/rest/api/media/streamingpolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` и `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` и `desc`|
|[Преобразования](https://docs.microsoft.com/rest/api/media/transforms)| name | `eq`            | `asc` и `desc`|
|| properties.created      | `gt`, `ge`, `lt`, `le`| `asc` и `desc`|
|| properties.lastModified | `gt`, `ge`, `lt`, `le`| `asc` и `desc`|

## <a name="next-steps"></a>Дальнейшие действия

* [Список ресурсов](https://docs.microsoft.com/rest/api/media/assets/list)
* [Список политик ключей содержимого](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)
* [Список заданий](https://docs.microsoft.com/rest/api/media/jobs/list)
* [Перечисление политик потоковой передачи](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)
* [Перечисление указателей потоковой передачи](https://docs.microsoft.com/rest/api/media/streaminglocators/list)
* [Краткое руководство по потоковой передаче видеофайлов — .NET](stream-files-dotnet-quickstart.md)
* [Квоты и ограничения](limits-quotas-constraints.md)
