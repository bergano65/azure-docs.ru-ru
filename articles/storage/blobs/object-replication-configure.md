---
title: Настройка репликации объектов
titleSuffix: Azure Storage
description: Сведения о том, как настроить репликацию объектов для асинхронного копирования блочных BLOB-объектов из контейнера в одной учетной записи хранения в другую.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/10/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 4fb616860cb1e85c6249329f3679de0d29b72e61
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90018838"
---
# <a name="configure-object-replication-for-block-blobs"></a>Настройка репликации объекта для блочных BLOB-объектов

Репликация объектов Асинхронно копирует блочные BLOB-объекты между исходной и целевой учетными записями хранения. Дополнительные сведения о репликации объектов см. в разделе [репликация объектов](object-replication-overview.md).

При настройке репликации объектов создается политика репликации, указывающая исходную учетную запись хранения и целевую учетную запись. Политика репликации включает одно или несколько правил, задающих исходный и целевой контейнеры, и указывает, какие блочные BLOB-объекты в исходном контейнере будут реплицированы.

В этой статье описано, как настроить репликацию объектов для учетной записи хранения с помощью портала Azure, PowerShell или Azure CLI. Для настройки репликации объектов можно также использовать одну из клиентских библиотек поставщика ресурсов службы хранилища Azure.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-a-replication-policy-and-rules"></a>Создание правил и политики репликации

Перед настройкой репликации объектов создайте исходную и целевую учетные записи хранения, если они еще не существуют. Они обе должны быть учетными записями хранения общего назначения версии 2. Дополнительные сведения см. в разделе [Создание учетной записи хранения Azure](../common/storage-account-create.md).

Для репликации объектов требуется, чтобы Управление версиями BLOB-объектов было включено как для исходной, так и для целевой учетной записи, а этот канал изменений большого двоичного объекта включен для исходной учетной записи. Дополнительные сведения о управлении версиями BLOB-объектов см. в разделе [Управление версиями BLOB](versioning-overview.md)-объектов. Дополнительные сведения о веб-канале изменений см. [в статье поддержка веб-канала изменений в хранилище BLOB-объектов Azure](storage-blob-change-feed.md). Помните, что включение этих функций может привести к дополнительным затратам.

Учетная запись хранения может выступать в качестве исходной учетной записи для двух целевых учетных записей. Исходная и Целевая учетные записи могут находиться в одном регионе или в разных регионах. Они также могут находиться в разных подписках и в разных клиентах Azure Active Directory (Azure AD). Для каждой пары учетных записей может быть создана только одна политика репликации.

При настройке репликации объектов создайте политику репликации для целевой учетной записи с помощью поставщика ресурсов службы хранилища Azure. После создания политики репликации Служба хранилища Azure назначает ей идентификатор политики. Затем необходимо связать эту политику репликации с исходной учетной записью с помощью идентификатора политики. Чтобы репликация была выполнена, идентификатор политики в исходной и целевой учетной записи должен быть одинаковым.

Чтобы настроить политику репликации объектов для учетной записи хранения, необходимо назначить роль **участника** Azure Resource Manager, областью действия которой является уровень учетной записи хранения или выше. Дополнительные сведения см. в статье [встроенные роли Azure](../../role-based-access-control/built-in-roles.md) в документации по управлению доступом на основе РОЛЕЙ (RBAC) в Azure.

### <a name="configure-object-replication-when-you-have-access-to-both-storage-accounts"></a>Настройка репликации объектов при наличии доступа к обеим учетным записям хранения

Если у вас есть доступ как к исходной, так и к целевой учетной записи хранения, можно настроить политику репликации объектов для обеих учетных записей.

Перед настройкой репликации объектов на портале Azure создайте исходный и целевой контейнеры в соответствующих учетных записях хранения, если они еще не существуют. Кроме того, включите управление версиями BLOB-объектов и веб-канал изменений в исходной учетной записи и включите управление версиями BLOB-объектов в целевой учетной записи.

# <a name="azure-portal"></a>[Портал Azure](#tab/portal)

Портал Azure автоматически создает политику в исходной учетной записи после ее настройки для целевой учетной записи.

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

    На следующем рисунке показана пользовательская область копирования, которая копирует объекты из заданной даты и времени.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="Снимок экрана, показывающий пользовательскую область копирования для репликации объектов":::

1. Выберите **Сохранить и применить**, чтобы создать политику репликации и начать репликацию данных.

После настройки репликации объектов портал Azure отображает политику и правила репликации, как показано на следующем рисунке.

:::image type="content" source="media/object-replication-configure/object-replication-policies-portal.png" alt-text="Снимок экрана, показывающий политику репликации объектов в портал Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Чтобы создать политику репликации с помощью PowerShell, сначала установите версию [2.5.0](https://www.powershellgallery.com/packages/Az.Storage/2.5.0) или более позднюю из модуля "az. Storage PowerShell". Дополнительные сведения об установке Azure PowerShell см. в статье [Установка Azure PowerShell с помощью PowerShellGet](/powershell/azure/install-az-ps).

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

Чтобы создать политику репликации с Azure CLI, сначала установите Azure CLI версии 2.11.1 или более поздней. Дополнительные сведения см. в статье [Приступая к работе с Azure CLI](/cli/azure/get-started-with-azure-cli).

Затем включите управление версиями BLOB-объектов в исходных и целевых учетных записях хранения и включите канал изменений в исходной учетной записи. Не забудьте заменить значения в угловых скобках собственными значениями.

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-change-feed

az storage account blob-service-properties update \
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
    --account-name <dest-storage-account> \
    --policy-id <policy-id> |
    az storage account or-policy create --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --policy "@-"
```

---

### <a name="configure-object-replication-when-you-have-access-only-to-the-destination-account"></a>Настройка репликации объектов при наличии доступа только к целевой учетной записи

Если у вас нет разрешений на доступ к исходной учетной записи хранения, можно настроить репликацию объектов в конечной учетной записи и предоставить JSON-файл, содержащий определение политики для другого пользователя, чтобы создать ту же политику в исходной учетной записи. Например, если исходная учетная запись находится в другом клиенте Azure AD из целевой учетной записи, используйте этот подход для настройки репликации объектов. 

Помните, что для создания политики необходимо назначить роль **участника** Azure Resource Manager, область действия которой ограничена уровнем целевой учетной записи хранения или выше. Дополнительные сведения см. в статье [встроенные роли Azure](../../role-based-access-control/built-in-roles.md) в документации по управлению доступом на основе РОЛЕЙ (RBAC) в Azure.

В следующей таблице перечислены значения, которые следует использовать для идентификатора политики в файле JSON в каждом сценарии.

| При создании JSON файла для этой учетной записи... | Задайте для идентификатора политики это значение... |
|-|-|
| Целевая учетная запись | Строковое значение *по умолчанию*. Служба хранилища Azure создаст идентификатор политики. |
| Исходная учетная запись | Идентификатор политики, возвращаемый при скачивании JSON-файла, содержащего правила, определенные в конечной учетной записи. |

В следующем примере определяется политика репликации для целевой учетной записи с одним правилом, соответствующим префиксу *b* , и устанавливается минимальное время создания для реплицируемых больших двоичных объектов. Не забудьте заменить значения в угловых скобках собственными значениями.

```json
{
  "properties": {
    "policyId": "default",
    "sourceAccount": "<source-account>",
    "destinationAccount": "<dest-account>",
    "rules": [
      {
        "ruleId": "default",
        "sourceContainer": "<source-container>",
        "destinationContainer": "<destination-container>",
        "filters": {
          "prefixMatch": [
            "b"
          ],
          "minCreationTime": "2020-08-028T00:00:00Z"
        }
      }
    ]
  }
}
```

# <a name="azure-portal"></a>[Портал Azure](#tab/portal)

Чтобы настроить репликацию объектов в конечной учетной записи с помощью JSON-файла в портал Azure, выполните следующие действия.

1. Создайте локальный JSON-файл, определяющий политику репликации в целевой учетной записи. Задайте для поля **policyId** значение **по умолчанию** , чтобы служба хранилища Azure определила идентификатор политики.

    Простой способ создать JSON-файл, определяющий политику репликации, — сначала создать тестовую политику репликации между двумя учетными записями хранения в портал Azure. После этого можно скачать правила репликации и при необходимости изменить файл JSON.

1. Перейдите к параметрам **репликации объектов** для целевой учетной записи в портал Azure.
1. Выберите **отправить правила репликации**.
1. Отправьте JSON файл. Портал Azure отображает политику и правила, которые будут созданы, как показано на следующем рисунке.

    :::image type="content" source="media/object-replication-configure/replication-rules-upload-portal.png" alt-text="Снимок экрана, показывающий, как отправить JSON-файл для определения политики репликации":::

1. Нажмите кнопку **Отправить** , чтобы создать политику репликации для целевой учетной записи.

Затем можно скачать JSON-файл, содержащий определение политики, которое можно предоставить другому пользователю для настройки исходной учетной записи. Чтобы скачать этот файл JSON, выполните следующие действия.

1. Перейдите к параметрам **репликации объектов** для целевой учетной записи в портал Azure.
1. Нажмите кнопку **Дополнительно** рядом с политикой, которую вы хотите скачать, а затем выберите **правила загрузки**, как показано на следующем рисунке.

    :::image type="content" source="media/object-replication-configure/replication-rules-download-portal.png" alt-text="Снимок экрана, показывающий, как скачать правила репликации в JSON-файл":::

1. Сохраните файл JSON на локальном компьютере, чтобы предоставить общий доступ другому пользователю для настройки политики в исходной учетной записи.

Скачанный файл JSON содержит идентификатор политики, созданный службой хранилища Azure для политики в целевой учетной записи. Для настройки репликации объектов в исходной учетной записи необходимо использовать один и тот же идентификатор политики.

Помните, что отправка JSON-файла для создания политики репликации для целевой учетной записи с помощью портал Azure не приводит к автоматическому созданию той же политики в исходной учетной записи. Другой пользователь должен создать политику в исходной учетной записи, прежде чем служба хранилища Azure начнет репликацию объектов.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Чтобы скачать файл JSON, содержащий определение политики репликации для целевой учетной записи из PowerShell, вызовите команду [Get-азсторажеобжектрепликатионполици](/powershell/module/az.storage/get-azstorageobjectreplicationpolicy) , чтобы вернуть политику. Затем преобразуйте политику в формат JSON и сохраните ее в виде локального файла, как показано в следующем примере. Не забудьте заменить значения в угловых скобках и пути к файлам собственными значениями:

```powershell
$rgName = "<resource-group>"
$destAccountName = "<destination-storage-account>"

$destPolicy = Get-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName
$destPolicy | ConvertTo-Json -Depth 5 > c:\temp\json.txt
```

Чтобы использовать JSON файл для определения политики репликации в исходной учетной записи с помощью PowerShell, извлеките локальный файл и преобразуйте его из JSON в объект. Затем вызовите команду [Set-азсторажеобжектрепликатионполици](/powershell/module/az.storage/set-azstorageobjectreplicationpolicy) , чтобы настроить политику в исходной учетной записи, как показано в следующем примере. Не забудьте заменить значения в угловых скобках и пути к файлам собственными значениями:

```powershell
$object = Get-Content -Path C:\temp\json.txt | ConvertFrom-Json
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $object.PolicyId `
    -SourceAccount $object.SourceAccount `
    -DestinationAccount $object.DestinationAccount `
    -Rule $object.Rules
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Недоступно

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

- [Общие сведения о репликации объектов](object-replication-overview.md)
- [Включение управления версиями BLOB-объектов и работа с ним](versioning-enable.md)
- [Обработка веб-канала изменений в хранилище BLOB-объектов Azure](storage-blob-change-feed-how-to.md)
