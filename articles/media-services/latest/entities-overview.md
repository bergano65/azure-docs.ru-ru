---
title: Фильтрация, заказ и paging объектов Медиа-услуг
titleSuffix: Azure Media Services
description: Узнайте о фильтрации, заказе и использовании объектов Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/21/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: bc5c983bc98c3b62df977c6765978cd45cd3c93b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500033"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Фильтрация, заказ и paging объектов Медиа-услуг

В этой теме обсуждаются варианты запроса OData и поддержка pagination, доступные при перечислении объектов Azure Media Services v3.

## <a name="considerations"></a>Рекомендации

* Свойства сущностей, `Datetime` которые имеют тип, всегда находятся в формате UTC.
* Белое пространство в строке запроса должно быть закодировано URL-адресом перед отправкой запроса.

## <a name="comparison-operators"></a>Операторы сравнения

Вы можете использовать следующие операторы для сравнения поля с постоянным значением:

Операторы по вопросам равенства:

- `eq`: Проверьте, является ли поле *равным* постоянному значению.
- `ne`: Проверьте, не является ли поле *равным* постоянному значению.

Операторы диапазона:

- `gt`: Проверьте, является ли поле *больше, чем* постоянное значение.
- `lt`: Проверьте, является ли поле *меньше, чем* постоянное значение.
- `ge`: Проверьте, *является* ли поле больше или равно постоянному значению.
- `le`: Проверьте, *является* ли поле меньше или равно постоянному значению.

## <a name="filter"></a>Filter

Используйте `$filter` для предоставления параметра фильтра OData, чтобы найти только интересующие вас объекты.

Следующий пример REST фильтрует `alternateId` стоимость актива:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

Следующие фильтры примера C's на дате созданного актива:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>Заказ по

Используйте `$orderby` для сортировки возвращенных объектов по указанному параметру. Пример:  

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Чтобы сортировать результаты в порядке восходящего `asc` `desc` или убывающего порядка, прикажите либо или к названию поля, разделенному пространством. Например: `$orderby properties/created desc`.

## <a name="skip-token"></a>Пропустить токен

Если ответ запроса содержит много элементов, служба возвращает значение `$skiptoken` ,`@odata.nextLink`которое используется для получения следующей страницы результатов. Используйте его для страницы через весь набор результатов.

В Media Services v3 вы не можете настроить размер страницы. Размер страницы варьируется в зависимости от типа сущности. Читайте отдельные разделы, которые следуют за деталями.

Если объекты создаются или удаляются во время прокладки коллекции, изменения отражаются в вернувшихся результатах (если эти изменения находятся в той части коллекции, которая не была загружена).

> [!TIP]
> Всегда `nextLink` используйте для перечисления коллекции и не зависит от конкретного размера страницы.
>
> Значение `nextLink` будет присутствовать только при наличии более одной страницы сущностей.

Рассмотрим следующий `$skiptoken` пример того, где используется. Замените *amstestaccount* именем своей учетной записи и установите для значения *api-version* последнюю версию.

Если вы запрашиваете список таких активов:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Вы получите ответ похож на этот:

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

В следующем примере C-примера показано, как перечислять через все потоковые локаторы в учетной записи.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Использование логических операторов для объединения параметров запроса

Media Services v3 поддерживает **OR** и **AND** логических операторов. 

Следующий пример REST проверяет состояние задания:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

Вы строите тот же запрос в C', как это: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Варианты фильтрации и заказа юридических лиц

В следующей таблице показано, как можно применить параметры фильтрации и заказа для различных сущностей:

|Имя сущности|Имя свойства|Filter|Порядок|
|---|---|---|---|
|[Активы](https://docs.microsoft.com/rest/api/media/assets/)|name|`eq`, `gt`, `lt`, `ge`, `le`|`asc` и `desc`|
||properties.alternateId |`eq`||
||properties.assetId |`eq`||
||properties.created| `eq`, `gt`, `lt`| `asc` и `desc`|
|[Ключевые политики содержания](https://docs.microsoft.com/rest/api/media/contentkeypolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` и `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` и `desc`|
||properties.description    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||properties.lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` и `desc`|
||properties.policyId|`eq`, `ne`||
|[Задания](https://docs.microsoft.com/rest/api/media/jobs)| name  | `eq`            | `asc` и `desc`|
||properties.state        | `eq`, `ne`        |                         |
||properties.created      | `gt`, `ge`, `lt`, `le`| `asc` и `desc`|
||properties.lastModified | `gt`, `ge`, `lt`, `le` | `asc` и `desc`| 
|[Потоковые локаторы](https://docs.microsoft.com/rest/api/media/streaminglocators)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` и `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`,  `gt`, `lt`|`asc` и `desc`|
||properties.endTime    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` и `desc`|
|[Политики потоковой передачи](https://docs.microsoft.com/rest/api/media/streamingpolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` и `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` и `desc`|
|[Преобразования](https://docs.microsoft.com/rest/api/media/transforms)| name | `eq`            | `asc` и `desc`|
|| properties.created      | `gt`, `ge`, `lt`, `le`| `asc` и `desc`|
|| properties.lastModified | `gt`, `ge`, `lt`, `le`| `asc` и `desc`|

## <a name="next-steps"></a>Дальнейшие действия

* [Список активов](https://docs.microsoft.com/rest/api/media/assets/list)
* [Список ключевых политик содержания](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)
* [Список вакансий](https://docs.microsoft.com/rest/api/media/jobs/list)
* [Политика потоковой передачи списка](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)
* [Список потоков локаторов](https://docs.microsoft.com/rest/api/media/streaminglocators/list)
* [Краткое руководство по потоковой передаче видеофайлов — .NET](stream-files-dotnet-quickstart.md)
* [Квоты и ограничения](limits-quotas-constraints.md)
