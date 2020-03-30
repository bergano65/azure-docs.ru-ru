---
title: Создание учетной записи, которая поддерживает управляемые клиентом ключи для таблиц и очередей
titleSuffix: Azure Storage
description: Узнайте, как создать учетную запись хранения, поддерживающую настройку ключей, управляемых клиентами для таблиц и очередей. Используйте шаблон Azure CLI или шаблон менеджера ресурсов Azure для создания учетной записи, которая опирается на ключ шифрования учетной записи для шифрования Azure Storage. Затем можно настроить управляемые клиентом ключи для учетной записи.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 09558a8d1e4e2dc68cefd2c870f54e008d10b97b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083554"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Создание учетной записи, которая поддерживает управляемые клиентом ключи для таблиц и очередей

Azure Storage шифрует все данные в учетной записи хранения в состоянии покоя. По умолчанию в хранилищах очереди и таблице хранения используется ключ, который используется для службы и управляется корпорацией Майкрософт. Вы также можете использовать управляемые клиентом ключи для шифрования очереди или данных таблицы. Чтобы использовать управляемые клиентом ключи с очередями и таблицами, необходимо сначала создать учетную запись хранения, которая использует ключ шифрования, который прикрыт для учетной записи, а не для службы. После создания учетной записи, используюейейейой ключ шифрования учетной записи для данных очереди и таблицы, можно настроить управляемые клиентом ключи с помощью Azure Key Vault для этой учетной записи хранения.

В этой статье описывается, как создать учетную запись хранения, которая опирается на ключ, который приспозывается к учетной записи. Когда учетная запись была создана впервые, корпорация Майкрософт использует ключ учетной записи для шифрования данных в учетной записи, а корпорация Майкрософт управляет ключом. Впоследствии вы можете настроить управляемые клиентом ключи для учетной записи, чтобы воспользоваться этими преимуществами, включая возможность предоставить свои собственные ключи, обновить ключевую версию, повернуть клавиши и отозвать элементы управления доступом.

## <a name="about-the-feature"></a>О функции

Чтобы создать учетную запись хранения, которая опирается на ключ шифрования учетной записи для хранения очереди и таблицы, необходимо сначала зарегистрироваться, чтобы использовать эту функцию в Azure. Из-за ограниченной емкости следует знать, что может пройти несколько месяцев, прежде чем запросы на доступ будут одобрены.

Можно создать учетную запись хранения, которая опирается на ключ шифрования учетной записи для хранения очереди и таблицы в следующих регионах:

- Восточная часть США
- Центрально-южная часть США
- западная часть США 2  

### <a name="register-to-use-the-account-encryption-key"></a>Зарегистрируйтесь, чтобы использовать ключ шифрования учетной записи

Чтобы зарегистрироваться, чтобы использовать ключ шифрования учетной записи с хранилищем очереди или таблицы, используйте PowerShell или Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Чтобы зарегистрироваться в PowerShell, позвоните в команду [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Чтобы зарегистрироваться в Azure CLI, позвоните в команду [регистра функций az.](/cli/azure/feature#az-feature-register)

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Шаблон](#tab/template)

Недоступно

---

### <a name="check-the-status-of-your-registration"></a>Проверьте состояние вашей регистрации

Чтобы проверить состояние регистрации для хранения очереди или таблицы, используйте PowerShell или Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Чтобы проверить статус вашей регистрации в PowerShell, позвоните в команду [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Чтобы проверить статус регистрации в Azure CLI, позвоните в команду [функции az.](/cli/azure/feature#az-feature-show)

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Шаблон](#tab/template)

Недоступно

---

### <a name="re-register-the-azure-storage-resource-provider"></a>Перерегистрация поставщика ресурсов ресурсов Azure Storage

После утверждения регистрации необходимо перерегистрировать поставщика ресурсов Azure Storage. Используйте PowerShell или Azure CLI для перерегистрации поставщика ресурсов.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Чтобы перерегистрировать поставщика ресурсов в PowerShell, позвоните в команду [Register-AzResourceProvider.](/powershell/module/az.resources/register-azresourceprovider)

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Чтобы перерегистрировать поставщика ресурсов в Azure CLI, позвоните в команду [регистра аз-провайдера.](/cli/azure/provider#az-provider-register)

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Шаблон](#tab/template)

Недоступно

---

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Создание учетной записи, используюейейейейбый ключ шифрования учетной записи

Необходимо настроить новую учетную запись хранения, чтобы использовать ключ шифрования учетной записи для очередей и таблиц во время создания учетной записи. Область ключа шифрования не может быть изменена после создания учетной записи.

Учетная запись хранения должна быть типа общего назначения v2. Вы можете создать учетную запись хранения и настроить ее, чтобы полагаться на ключ шифрования учетной записи, используя либо Azure CLI, либо шаблон Менеджера ресурсов Azure.

> [!NOTE]
> Только емкость и таблица хранения может быть дополнительно настроен для шифрования данных с ключом шифрования учетной записи, когда учетная запись создана. Файлы хранения Blob и Azure всегда используют ключ шифрования учетной записи для шифрования данных.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Чтобы использовать PowerShell для создания учетной записи, которая опирается на ключ шифрования учетной записи, убедитесь, что вы установили модуль Azure PowerShell, версия 3.4.0 или позже. Для получения дополнительной [информации см.](/powershell/azure/install-az-ps)

Затем создайте учетную запись хранения v2 общего назначения, позвонив в команду [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) с соответствующими параметрами:

- Включите опцию `-EncryptionKeyTypeForQueue` и `Account` установите ее значение для использования ключа шифрования учетной записи для шифрования данных в хранилище Очереди.
- Включите опцию `-EncryptionKeyTypeForTable` и `Account` установите ее значение для использования ключа шифрования учетной записи для шифрования данных в хранилище таблицы.

В следующем примере показано, как создать учетную запись хранения v2 общего назначения, настроенную для геоизмового хранилища (RA-GRS) общего назначения и используюую ключ шифрования учетной записи для шифрования данных как для хранения очереди, так и для таблицы. Не забудьте заменить значения заполнителя в скобках на свои собственные значения:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Чтобы использовать Azure CLI для создания учетной записи для хранения данных, которая опирается на ключ шифрования учетной записи, убедитесь, что вы установили версию Azure CLI 2.0.80 или позже. Дополнительные сведения см. в статье [Установка Azure CLI](/cli/azure/install-azure-cli).

Далее создайте учетную запись хранения v2 общего назначения, [позвонив в учетную запись хранилища az, создайте](/cli/azure/storage/account#az-storage-account-create) команду с соответствующими параметрами:

- Включите опцию `--encryption-key-type-for-queue` и `Account` установите ее значение для использования ключа шифрования учетной записи для шифрования данных в хранилище Очереди.
- Включите опцию `--encryption-key-type-for-table` и `Account` установите ее значение для использования ключа шифрования учетной записи для шифрования данных в хранилище таблицы.

В следующем примере показано, как создать учетную запись хранения v2 общего назначения, настроенную для геоизмового хранилища (RA-GRS) общего назначения и используюую ключ шифрования учетной записи для шифрования данных как для хранения очереди, так и для таблицы. Не забудьте заменить значения заполнителя в скобках на свои собственные значения:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[Шаблон](#tab/template)

Следующий пример JSON создает учетную запись хранения v2 общего назначения, которая настроена для геоизмового хранилища (RA-GRS) общего назначения и которая использует ключ шифрования учетной записи для шифрования данных как для хранения очереди, так и для таблицы. Не забудьте заменить значения заполнителя в угловых скобках на собственные значения:

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

После создания учетной записи, которая опирается на ключ шифрования учетной записи, см. одну из следующих статей для настройки ключей, управляемых клиентом, с помощью Azure Key Vault:

- [Налаживание ключей, управляемых клиентами, с помощью портала Azure Key Vault](storage-encryption-keys-portal.md)
- [Налаживание ключей, управляемых клиентами, с помощью Хранилища ключей Azure с помощью PowerShell](storage-encryption-keys-powershell.md)
- [Настройка ключей, управляемых клиентами, с помощью Azure Key Vault с помощью Azure CLI](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>Проверить ключ шифрования учетной записи

Чтобы убедиться, что служба в учетной записи хранилища использует ключ шифрования учетной записи, позвоните в команду [учетной записи](/cli/azure/storage/account#az-storage-account-show) хранилища Azure CLI az. Эта команда возвращает набор свойств учетной записи хранилища и их значения. Ищите `keyType` поле для каждой службы в свойстве `Account`шифрования и убедитесь, что она установлена на .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Чтобы убедиться, что служба в учетной записи хранилища использует ключ шифрования учетной записи, позвоните в команду [Get-AzStorageAccount.](/powershell/module/az.storage/get-azstorageaccount) Эта команда возвращает набор свойств учетной записи хранилища и их значения. Ищите `KeyType` поле для каждой службы в свойстве `Encryption` и убедитесь, что она установлена на `Account`.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Чтобы убедиться, что служба в учетной записи хранилища использует ключ шифрования учетной записи, позвоните в команду [учетной записи аз.](/cli/azure/storage/account#az-storage-account-show) Эта команда возвращает набор свойств учетной записи хранилища и их значения. Ищите `keyType` поле для каждой службы в свойстве `Account`шифрования и убедитесь, что она установлена на .

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Шаблон](#tab/template)

Недоступно

---

## <a name="next-steps"></a>Дальнейшие действия

- [Шифрование хранилища Azure для данных в состоянии покоя](storage-service-encryption.md) 
- [Что такое Убежище ключей Azure?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
