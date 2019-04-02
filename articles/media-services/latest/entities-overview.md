---
title: Разработка с использованием интерфейсов API v3 — Azure | Документация Майкрософт
description: В этой статье рассматриваются правила, применяемые к сущностям и API при разработке с помощью Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/30/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6bf0efd6994315d56e7b1b2447ffed9154cf5ee5
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804876"
---
# <a name="developing-with-media-services-v3-apis"></a>Разработка с использованием служб мультимедиа версии 3 API-интерфейсов

В этой статье рассматриваются правила, применяемые к сущностям и API при разработке с помощью Media Services v3.

## <a name="naming-conventions"></a>Соглашения об именовании.

К именам ресурсов Служб мультимедиа версии 3 (например, "Ресурсы", "Задания", "Преобразования") применяются ограничения именования Azure Resource Manager. В соответствии с Azure Resource Manager имена ресурсов всегда уникальны. Таким образом, вы можете использовать любые уникальные строки идентификаторов (например, GUID) для имен ваших ресурсов. 

Имена ресурсов Служб мультимедиа не должны содержать следующие символы: "<", ">", "%", "&", ":", "&#92;", "?", "/", "*", "+", ".", символ единичной кавычки или любые управляющие символы. Все остальные символы разрешены. Максимальная длина имени ресурса составляет 260 символов. 

Дополнительные сведения об именовании в Azure Resource Manager см. в статье [о требованиях к именованию](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) и статье [Соглашения об именовании для ресурсов Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="v3-api-design-principles"></a>Принципы проектирования API версии 3

Один из ключевых принципов проектирования API версии 3 — сделать API более безопасным. API версии 3 не возвращает секреты или учетные данные в **Get** или в операции **List**. Ключи всегда являются NULL, пустыми или исключенными из ответа. Необходимо вызвать отдельный метод действий для получения секретов или учетных данных. Отдельные действия позволяют устанавливать разные разрешения безопасности RBAC в случае, если некоторые API извлекают или отображают секреты, в то время как другие API этого не делают. Сведения о том, как управлять доступом с помощью RBAC, см. в разделе [Управление доступом с помощью RBAC и REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

Примеры включают:

* Не возвращает значения ContentKey в Get StreamingLocator.
* Не возвращает ключи ограниченного использования программ в Get ContentKeyPolicy.
* Возвращает часть строки запроса URL-адрес (Чтобы удалить подпись) URL-адреса входных данных HTTP Jobs ".

См. пример [Политика получения ключа содержимого — .NET](get-content-key-policy-dotnet-howto.md).

## <a name="long-running-operations"></a>Длительные операции

Операции с пометкой `x-ms-long-running-operation` в службах мультимедиа Azure [swagger файлы](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) длинные выполняющиеся операции. 

Дополнительные сведения о том, как отслеживание асинхронных операций Azure, см. в разделе [асинхронных операций](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)

Службы мультимедиа доступны следующие долго выполняющейся операции:

* Создание LiveEvent
* Обновить LiveEvent
* Удалить LiveEvent
* Запустить LiveEvent
* Остановить LiveEvent
* Сброс LiveEvent
* Создание LiveOutput
* Удалить LiveOutput
* Создание конечной точки потоковой передачи
* Обновление конечной точки потоковой передачи
* Удаление конечной точки потоковой передачи
* Запуск конечной точки потоковой передачи
* Остановить конечную точку потоковой передачи
* Масштабирование конечной точки потоковой передачи


## <a name="filtering-ordering-paging-of-media-services-entities"></a>Фильтрация, упорядочивание и разбиение по страницам сущностей Служб мультимедиа

Службы мультимедиа поддерживают следующие параметры запроса OData для сущностей Служб мультимедиа версии 3: 

* $filter 
* $orderby 
* $top 
* $skiptoken 

Описание оператора:

* Eq = равно.
* Ne = не равно.
* Ge = больше или равно.
* Le = меньше или равно.
* Gt = больше чем.
* Lt = меньше чем.

Свойства сущностей типа Datetime всегда задаются в формате UTC.

### <a name="page-results"></a>Разбиение результатов на страницы

Если ответ на запрос содержит большое количество элементов, служба возвращает свойство \@odata.nextLink, чтобы получить следующую страницу результатов. Это можно использовать для просмотра всего результирующего набора. Вы не можете настроить размер страницы. Размер страницы зависит от типа сущности. Ознакомьтесь с отдельными разделами под описанием.

Если сущности создаются или удаляются во время разбиения коллекции на страницы, изменения отражаются в возвращаемых результатах (если эти изменения находятся в той части коллекции, которая не была скачана). 

> [!TIP]
> Для перечисления коллекции всегда нужно использовать следующую ссылку, которая не зависит от конкретного размера страницы.

### <a name="assets"></a>Активы

#### <a name="filteringordering"></a>Фильтрация и упорядочение

В следующей таблице показано, как параметры фильтрации и упорядочения могут быть применены к свойствам [ресурса](https://docs.microsoft.com/rest/api/media/assets). 

|ИМЯ|Фильтр|Порядок|
|---|---|---|
|id|||
|name|eq, gt, lt| По возрастанию и убыванию.|
|properties.alternateId |eq||
|properties.assetId |eq||
|properties.container |||
|properties.created| eq, gt, lt| По возрастанию и убыванию.|
|properties.description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|Тип|||

В следующем примере C# выполняется фильтрация по дате создания:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

#### <a name="pagination"></a>Разбиение на страницы 

Для каждого из четырех порядков сортировки поддерживается разбиение на страницы. Сейчас размер страницы составляет 1000.

##### <a name="c-example"></a>Пример C#

В следующем примере C# показано перечисление всех ресурсов в учетной записи.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

##### <a name="rest-example"></a>Пример REST

Рассмотрим следующий пример, где используется $skiptoken. Замените *amstestaccount* именем своей учетной записи и установите для значения *api-version* последнюю версию.

При запросе списка ресурсов следующим образом:

```
GET  https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

вы должны увидеть ответ следующего вида.

```
HTTP/1.1 200 OK
 
{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-5a4f-470a-9d81-6037d7c23eff","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-98d07299-5a4f-470a-9d81-6037d7c23eff","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-912e-447b-a1ed-0f723913b20d","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-fd05a503-912e-447b-a1ed-0f723913b20d","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Необходимо затем запросить следующую страницу, отправив запрос get.

```
https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

Дополнительные примеры REST для перечисления ресурсов см. в [этой статье](https://docs.microsoft.com/rest/api/media/assets/list).

### <a name="content-key-policies"></a>Политики ключа содержимого

#### <a name="filteringordering"></a>Фильтрация и упорядочение

В следующей таблице показано, как можно применить эти параметры к свойствам [политик ключей содержимого](https://docs.microsoft.com/rest/api/media/contentkeypolicies). 

|ИМЯ|Фильтр|Порядок|
|---|---|---|
|id|||
|name|eq, ne, ge, le, gt, lt|По возрастанию и убыванию.|
|properties.created |eq, ne, ge, le,  gt, lt|По возрастанию и убыванию.|
|properties.description |eq, ne, ge, le, gt, lt||
|properties.lastModified|eq, ne, ge, le, gt, lt|По возрастанию и убыванию.|
|properties.options |||
|properties.policyId|eq, ne||
|Тип|||

#### <a name="pagination"></a>Разбиение на страницы

Для каждого из четырех порядков сортировки поддерживается разбиение на страницы. В настоящее время размер страницы составляет 10.

В следующем примере C# показано перечисление всех **политик ключей содержимого** в учетной записи.

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

Примеры использования REST см. в статье о [политиках ключа содержимого](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list).

### <a name="jobs"></a>Задания

#### <a name="filteringordering"></a>Фильтрация и упорядочение

В следующей таблице показано, как эти параметры могут быть применены к свойствам [заданий](https://docs.microsoft.com/rest/api/media/jobs). 

| ИМЯ    | Фильтр                        | Порядок |
|---------|-------------------------------|-------|
| name                    | eq            | По возрастанию и убыванию.|
| properties.state        | eq, ne        |                         |
| properties.created      | gt, ge, lt, le| По возрастанию и убыванию.|
| properties.lastModified | gt, ge, lt, le | По возрастанию и убыванию.| 


#### <a name="pagination"></a>Разбиение на страницы

В Службах мультимедиа версии 3 поддерживается разбиение заданий на страницы.

В следующем примере C# показано перечисление всех заданий в учетной записи.

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

Примеры REST для перечисления заданий см. в [этой статье](https://docs.microsoft.com/rest/api/media/jobs/list).

### <a name="streaming-locators"></a>Указатели потоковой передачи

#### <a name="filteringordering"></a>Фильтрация и упорядочение

В следующей таблице показано, как можно применить эти параметры к свойствам StreamingLocator: 

|ИМЯ|Фильтр|Порядок|
|---|---|---|
|id |||
|name|eq, ne, ge, le, gt, lt|По возрастанию и убыванию.|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|properties.created |eq, ne, ge, le,  gt, lt|По возрастанию и убыванию.|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |eq, ne, ge, le, gt, lt|По возрастанию и убыванию.|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|Тип   |||

#### <a name="pagination"></a>Разбиение на страницы

Для каждого из четырех порядков сортировки поддерживается разбиение на страницы. В настоящее время размер страницы составляет 10.

В следующем примере C# показано перечисление всех StreamingLocators в учетной записи.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

Примеры использования REST см. в статье о [перечислении указателей потоковой передачи](https://docs.microsoft.com/rest/api/media/streaminglocators/list).

### <a name="streaming-policies"></a>Политики потоковой передачи

#### <a name="filteringordering"></a>Фильтрация и упорядочение

В следующей таблице показано, как можно применить эти параметры к свойствам StreamingPolicy: 

|ИМЯ|Фильтр|Порядок|
|---|---|---|
|id|||
|name|eq, ne, ge, le, gt, lt|По возрастанию и убыванию.|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|properties.created |eq, ne, ge, le,  gt, lt|По возрастанию и убыванию.|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|Тип|||

#### <a name="pagination"></a>Разбиение на страницы

Для каждого из четырех порядков сортировки поддерживается разбиение на страницы. В настоящее время размер страницы составляет 10.

В следующем примере C# показано перечисление всех StreamingPolicy в учетной записи.

```csharp
var firstPage = await MediaServicesArmClient.StreamingPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

Примеры использования REST см. в статье о [перечислении политик потоковой передачи](https://docs.microsoft.com/rest/api/media/streamingpolicies/list).


### <a name="transform"></a>Преобразование

#### <a name="filteringordering"></a>Фильтрация и упорядочение

В следующей таблице показано, как эти параметры могут быть применены к свойствам [преобразований](https://docs.microsoft.com/rest/api/media/transforms). 

| ИМЯ    | Фильтр                        | Порядок |
|---------|-------------------------------|-------|
| name                    | eq            | По возрастанию и убыванию.|
| properties.created      | gt, ge, lt, le| По возрастанию и убыванию.|
| properties.lastModified | gt, ge, lt, le | По возрастанию и убыванию.|

## <a name="next-steps"></a>Дальнейшие действия

[Краткое руководство по потоковой передаче видеофайлов — .NET](stream-files-dotnet-quickstart.md)
