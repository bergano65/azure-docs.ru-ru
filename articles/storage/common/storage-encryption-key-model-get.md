---
title: Определить, какая модель ключей шифрования используется для учетной записи хранилища
titleSuffix: Azure Storage
description: Используйте портал Azure, PowerShell или Azure CLI, чтобы проверить, как управляются ключи шифрования для учетной записи хранилища. Ключи могут управляться корпорацией Майкрософт (по умолчанию) или клиентом. Ключи, управляемые клиентом, должны храниться в Хранилище ключей Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/13/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0df0ba4ce76d249bcb4738b41c94677e061f14ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409867"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>Определить, какая ключевая модель шифрования Azure Хранения используется для учетной записи хранилища

Данные в учетной записи хранилища автоматически шифруются Azure Storage. Шифрование Azure Storage предлагает два варианта управления ключами шифрования на уровне учетной записи хранилища:

- **Ключи, управляемые корпорацией Майкрософт.** По умолчанию корпорация Майкрософт управляет ключами, используемыми для шифрования учетной записи хранилища.
- **Ключи, управляемые клиентом.** Вы можете по желанию управлять ключами шифрования для учетной записи хранения. Ключи, управляемые клиентом, должны храниться в Хранилище ключей Azure.

Кроме того, вы можете предоставить ключ шифрования на уровне индивидуального запроса для некоторых операций хранения Blob. Когда ключ шифрования указан в запросе, этот ключ переопределяет ключ шифрования, который активен на учетной записи хранилища. Для получения дополнительной информации [см.](../blobs/storage-blob-customer-provided-key.md)

Для получения дополнительной информации [Azure Storage encryption for data at rest](storage-service-encryption.md)о ключах шифрования см.

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>Проверьте модель ключа шифрования для учетной записи хранилища

Чтобы определить, использует ли учетная запись хранилища для шифрования управляемые корпорацией Майкрософт или управляемые клиентом ключи, используйте один из следующих подходов.

# <a name="azure-portal"></a>[Портал Azure](#tab/portal)

Чтобы проверить модель шифрования учетной записи хранилища с помощью портала Azure, выполните следующие действия:

1. Войдите в свою учетную запись хранения на портале Azure.
1. Выберите настройки **шифрования** и обратите внимание на настройки.

На следующем изображении отображается учетная запись хранилища, которая зашифрована с помощью ключей, управляемых корпорацией Майкрософт:

![Просмотр учетной записи, зашифрованной с помощью ключей, управляемых корпорацией Майкрософт](media/storage-encryption-key-model-get/microsoft-managed-encryption-key-setting-portal.png)

На следующем изображении показана учетная запись хранения, которая зашифрована с помощью ключей, управляемых клиентом:

![Скриншот, показывающий настройки ключей шифрования на портале Azure](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Чтобы проверить модель шифрования учетной записи хранилища с помощью PowerShell, позвоните в команду [Get-AzStorageAccount,](/powershell/module/az.storage/get-azstorageaccount) а затем проверьте свойство **KeySource** для учетной записи.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

Если значение свойства **KeySource** `Microsoft.Storage`составляет , то учетная запись зашифрована с помощью ключей, управляемых Корпорацией Майкрософт. Если значение свойства **KeySource** `Microsoft.Keyvault`составляет , то учетная запись зашифрована с ключами, управляемыми клиентом.

# <a name="azure-cli"></a>[Лазурный CLI](#tab/cli)

Чтобы проверить модель шифрования учетной записи хранилища с помощью Azure CLI, позвоните в [команду шоу-аккаунта az,](/cli/azure/storage/account#az-storage-account-show) а затем проверьте свойство **keySource** для учетной записи.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

Если значение свойства **keySource** `Microsoft.Storage`составляет , то учетная запись зашифрована с помощью ключей, управляемых Корпорацией Майкрософт. Если значение свойства **keySource** `Microsoft.Keyvault`составляет , то учетная запись зашифрована с ключами, управляемыми клиентом.

---

## <a name="next-steps"></a>Дальнейшие действия

- [Шифрование хранилища Azure для данных в состоянии покоя](storage-service-encryption.md)
- [Используйте управляемые клиентами ключи с Azure Key Vault для управления шифрованием хранилища Azure](encryption-customer-managed-keys.md)