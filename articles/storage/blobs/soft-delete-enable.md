---
title: Включение обратимого удаления для больших двоичных объектов и управление им
titleSuffix: Azure Storage
description: Включите обратимое удаление для объектов BLOB, чтобы упростить восстановление данных при ошибочном изменении или удалении.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: bbefa2a5d40d047d8885e4a0db8239d79a24feae
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83120119"
---
# <a name="enable-and-manage-soft-delete-for-blobs"></a>Включение обратимого удаления для больших двоичных объектов и управление им

Обратимое удаление защищает данные большого двоичного объекта от случайного или ошибочного изменения или удаления. Если обратимое удаление включено для учетной записи хранения, большие двоичные объекты, версии больших двоичных объектов (Предварительная версия) и моментальные снимки в этой учетной записи хранения могут быть восстановлены после их удаления в течение указанного срока хранения.

Если есть вероятность, что данные могут быть случайно изменены или удалены приложением или другим пользователем учетной записи хранения, корпорация Майкрософт рекомендует включить обратимое удаление.

В этой статье показано, как начать обратимое удаление.

## <a name="enable-soft-delete"></a>Включение обратимого удаления

# <a name="portal"></a>[Портал](#tab/azure-portal)

Включите обратимое удаление для больших двоичных объектов в учетной записи хранения с помощью портал Azure:

1. В [портал Azure](https://portal.azure.com/)выберите свою учетную запись хранения. 

2. Перейдите к параметру **Защита данных** в разделе **Служба BLOB-объектов**.

3. Щелкните **включено** в разделе **обратимое удаление BLOB-объекта** .

4. Введите число дней, которое требуется *хранить для* **политик хранения** .

5. Нажмите кнопку " **сохранить** ", чтобы подтвердить параметры защиты данных

![](media/soft-delete-enable/storage-blob-soft-delete-portal-configuration.png)

Чтобы просмотреть обратимо удаленные большие двоичные объекты, установите флажок **Show deleted blobs** (Показать удаленные большие двоичные объекты).

![](media/soft-delete-enable/storage-blob-soft-delete-portal-view-soft-deleted.png)

Чтобы просмотреть обратимо удаленные моментальные снимки для данного большого двоичного объекта, выберите большой двоичный объект, а затем щелкните **Просмотреть моментальные снимки**.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Установите флажок **Показать удаленные моментальные снимки**.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Щелкнув обратимо удаленный большой двоичный объект или моментальный снимок, обратите внимание на новые свойства большого двоичного объекта. Они указывают, когда объект был удален и количество дней, оставшихся до полного удаления большого двоичного объекта или моментального снимка. Если обратимо удаленный объект не является моментальным снимком, его удаление можно отменить.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-properties.png)

Помните, что отмена удаления большого двоичного объекта также отменит удаление всех связанных с ним моментальных снимков. Чтобы обратимо удалить моментальные снимки активного большого двоичного объекта, щелкните его и выберите **Undelete all snapshots** (Отменить удаление всех моментальных снимков).

![](media/soft-delete-enable/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

После отмены удаления моментальных снимков большого двоичного объекта можно щелкнуть **Повысить уровень**, чтобы скопировать моментальный снимок в корневой большой двоичный объект, тем самым восстановив большой двоичный объект в моментальный снимок.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Чтобы включить обратимое удаление, обновите свойства клиентской службы BLOB-объектов. В следующем примере включено обратимое удаление подмножества учетных записей в подписке:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```

Убедиться, что обратимое удаление включено, можно с помощью следующей команды:

```powershell
$MatchingAccounts | $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context | Select-Object -ExpandProperty DeleteRetentionPolicy
```

Чтобы восстановить большие двоичные объекты, которые были случайно удалены, вызовите для них отмену удаления. Помните, что при вызове операции **Undelete Blob** как в активных, так и в обратимо удаленных больших двоичных объектах все связанные обратимо удаленные моментальные снимки будут восстановлены в качестве активных. В следующем примере вызывается отмена удаления всех обратимо удаленных и активных больших двоичных объектов в контейнере:

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Чтобы найти текущую политику хранения обратимого удаления, используйте следующую команду:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

Чтобы включить обратимое удаление, обновите свойства клиентской службы BLOB-объектов:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Чтобы проверить, включена ли возможность удаления, используйте следующую команду: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="python"></a>[Python](#tab/python)

Чтобы включить обратимое удаление, обновите свойства клиентской службы BLOB-объектов:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(
    account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(
    delete_retention_policy=DeleteRetentionPolicy(enabled=True, days=7))
```

# <a name="net"></a>[.NET](#tab/net)

Чтобы включить обратимое удаление, обновите свойства клиентской службы BLOB-объектов:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Чтобы восстановить большие двоичные объекты, которые были случайно удалены, вызовите для них отмену удаления. Помните, что при вызове операции **Undelete Blob** как в активных, так и в обратимо удаленных больших двоичных объектах все связанные обратимо удаленные моментальные снимки будут восстановлены в качестве активных. В следующем примере вызывается отмена удаления всех обратимо удаленных и активных больших двоичных объектов в контейнере:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Чтобы выполнить восстановление до определенной версии большого двоичного объекта, сначала необходимо вызвать отмену удаления большого двоичного объекта, а затем скопировать требуемый моментальный снимок в большой двоичный объект. В следующем примере блочный BLOB-объект восстанавливается до недавно созданного моментального снимка:

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob    
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```

---

## <a name="next-steps"></a>Дальнейшие действия

- [Обратимое удаление для хранилища BLOB-объектов](soft-delete-overview.md)
- [Управление версиями BLOB-объектов (Предварительная версия)](versioning-overview.md)