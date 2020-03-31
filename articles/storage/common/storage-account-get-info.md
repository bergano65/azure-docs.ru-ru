---
title: Получить тип учетной записи хранилища и имя SKU с .NET
titleSuffix: Azure Storage
description: Узнайте, как получить тип учетной записи хранилища Azure и имя SKU с помощью клиентской библиотеки .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 1495ed55c24a0f94b911d60d1db0f32940ea134a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137064"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Получить тип учетной записи хранилища и имя SKU с .NET

В этой статье показано, как получить тип учетной записи Хранилища Azure и имя SKU для капли, используя [библиотеку клиентов Azure Storage для .NET.](/dotnet/api/overview/azure/storage?view=azure-dotnet)

Информация о счетах доступна в служебных версиях, начиная с версии 2018-03-28.

## <a name="about-account-type-and-sku-name"></a>О типе учетной записи и имени SKU

**Тип учетной записи** `BlockBlobStorage`: `FileStorage`Допустимые типы учетных записей `Storage`включают, `StorageV2` `BlobStorage`, , и . [Обзор учетной записи хранения Azure](storage-account-overview.md) имеет больше информации, включая описания различных учетных записей хранения.

**SKU имя**: Действительные `Premium_LRS` `Premium_ZRS`имена `Standard_GRS` `Standard_GZRS`SKU `Standard_RAGZRS`включают `Standard_ZRS`, , , `Standard_LRS`, `Standard_RAGRS`, и . Имена SKU чувствительны к случаям и являются полями строк в [классе SkuName.](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet)

## <a name="retrieve-account-information"></a>Извлечение информации об учетной записи

Чтобы получить тип учетной записи хранилища и имя SKU, связанные с blob, позвоните в [метод GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet) или [GetAccountPropertiesAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet)

Следующий пример кода извлекает и отображает свойства учетной записи только для чтения.

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

Узнайте о других операциях, которые можно выполнить на учетной записи хранилища через [портал Azure](https://portal.azure.com) и API Azure REST.

- [Получить информационную операцию по учету (REST)](/rest/api/storageservices/get-account-information)
