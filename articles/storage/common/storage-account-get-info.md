---
title: Получение имени типа учетной записи хранения и номера SKU с помощью .NET
titleSuffix: Azure Storage
description: Узнайте, как получить имя типа учетной записи хранения Azure и номера SKU с помощью клиентской библиотеки .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 17f18f84ac1c1738f8c248bb0071c748e15dacf3
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090936"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Получение имени типа учетной записи хранения и номера SKU с помощью .NET

В этой статье показано, как получить имя типа учетной записи хранения Azure и номер SKU для большого двоичного объекта с помощью [клиентской библиотеки службы хранилища Azure для .NET](/dotnet/api/overview/azure/storage).

Сведения об учетной записи доступны в версиях служб, начиная с версии 2018-03-28.

## <a name="about-account-type-and-sku-name"></a>Сведения о типе учетной записи и имени SKU

**Тип учетной записи**: допустимые типы учетных записей включают `BlobStorage` , `BlockBlobStorage` ,, `FileStorage` `Storage` и `StorageV2` . В разделе [Общие сведения об учетной записи хранения Azure](storage-account-overview.md) содержится дополнительная информация, включая описания различных учетных записей хранения.

**Имя SKU**: допустимые имена SKU включают `Premium_LRS` , `Premium_ZRS` , `Standard_GRS` , `Standard_GZRS` , `Standard_LRS` ,, и `Standard_RAGRS` `Standard_RAGZRS` `Standard_ZRS` . Имена SKU учитывают регистр и являются строковыми полями в [классе SkuName](/dotnet/api/microsoft.azure.management.storage.models.skuname).

## <a name="retrieve-account-information"></a>Получение сведений об учетной записи

Чтобы получить тип учетной записи хранения и имя SKU, связанные с большим двоичным объектом, вызовите метод [жетаккаунтпропертиес](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties) или [жетаккаунтпропертиесасинк](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync) .

В следующем примере кода извлекаются и отображаются свойства учетной записи только для чтения.

```csharp
private static async Task GetAccountInfoAsync(CloudBlob blob)
{
    try
    {
        // Get the blob's storage account properties.
        AccountProperties acctProps = await blob.GetAccountPropertiesAsync();

        // Display the properties.
        Console.WriteLine("Account properties");
        Console.WriteLine("  AccountKind: {0}", acctProps.AccountKind);
        Console.WriteLine("      SkuName: {0}", acctProps.SkuName);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Дальнейшие действия

Узнайте о других операциях, которые можно выполнять с учетной записью хранения, используя [портал Azure](https://portal.azure.com) и REST API Azure.

- [Операция получения сведений об учетной записи (ОСТАВШАЯся)](/rest/api/storageservices/get-account-information)
