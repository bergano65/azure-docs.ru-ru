---
title: Получение имени типа учетной записи хранения и номера SKU с помощью .NET — службы хранилища Azure
description: Узнайте, как получить имя типа учетной записи хранения Azure и номера SKU с помощью клиентской библиотеки .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 0008d8fa9a33db95b0695ce998d4fc267d6222da
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985348"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Получение имени типа учетной записи хранения и номера SKU с помощью .NET

В этой статье показано, как получить имя типа учетной записи хранения Azure и номер SKU для большого двоичного объекта с помощью [клиентской библиотеки службы хранилища Azure для .NET](/dotnet/api/overview/azure/storage/client).

Сведения об учетной записи доступны в версиях служб, начиная с версии 2018-03-28.

## <a name="about-account-type-and-sku-name"></a>Сведения о типе учетной записи и имени SKU

**Тип учетной записи**: Допустимые типы учетных `BlockBlobStorage`записей `FileStorage`: `Storage` `BlobStorage`,, `StorageV2`, и. В разделе [Общие сведения об учетной записи хранения Azure](storage-account-overview.md) содержится дополнительная информация, включая описания различных учетных записей хранения.

**Имя SKU**: Допустимые имена SKU `Premium_LRS`: `Premium_ZRS`, `Standard_GRS`, `Standard_GZRS`, `Standard_LRS`, ,`Standard_RAGRS` ,`Standard_RAGZRS`и .`Standard_ZRS` Имена SKU учитывают регистр и являются строковыми полями в [классе SkuName](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet).

## <a name="retrieve-account-information"></a>Получение сведений об учетной записи

Чтобы получить тип учетной записи хранения и имя SKU, связанные с большим двоичным объектом, вызовите метод [жетаккаунтпропертиес](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet) или [жетаккаунтпропертиесасинк](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet) .

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

## <a name="next-steps"></a>Следующие шаги

Узнайте о других операциях, которые можно выполнять с учетной записью хранения, используя [портал Azure](https://portal.azure.com) и REST API Azure.

- [Управление учетной записью хранения](storage-account-manage.md)
- [Обновление учетной записи хранения](storage-account-upgrade.md)
- [Операция получения сведений об учетной записи (ОСТАВШАЯся)](/rest/api/storageservices/get-account-information)
