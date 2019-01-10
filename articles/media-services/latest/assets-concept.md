---
title: Ресурсы в Службах мультимедиа в Azure | Документация Майкрософт
description: В этой статье приведены общие сведения о ресурсах и их использовании в Службах мультимедиа Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/01/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 8507d51f0d4d49d89fc24b38ed73df7488261daa
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969581"
---
# <a name="assets"></a>Активы

В Службах мультимедиа Azure сущность [Ресурс](https://docs.microsoft.com/rest/api/media/assets) содержит цифровые файлы (в том числе видео, аудио, изображения, коллекции эскизов, текстовые дорожки и файлы скрытых титров), а также метаданные об этих файлах. После загрузки цифровых файлов в ресурс их можно использовать в рабочих процессах кодирования, потоковой передачи и анализа содержимого Служб мультимедиа. Дополнительные сведения см. в разделе [Отправка цифровых файлов в ресурсы](#upload-digital-files-into-assets) ниже.

Ресурс сопоставляется с контейнером больших двоичных объектов в [учетной записи хранения Azure](storage-account-concept.md). Файлы ресурса хранятся в этом контейнере как блочные BLOB-объекты. Если в учетной записи используется хранилище общего назначения версии 2 (GPv2), Службы мультимедиа поддерживают уровни больших двоичных объектов. Это хранилище позволяет перемещать файлы в [холодный или архивный уровень хранилища](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). **Архивный** уровень хранилища подходит для архивации ненужных исходных файлов (например, после шифрования).

**Архивный уровень хранилища** рекомендуется только для очень больших исходных файлов, которые уже были закодированы, а выходные данные задания кодирования были помещены в выходной контейнер больших двоичных объектов. Большие двоичные объекты в выходном контейнере, который вы хотите привязать к ресурсу и использовать для потоковой передачи или анализа содержимого, должны существовать на **горячем** или **холодном** уровне хранилища.

## <a name="asset-definition"></a>Определение ресурса

В следующей таблице представлены свойства ресурса и их определения.

|ИМЯ|ОПИСАНИЕ|
|---|---|
|id|Полный идентификатор ресурса.|
|name|Имя ресурса.|
|properties.alternateId |Альтернативный идентификатор ресурса.|
|properties.assetId |Идентификатор ресурса.|
|properties.container |Имя контейнера больших двоичных объектов ресурса.|
|properties.created |Дата создания ресурса.<br/> Дата и время всегда указаны в формате UTC.|
|properties.description|Описание ресурса.|
|properties.lastModified |Дата последнего изменения ресурса. <br/> Дата и время всегда указаны в формате UTC.|
|properties.storageAccountName |Имя учетной записи хранения.|
|properties.storageEncryptionFormat |Формат шифрования ресурса. Не указан или MediaStorageEncryption.|
|Тип|Тип ресурса.|

Полное определение см. в статье о [ресурсах](https://docs.microsoft.com/rest/api/media/assets).

## <a name="upload-digital-files-into-assets"></a>Отправка цифровых файлов в ресурсы

Один из стандартных рабочих процессов Служб мультимедиа — отправка, кодирование и потоковая передача файла. В этом разделе описываются общие инструкции.

1. Используйте API Служб мультимедиа версии 3, чтобы создать новый входной ресурс. Эта операция создает контейнер в учетной записи хранения, связанной с вашей учетной записью Служб мультимедиа. API возвращает имя контейнера (например, `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).
   
    Если у вас уже есть контейнер больших двоичных объектов, который необходимо связать с ресурсом, можно указать имя контейнера при создании ресурса. Службы мультимедиа в настоящее время поддерживают только большие двоичные объекты в корневом контейнере, а не с путями в имени файла. Таким образом, контейнер с именем файла input.mp4 подойдет. А контейнер с именем файла videos/inputs/input.mp4 не подойдет.

    Azure CLI можно использовать для передачи непосредственно в любую учетную запись хранения и контейнер, на которые у вас есть права в вашей подписке. <br/>Имя контейнера должно быть уникальным и соответствовать правилам именования хранилища. Имя не обязательно должно соответствовать формату имени контейнера ресурса Служб мультимедиа (Asset-GUID). 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Получите URL-адрес SAS с разрешениями на чтение и запись, которые будут использоваться для передачи цифровых файлов в контейнер ресурса. Можно использовать API Служб мультимедиа для вывода [списка URL-адресов контейнеров ресурса](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).
3. Используйте API службы хранилища Azure или пакеты SDK (например, [REST API службы хранилища](../../storage/common/storage-rest-api-auth.md), [пакет SDK для JAVA](../../storage/blobs/storage-quickstart-blobs-java-v10.md) или [пакет SDK для .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) для передачи файлов в контейнер ресурса. 
4. Используйте API Служб мультимедиа версии 3, чтобы создать преобразование и задание для обработки входного ресурса. Дополнительные сведения см. в статье [Преобразования и задания](transform-concept.md).
5. Потоковая передача содержимого из выходного ресурса.

> [!TIP]
> Полный пример .NET, в котором показано, как создать ресурс, получить доступный для записи URL-адрес SAS для контейнера ресурса в хранилище, отправить файл в контейнер в хранилище с помощью URL-адреса SAS, см. в разделе [Создание входных данных задания из локального файла](job-input-from-local-file-how-to.md).

### <a name="create-a-new-asset"></a>Создание ресурса

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Пример REST см. в разделе [Создание ресурса с помощью REST](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples).

В примере показано создание **текста запроса**, где можно указать полезные сведения, такие как описание, имя контейнера, учетную запись хранения и другие сведения.

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

Полный пример см. в разделе [Создание входных данных задания из локального файла](job-input-from-local-file-how-to.md). В Службах мультимедиа версии 3 входные данные задания также могут создаваться из URL-адреса HTTPS (см. раздел [Создание входных данных задания из URL-адреса HTTPS](job-input-from-http-how-to.md)).

## <a name="filtering-ordering-paging"></a>Фильтрации, упорядочивание, разбиение по страницам

Службы мультимедиа поддерживают следующие параметры запроса OData для ресурсов: 

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

### <a name="filteringordering"></a>Фильтрация и упорядочение

В следующей таблице показано, как эти параметры могут быть применены к свойствам ресурса: 

|ИМЯ|Фильтр|Порядок|
|---|---|---|
|id|||
|name|Поддерживает: Eq, Gt, Lt|Поддерживает: по возрастанию и убыванию|
|properties.alternateId |Поддерживает: Eq||
|properties.assetId |Поддерживает: Eq||
|properties.container |||
|properties.created|Поддерживает: Eq, Gt, Lt| Поддерживает: по возрастанию и убыванию|
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

### <a name="pagination"></a>Разбиение на страницы

Для каждого из четырех порядков сортировки поддерживается разбиение на страницы. Сейчас размер страницы составляет 1000.

> [!TIP]
> Для перечисления коллекции всегда нужно использовать следующую ссылку, которая не зависит от конкретного размера страницы.

Если ответ на запрос содержит большое количество элементов, служба возвращает свойство \@odata.nextLink, чтобы получить следующую страницу результатов. Это можно использовать для просмотра всего результирующего набора. Вы не можете настроить размер страницы. 

Если ресурсы создаются или удаляются во время разбивки коллекции на страницы, изменения отражаются в возвращаемых результатах (если эти изменения находятся в той части коллекции, которая не была загружена). 

#### <a name="c-example"></a>Пример C#

В следующем примере C# показано перечисление всех ресурсов в учетной записи.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

#### <a name="rest-example"></a>Пример REST

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

## <a name="storage-side-encryption"></a>Шифрование на стороне хранилища

Чтобы защитить неактивные ресурсы, их нужно зашифровать на стороне хранилища. В следующей таблице показано, как происходит шифрование на стороне хранилища в Службах мультимедиа.

|Вариант шифрования|ОПИСАНИЕ|Службы мультимедиа версии 2|Службы мультимедиа версии 3|
|---|---|---|---|
|Шифрование хранилища Служб мультимедиа|Шифрование AES-256. Ключами управляют Службы мультимедиа|Поддерживается<sup>(1)</sup>|Не поддерживается<sup>(2)</sup>|
|[Шифрование службы хранилища для неактивных данных](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Шифрование на стороне сервера, предоставляемое службой хранилища Azure. Ключами управляет Azure или клиент|Поддерживаются|Поддерживаются|
|[Шифрование хранилища на стороне клиента](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Шифрование на стороне клиента, предоставляемое службой хранилища Azure. Ключами управляет клиент в Key Vault|Не поддерживается|Не поддерживается|

<sup>1</sup>Хотя Службы мультимедиа поддерживают обработку содержимого в чистом, незашифрованном виде, мы не рекомендуем ее использовать.

<sup>2</sup>В Службах мультимедиа версии 3 для обратной совместимости поддерживается шифрование хранилища (шифрование AES-256), только если ресурсы созданы с помощью Служб мультимедиа версии 2. То есть версия 3 работает с существующими зашифрованными ресурсами хранилища, но создание новых ресурсов не поддерживается.

## <a name="next-steps"></a>Дополнительная информация

[Краткое руководство по потоковой передаче видеофайлов — .NET](stream-files-dotnet-quickstart.md)

[Различия между Службами мультимедиа версий 2 и 3](migrate-from-v2-to-v3.md)
