---
title: Создание учетной записи хранения с включенным шифрованием инфраструктуры для двойного шифрования данных
titleSuffix: Azure Storage
description: Клиенты, которым требуется более высокий уровень защиты данных, могут также включить 256-разрядное шифрование AES на уровне инфраструктуры службы хранилища Azure. При включении шифрования инфраструктуры данные в учетной записи хранения шифруются дважды с помощью двух разных алгоритмов шифрования и двух разных ключей.
services: storage
author: tamram
ms.service: storage
ms.date: 07/08/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: references_regions
ms.openlocfilehash: edeb184af1c1260a456ed3de7064805526629de8
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225370"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>Создание учетной записи хранения с включенным шифрованием инфраструктуры для двойного шифрования данных

Служба хранилища Azure автоматически шифрует все данные в учетной записи хранения на уровне обслуживания с помощью 256-разрядного шифрования AES, одного из наиболее поддаваемых блочных шифров и совместимого с FIPS 140-2. Клиенты, которым требуется более высокий уровень защиты данных, могут также включить 256-разрядное шифрование AES на уровне инфраструктуры службы хранилища Azure. Если включено шифрование инфраструктуры, данные в учетной записи хранения шифруются дважды &mdash; на уровне службы и один раз на уровне инфраструктуры &mdash; с двумя различными алгоритмами шифрования и двумя разными ключами. Двойное шифрование данных службы хранилища Azure позволяет защититься от ситуации, когда один из алгоритмов шифрования или ключей может быть скомпрометирован. В этом сценарии дополнительный уровень шифрования сохраняется для защиты данных.

Шифрование на уровне службы поддерживает использование ключей, управляемых корпорацией Майкрософт, или ключей, управляемых клиентом, с помощью Azure Key Vault. Шифрование на уровне инфраструктуры основано на ключах, управляемых корпорацией Майкрософт, и всегда использует отдельный ключ. Дополнительные сведения об управлении ключами с помощью шифрования службы хранилища Azure см. в разделе [сведения об управлении ключами шифрования](storage-service-encryption.md#about-encryption-key-management).

Чтобы удвоить шифрование данных, необходимо сначала создать учетную запись хранения, настроенную для шифрования инфраструктуры. В этой статье описывается, как создать учетную запись хранения, которая обеспечивает шифрование инфраструктуры.

## <a name="about-the-feature"></a>О функции

Чтобы создать учетную запись хранения с включенным шифрованием инфраструктуры, сначала необходимо зарегистрироваться, чтобы использовать эту функцию в Azure. В связи с ограниченной емкостью имейте в виду, что до утверждения запросов на доступ может пройти несколько месяцев.

Вы можете создать учетную запись хранения с включенным шифрованием инфраструктуры в следующих регионах:

- Восточная часть США
- Центрально-южная часть США
- западная часть США 2

### <a name="register-to-use-infrastructure-encryption"></a>Регистрация для использования шифрования инфраструктуры

Чтобы зарегистрироваться для использования шифрования инфраструктуры в службе хранилища Azure, используйте PowerShell или Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Чтобы зарегистрироваться в PowerShell, вызовите команду [Register-азпровидерфеатуре](/powershell/module/az.resources/register-azproviderfeature) .

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Чтобы зарегистрироваться в Azure CLI, вызовите команду [AZ Feature Register](/cli/azure/feature#az-feature-register) .

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

# <a name="template"></a>[Шаблон](#tab/template)

Недоступно

---

### <a name="check-the-status-of-your-registration"></a>Проверка состояния регистрации

Чтобы проверить состояние регистрации для шифрования инфраструктуры, используйте PowerShell или Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Чтобы проверить состояние регистрации с помощью PowerShell, вызовите команду [Get-азпровидерфеатуре](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Чтобы проверить состояние регистрации с помощью Azure CLI, вызовите команду [AZ Feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

# <a name="template"></a>[Шаблон](#tab/template)

Недоступно

---

### <a name="re-register-the-azure-storage-resource-provider"></a>Повторная регистрация поставщика ресурсов службы хранилища Azure

После утверждения регистрации необходимо повторно зарегистрировать поставщик ресурсов службы хранилища Azure. Используйте PowerShell или Azure CLI для повторной регистрации поставщика ресурсов.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Чтобы повторно зарегистрировать поставщик ресурсов с помощью PowerShell, вызовите команду [Register-азресаурцепровидер](/powershell/module/az.resources/register-azresourceprovider) .

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Чтобы повторно зарегистрировать поставщик ресурсов с Azure CLI, вызовите команду [AZ Provider Register](/cli/azure/provider#az-provider-register) .

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Шаблон](#tab/template)

Недоступно

---

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>Создание учетной записи с включенным шифрованием инфраструктуры

Необходимо настроить учетную запись хранения для использования шифрования инфраструктуры во время создания учетной записи. После создания учетной записи шифрование инфраструктуры не может быть включено или отключено.

Учетная запись хранения должна иметь тип общего назначения v2. Вы можете создать учетную запись хранения и настроить ее для включения шифрования инфраструктуры с помощью PowerShell, Azure CLI или шаблона Azure Resource Manager.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Чтобы использовать PowerShell для создания учетной записи хранения с включенным шифрованием инфраструктуры, убедитесь, что установлен [модуль AZ. Storage PowerShell](https://www.powershellgallery.com/packages/Az.Storage), версия 2.2.0 или более поздняя. Дополнительные сведения см. в статье [Установка Azure PowerShell](/powershell/azure/install-az-ps).

Затем создайте учетную запись хранения общего назначения версии 2, вызвав команду [New-азсторажеаккаунт](/powershell/module/az.storage/new-azstorageaccount) . Включите `-RequireInfrastructureEncryption` параметр для включения шифрования инфраструктуры.

В следующем примере показано, как создать учетную запись хранения общего назначения v2, настроенную для геоизбыточного хранилища с доступом на чтение (RA-GRS), и включить шифрование инфраструктуры для двойного шифрования данных. Не забудьте заменить значения заполнителей в квадратных скобках собственными значениями:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Чтобы использовать Azure CLI для создания учетной записи хранения с включенным шифрованием инфраструктуры, убедитесь, что установлена версия Azure CLI 2.8.0 или более поздняя. Дополнительные сведения см. в статье [Установка Azure CLI](/cli/azure/install-azure-cli).

Затем создайте учетную запись хранения общего назначения версии 2, вызвав команду [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create) и включив `--require-infrastructure-encryption option` для включения шифрования инфраструктуры.

В следующем примере показано, как создать учетную запись хранения общего назначения v2, настроенную для геоизбыточного хранилища с доступом на чтение (RA-GRS), и включить шифрование инфраструктуры для двойного шифрования данных. Не забудьте заменить значения заполнителей в квадратных скобках собственными значениями:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --require-infrastructure-encryption
```

# <a name="template"></a>[Шаблон](#tab/template)

В следующем примере JSON создается учетная запись хранения общего назначения версии 2, настроенная для геоизбыточного хранилища с доступом на чтение (RA-GRS) и поддерживающая шифрование инфраструктуры для двойного шифрования данных. Не забудьте заменить значения заполнителей в квадратных скобках собственными значениями:

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
                "keySource": "Microsoft.Storage",
                "requireInfrastructureEncryption": true,
                "services": {
                    "blob": { "enabled": true },
                    "file": { "enabled": true }
              }
            }
        }
    }
],
```

---

## <a name="verify-that-infrastructure-encryption-is-enabled"></a>Проверка включения шифрования инфраструктуры

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Чтобы проверить, включено ли шифрование инфраструктуры для учетной записи хранения, вызовите команду [Get-азсторажеаккаунт](/powershell/module/az.storage/get-azstorageaccount) . Эта команда возвращает набор свойств учетной записи хранения и их значений. Получите `RequireInfrastructureEncryption` поле в `Encryption` свойстве и убедитесь, что оно имеет значение `True` .

В следующем примере извлекается значение `RequireInfrastructureEncryption` Свойства. Не забудьте заменить значения заполнителей в угловых скобках собственными значениями:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Чтобы проверить, включено ли шифрование инфраструктуры для учетной записи хранения, вызовите команду [AZ Storage Account-шоу](/cli/azure/storage/account#az-storage-account-show) . Эта команда возвращает набор свойств учетной записи хранения и их значений. Найдите `requireInfrastructureEncryption` поле в `encryption` свойстве и убедитесь, что оно имеет значение `true` .

В следующем примере извлекается значение `requireInfrastructureEncryption` Свойства. Не забудьте заменить значения заполнителей в угловых скобках собственными значениями:

```azurecli-interactive
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Шаблон](#tab/template)

Недоступно

---

## <a name="next-steps"></a>Дальнейшие действия

- [Шифрование службы хранилища Azure для неактивных данных](storage-service-encryption.md)
- [Использование управляемых клиентом ключей с Azure Key Vault для управления шифрованием службы хранилища Azure](encryption-customer-managed-keys.md)