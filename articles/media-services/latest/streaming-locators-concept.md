---
title: Указатели потоковой передачи в Службах мультимедиа Azure | Документация Майкрософт
description: В этой статье приведены общие сведения об указателях потоковой передачи и их использовании в Службах мультимедиа Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/22/2018
ms.author: juliako
ms.openlocfilehash: 56e9a0b4eec347d2367c38ab00c6c9d9dca60752
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986904"
---
# <a name="streaming-locators"></a>Указатели потоковой передачи

Чтобы предоставить клиентам URL-адрес, предназначенный для воспроизведения видео или звуковых файлов, необходимо создать [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) и компилировать URL-адреса потоковой передачи. Дополнительные сведения см. в статье [Краткое руководство по потоковой передаче видеофайлов — .NET](stream-files-dotnet-quickstart.md).

## <a name="streaminglocator-definition"></a>Определение StreamingLocator

В следующей таблице представлены свойства StreamingLocator и их определения.

|ИМЯ|type|ОПИСАНИЕ|
|---|---|---|
|id |строка|Полный идентификатор ресурса.|
|name   |строка|Имя ресурса.|
|properties.alternativeMediaId  |строка|Идентификатор мультимедиа этого указателя потоковой передачи.|
|properties.assetName   |строка|Имя ресурса|
|properties.contentKeys |StreamingLocatorContentKey[]|Указатель потоковой передачи использует ContentKeys.|
|properties.created |строка|Время создания указателя потоковой передачи.|
|properties.defaultContentKeyPolicyName |строка|Указатель потоковой передачи использует имя по умолчанию ContentKeyPolicy.|
|properties.endTime |строка|Время окончания работы указателя потоковой передачи.|
|properties.startTime   |строка|Время начала работы указателя потоковой передачи.|
|properties.streamingLocatorId  |строка|StreamingLocatorId указателя потоковой передачи.|
|properties.streamingPolicyName |строка|Указатель потоковой передачи использует имя политики потоковой передачи. Можно указать имя созданной вами политики потоковой передачи или использовать одну из предопределенных политик. В настоящее время доступны следующие предопределенные политики потоковой передачи: Predefined_DownloadOnly, Predefined_ClearStreamingOnly, Predefined_DownloadAndClearStreaming, Predefined_ClearKey, Predefined_MultiDrmCencStreaming и Predefined_MultiDrmStreaming.|
|Тип   |строка|Тип ресурса.|

Полное определение см. на странице [указателей потоковой передачи](https://docs.microsoft.com/rest/api/media/streaminglocators).

## <a name="filtering-ordering-paging"></a>Фильтрации, упорядочивание, разбиение по страницам

Службы мультимедиа поддерживают следующие параметры запроса OData для указателей потоковой передачи. 

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

В следующей таблице показано, как можно применить эти параметры к свойствам StreamingLocator: 

|ИМЯ|Фильтр|Порядок|
|---|---|---|
|id |||
|name|Eq, ne, ge, le, gt, lt|По возрастанию и по убыванию|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|properties.created |Eq, ne, ge, le, gt, lt|По возрастанию и по убыванию|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |Eq, ne, ge, le, gt, lt|По возрастанию и по убыванию|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|Тип   |||

### <a name="pagination"></a>Разбиение на страницы

Для каждого из четырех порядков сортировки поддерживается разбиение на страницы. В настоящее время размер страницы составляет 10.

> [!TIP]
> Для перечисления коллекции всегда нужно использовать следующую ссылку, которая не зависит от конкретного размера страницы.

Если ответ на запрос содержит большое количество элементов, служба возвращает свойство \@odata.nextLink, чтобы получить следующую страницу результатов. Это можно использовать для просмотра всего результирующего набора. Вы не можете настроить размер страницы. 

Если StreamingLocators создается или удаляется во время разбивки коллекции на страницы, изменения отражаются в возвращаемых результатах (если эти изменения находятся в той части коллекции, которая не была загружена). 

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

## <a name="next-steps"></a>Дополнительная информация

[Краткое руководство по потоковой передаче видеофайлов — .NET](stream-files-dotnet-quickstart.md)
