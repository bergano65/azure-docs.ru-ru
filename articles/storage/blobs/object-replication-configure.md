---
title: Настройка репликации объектов (предварительная версия)
titleSuffix: Azure Storage
description: Сведения о том, как настроить репликацию объектов для асинхронного копирования блочных BLOB-объектов из контейнера в одной учетной записи хранения в другую.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 9964a10b85d9f388a4498766c6e81ee52498aa37
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89076180"
---
# <a name="configure-object-replication-for-block-blobs-preview"></a>Настройка репликации объектов для блочных BLOB-объектов (предварительная версия)

Репликация объектов (предварительная версия) асинхронно копирует блочные BLOB-объекты между исходной и целевой учетными записями хранения. Дополнительные сведения о репликации объектов см. в разделе [Репликация объектов (предварительная версия)](object-replication-overview.md).

При настройке репликации объектов создается политика репликации, указывающая исходную учетную запись хранения и целевую учетную запись. Политика репликации включает одно или несколько правил, задающих исходный и целевой контейнеры, и указывает, какие блочные BLOB-объекты в исходном контейнере будут реплицированы.

В этой статье описано, как настроить репликацию объектов для учетной записи хранения с помощью портала Azure, PowerShell или Azure CLI. Для настройки репликации объектов можно также использовать одну из клиентских библиотек поставщика ресурсов службы хранилища Azure.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-a-replication-policy-and-rules"></a>Создание правил и политики репликации

Перед настройкой репликации объектов создайте исходную и целевую учетные записи хранения, если они еще не существуют. Они обе должны быть учетными записями хранения общего назначения версии 2. Дополнительные сведения см. в разделе [Создание учетной записи хранения Azure](../common/storage-account-create.md).

Учетная запись хранения может выступать в качестве исходной учетной записи для двух целевых учетных записей. У целевой учетной записи может быть не более двух исходных учетных записей. Исходная и целевая учетные записи могут находиться в разных регионах. Можно настроить отдельные политики репликации для репликации данных в каждую из целевых учетных записей.

Прежде чем начать, убедитесь, что зарегистрированы следующие предварительные версии компонентов:

- [Репликация объектов (предварительная версия)](object-replication-overview.md)
- [Управление версиями BLOB-объектов (предварительная версия)](versioning-overview.md)
- [Поддержка канала изменений в хранилище BLOB-объектов Azure (предварительная версия)](storage-blob-change-feed.md)

# <a name="azure-portal"></a>[Портал Azure](#tab/portal)

Перед настройкой репликации объектов на портале Azure создайте исходный и целевой контейнеры в соответствующих учетных записях хранения, если они еще не существуют. Кроме того, включите поддержку управления версиями BLOB-объектов и веб-канала изменений в исходной учетной записи и управление версиями BLOB-объектов в целевой учетной записи.

Чтобы создать политику репликации на портале Azure, сделайте следующее.

1. Перейдите к исходной учетной записи хранения на портале Azure.
1. В разделе **Служба BLOB-объектов**выберите **репликация объектов**.
1. Выберите **Настройка правил репликации**.
1. Выберите целевую подписку и учетную запись хранения.
1. В разделе **Пары контейнеров** выберите исходный контейнер в исходной учетной записи и целевой контейнер в целевой учетной записи. Для каждой политики репликации можно создать до 10 пар контейнеров.

    На следующем рисунке показан набор правил репликации.

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="Снимок экрана, показывающий правила репликации на портале Azure":::

1. При необходимости укажите один или несколько фильтров, чтобы скопировать только BLOB-объекты, соответствующие шаблону префикса. Например, если указать префикс `b`, реплицируются только те BLOB-объекты, имя которых начинается с этой буквы. В качестве части префикса можно указать виртуальный каталог. Строка префикса не поддерживает подстановочные знаки.

    На следующем рисунке показаны фильтры, которые ограничивают то, какие именно BLOB-объекты копируются в рамках правила репликации.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="Снимок экрана, показывающий фильтры для правила репликации":::

1. По умолчанию в области копирования задано копирование только новых объектов. Чтобы скопировать все объекты в контейнере или скопировать объекты, начиная с настраиваемой даты и времени, выберите ссылку **Изменить** и настройте область копирования для пары контейнеров.

    На следующем рисунке показана пользовательская область копирования.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="Снимок экрана, показывающий пользовательскую область копирования для репликации объектов":::

1. Выберите **Сохранить и применить**, чтобы создать политику репликации и начать репликацию данных.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Чтобы создать политику репликации с помощью PowerShell, сначала установите версию [2.0.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/2.0.1-preview) или более позднюю версию модуля "az. Storage PowerShell". Чтобы установить предварительную версию модуля, сделайте следующее.

1. Удалите все предыдущие установки Azure PowerShell из Windows с помощью параметра **Apps & features** (Приложения и компоненты) в разделе **Параметры**.

1. Убедитесь, что у вас установлена последняя версия PowerShellGet. Откройте окно Windows PowerShell и выполните следующую команду, чтобы установить последнюю версию:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

    После установки PowerShellGet закройте и снова откройте окно PowerShell.

1. Установите Azure PowerShell последней версии:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Установите предварительную версию модуля Az.Storage:

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 2.0.1-preview -AllowPrerelease -AllowClobber -Force
    ```

Дополнительные сведения об установке Azure PowerShell см. в статье [Установка Azure PowerShell с помощью PowerShellGet](/powershell/azure/install-az-ps).

В следующем примере показано, как создать политику репликации для исходной и целевой учетных записей. Не забудьте заменить значения в угловых скобках собственными значениями.

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2020-05-10T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Чтобы создать политику репликации с помощью Azure CLI, сначала установите предварительную версию расширения для службы хранилища Azure:

```azurecli
az extension add -n storage-or-preview
```

Затем выполните вход с помощью своих учетных данных Azure

```azurecli
az login
```

Включите управление версиями BLOB-объектов в исходной и целевой учетных записях хранения и включите канал изменений в исходной учетной записи. Не забудьте заменить значения в угловых скобках собственными значениями.

```azurecli
az storage blob service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning

az storage blob service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-change-feed

az storage blob service-properties update \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

Создайте исходный и целевой контейнеры в соответствующих учетных записях хранения.

```azurecli
az storage container create \
    --account-name <source-storage-account> \
    --name source-container3 \
    --auth-mode login
az storage container create \
    --account-name <source-storage-account> \
    --name source-container4 \
    --auth-mode login

az storage container create \
    --account-name <dest-storage-account> \
    --name source-container3 \
    --auth-mode login
az storage container create \
    --account-name <dest-storage-account> \
    --name source-container4 \
    --auth-mode login
```

Создайте политику репликации и связанные с ней правила для целевой учетной записи.

```azurecli
az storage account or-policy create \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container3 \
    --destination-container dest-container3 \
    --min-creation-time '2020-05-10T00:00:00Z' \
    --prefix-match a

az storage account or-policy rule add \
    --account-name <dest-storage-account> \
    --destination-container dest-container4 \
    --policy-id <policy-id> \
    --resource-group <resource-group> \
    --source-container source-container4 \
    --prefix-match b
```

Создайте политику в исходной учетной записи, используя идентификатор политики.

```azurecli
az storage account or-policy show \
    --resource-group <resource-group> \
    --name <dest-storage-account> \
    --policy-id <policy-id> |
    --az storage account or-policy create --resource-group <resource-group> \
    --name <source-storage-account> \
    --policy "@-"
```

---

## <a name="remove-a-replication-policy"></a>Удаление политики репликации

Чтобы удалить политику репликации и связанные с ней правила, используйте портал Azure, PowerShell или CLI.

# <a name="azure-portal"></a>[Портал Azure](#tab/portal)

Чтобы удалить политику репликации на портале Azure, сделайте следующее.

1. Перейдите к исходной учетной записи хранения на портале Azure.
1. В разделе **Параметры** выберите **Репликация объектов**.
1. Нажмите кнопку **Дополнительно** рядом с именем политики.
1. Выберите **Удалить правила**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Чтобы удалить политику репликации, удалите ее из исходной и целевой учетных записей. При удалении политики также удаляются все связанные с ней правила.

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Чтобы удалить политику репликации, удалите ее из исходной и целевой учетных записей. При удалении политики также удаляются все связанные с ней правила.

```azurecli
az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Дальнейшие действия

- [Общие сведения о репликации объектов (предварительная версия)](object-replication-overview.md)
