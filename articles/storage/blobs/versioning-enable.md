---
title: Включение управления версиями BLOB-объектов и управление ими (Предварительная версия)
titleSuffix: Azure Storage
description: Узнайте, как включить управление версиями BLOB-объектов (Предварительная версия) в портал Azure или с помощью шаблона Azure Resource Manager.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/10/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 7e8d712a3477fe7dd9b963f203b3374dd5fa2a2e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001065"
---
# <a name="enable-and-manage-blob-versioning-preview"></a>Включение управления версиями BLOB-объектов и управление ими (Предварительная версия)

Вы можете включить управление версиями хранилища BLOB-объектов (Предварительная версия) для автоматического обслуживания предыдущих версий объекта.  Если управление версиями BLOB-объектов включено, можно восстановить более раннюю версию большого двоичного объекта, чтобы восстановить данные, если они ошибочно изменены или удалены.

В этой статье показано, как включить или отключить управление версиями BLOB-объектов для учетной записи хранения с помощью портал Azure или шаблона Azure Resource Manager.

Перед включением управления версиями BLOB-объектов необходимо зарегистрироваться в предварительной версии. Дополнительные сведения о управлении версиями BLOB-объектов, включая сведения о регистрации для предварительной версии, см. в разделе [Управление версиями больших двоичных объектов (Предварительная версия)](versioning-overview.md).

## <a name="enable-blob-versioning"></a>Включение управления версиями BLOB-объектов

# <a name="azure-portal"></a>[Портал Azure](#tab/portal)

Чтобы включить управление версиями BLOB-объектов в портал Azure:

1. Перейдите к своей учетной записи хранения на портале.
1. В разделе **Служба BLOB-объектов**выберите **Защита данных**.
1. В разделе **Управление версиями** выберите **включено**.

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Снимок экрана, показывающий, как включить управление версиями BLOB-объектов в портал Azure":::

# <a name="template"></a>[Шаблон](#tab/template)

Чтобы включить управление версиями BLOB-объектов с помощью шаблона, создайте шаблон со свойством **исверсионинженаблед** в **значение true**. Следующие шаги описывают создание шаблона в портал Azure.

1. В портал Azure выберите **создать ресурс**.
1. В **поле Поиск в Marketplace**введите **шаблон развертывание**и нажмите клавишу **Ввод**.
1. Выберите **шаблоны развертывания**, выберите **создать**, а затем — создать **собственный шаблон в редакторе**.
1. В редакторе шаблонов вставьте следующий код JSON. Замените заполнитель `<accountName>` именем вашей учетной записи хранения.
1. при сохранении шаблона;
1. Укажите группу ресурсов учетной записи, а затем нажмите кнопку **приобрести** , чтобы развернуть шаблон и включить управление версиями BLOB-объектов.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

Дополнительные сведения о развертывании ресурсов с помощью шаблонов в портал Azure см. в разделе [развертывание ресурсов с помощью портал Azure](../../azure-resource-manager/templates/deploy-portal.md).

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>Изменение большого двоичного объекта для активации новой версии

В следующем примере кода показано, как активировать создание новой версии с помощью клиентской библиотеки службы хранилища Azure для .NET версии [12.5.0-Preview. 5](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.0-preview.5) или более поздней. Перед запуском этого примера убедитесь, что вы включили управление версиями для учетной записи хранения.

В примере создается блочный BLOB-объект, а затем обновляются метаданные большого двоичного объекта. Обновление метаданных большого двоичного объекта инициирует создание новой версии. В примере извлекается исходная и текущая версии, и показывается, что метаданные содержат только текущая версия.

```csharp
public static async Task UpdateVersionedBlobMetadata(string containerName, string blobName)
{
    // Create a new service client from the connection string.
    BlobServiceClient blobServiceClient = new BlobServiceClient(ConnectionString);

    // Create a new container client.
    BlobContainerClient containerClient = blobServiceClient.GetBlobContainerClient(containerName);

    try
    {
        // Create the container.
        await containerClient.CreateIfNotExistsAsync();

        // Upload a block blob.
        BlockBlobClient blockBlobClient = containerClient.GetBlockBlobClient(blobName);

        string blobContents = string.Format("Block blob created at {0}.", DateTime.Now);
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        string initalVersionId;
        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            Response<BlobContentInfo> uploadResponse = await blockBlobClient.UploadAsync(stream, null, default);

            // Get the version ID for the current version.
            initalVersionId = uploadResponse.Value.VersionId;
        }

        // Update the blob's metadata to trigger the creation of a new version.
        Dictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "key", "value" },
            { "key1", "value1" }
        };

        Response<BlobInfo> metadataResponse = await blockBlobClient.SetMetadataAsync(metadata);

        // Get the version ID for the new current version.
        string newVersionId = metadataResponse.Value.VersionId;

        // Request metadata on the previous version.
        BlockBlobClient initalVersionBlob = blockBlobClient.WithVersion(initalVersionId);
        Response<BlobProperties> propertiesResponse = await initalVersionBlob.GetPropertiesAsync();
        PrintMetadata(propertiesResponse);

        // Request metadata on the current version.
        BlockBlobClient newVersionBlob = blockBlobClient.WithVersion(newVersionId);
        Response<BlobProperties> newPropertiesResponse = await newVersionBlob.GetPropertiesAsync();
        PrintMetadata(newPropertiesResponse);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}

static void PrintMetadata(Response<BlobProperties> propertiesResponse)
{
    if (propertiesResponse.Value.Metadata.Count > 0)
    {
        Console.WriteLine("Metadata values for version {0}:", propertiesResponse.Value.VersionId);
        foreach (var item in propertiesResponse.Value.Metadata)
        {
            Console.WriteLine("Key:{0}  Value:{1}", item.Key, item.Value);
        }
    }
    else
    {
        Console.WriteLine("Version {0} has no metadata.", propertiesResponse.Value.VersionId);
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

- [Управление версиями BLOB-объектов (предварительная версия)](versioning-overview.md)
- [Soft delete for Azure Storage blobs](soft-delete-overview.md) (Обратимое удаление больших двоичных объектов службы хранилища Azure)
