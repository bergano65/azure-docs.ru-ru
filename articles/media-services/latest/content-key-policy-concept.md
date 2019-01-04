---
title: Политики ключей содержимого в Службах мультимедиа — Azure | Документация Майкрософт
description: В этой статье приведены общие сведения о политиках ключей содержимого и их использовании в Службах мультимедиа Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 541713677184d93eb78856e3c3373ab432d5f0cf
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141536"
---
# <a name="content-key-policies"></a>Политики ключа содержимого

Службы мультимедиа Azure позволяют защитить данные мультимедиа, покидающие ваш компьютер, на этапах их хранения, обработки и доставки, а также доставлять в режиме реального времени и по требованию содержимое, зашифрованное динамически с помощью Advanced Encryption Standard (AES-128) или трех основных систем управления цифровыми правами (DRM): Microsoft PlayReady, Google Widevine и Apple FairPlay. Они также обеспечивают службу доставки ключей AES и лицензий DRM (PlayReady, Widevine и FairPlay) авторизованным клиентам.

В Службах мультимедиа Azure версии 3 политики ключа содержимого позволяют указать, каким образом ключ содержимого доставляется конечным клиентам через компонент доставки ключей Служб мультимедиа. Дополнительные сведения см. в статье [Обзор системы защиты содержимого](content-protection-overview.md).

## <a name="contentkeypolicies-definition"></a>Определение ContentKeyPolicies

В следующей таблице представлены свойства ContentKeyPolicy и их определения.

|ИМЯ|ОПИСАНИЕ|
|---|---|
|id|Полный идентификатор ресурса.|
|name|Имя ресурса.|
|properties.created |Дата создания политики.|
|properties.description |Описание политики.|
|properties.lastModified|Дата последнего изменения политики.|
|properties.options |Параметры политик ключей.|
|properties.policyId    |Устаревший идентификатор политики.|
|Тип   |Тип ресурса.|

Полное определение см. на странице [политик ключа содержимого](https://docs.microsoft.com/rest/api/media/contentkeypolicies).

## <a name="filtering-ordering-paging"></a>Фильтрации, упорядочивание, разбиение по страницам

Службы мультимедиа поддерживают следующие параметры запроса OData для ContentKeyPolicies: 

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

В следующей таблице показано, как можно применить эти параметры к свойствам StreamingPolicy: 

|ИМЯ|Фильтр|Порядок|
|---|---|---|
|id|||
|name|Eq, ne, ge, le, gt, lt|По возрастанию и по убыванию|
|properties.created |Eq, ne, ge, le, gt, lt|По возрастанию и по убыванию|
|properties.description |Eq, ne, ge, le, gt, lt||
|properties.lastModified    |Eq, ne, ge, le, gt, lt|По возрастанию и по убыванию|
|properties.options |||
|properties.policyId    |Eq, ne||
|Тип   |||

### <a name="pagination"></a>Разбиение на страницы

Для каждого из четырех порядков сортировки поддерживается разбиение на страницы. В настоящее время размер страницы составляет 10.

> [!TIP]
> Для перечисления коллекции всегда нужно использовать следующую ссылку, которая не зависит от конкретного размера страницы.

Если ответ на запрос содержит большое количество элементов, служба возвращает свойство \@odata.nextLink, чтобы получить следующую страницу результатов. Это можно использовать для просмотра всего результирующего набора. Вы не можете настроить размер страницы. 

Если StreamingPolicy создается или удаляется во время разбивки коллекции на страницы, изменения отражаются в возвращаемых результатах (если эти изменения находятся в той части коллекции, которая не была загружена). 

В следующем примере C# показано перечисление всех ContentKeyPolicies в учетной записи.

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

Примеры использования REST см. в статье о [политиках ключа содержимого](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list).

## <a name="next-steps"></a>Дополнительная информация

[Использование динамического шифрования AES-128 и службы доставки ключей](protect-with-aes128.md)

[Использование динамического шифрования DRM и службы доставки лицензий](protect-with-drm.md)
