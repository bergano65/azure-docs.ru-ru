---
title: Настройка управляемых пользователем ключей для шифрования службы хранилища Azure с помощью Azure CLI
description: Узнайте, как с помощью Azure CLI для настройки управляемых пользователем ключей для шифрования службы хранилища Azure. Управляемые клиентом ключи позволяют создавать, поворот, отключить и отозвать элементы управления доступом.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ca2cfc9369fd6fb001b2a2dc401c33e5ddfd38c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65142913"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-azure-cli"></a>Настройка управляемых пользователем ключей для шифрования службы хранилища Azure с помощью Azure CLI

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

В этой статье показано, как настроить хранилище ключей с помощью управляемых клиентом ключей с помощью Azure CLI.

## <a name="assign-an-identity-to-the-storage-account"></a>Назначение удостоверения учетной записи хранения

Чтобы включить управляемые клиентом ключи для вашей учетной записи хранения, сначала назначьте назначенный системой управляемого удостоверения учетной записи хранения. Вы используете это управляемое удостоверение для предоставления разрешений учетной записи хранения для доступа к хранилищу ключей.

Чтобы назначить управляемого удостоверения с помощью Azure CLI, вызовите [обновление учетной записи хранения az](/cli/azure/storage/account#az-storage-account-update). Не забудьте заменить значения заполнителей в квадратные скобки, своими собственными значениями.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Дополнительные сведения о настройке управляемого удостоверения системы с помощью Azure CLI см. в разделе [Настройка управляемых удостоверений для ресурсов Azure на виртуальной Машине Azure с помощью Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Создание нового хранилища ключей

Хранилище ключей, которое используется для хранения управляемых пользователем ключей для шифрования службы хранилища Azure должна иметь два параметра ключа защиты включен, **обратимое удаление** и **не следует очистить**. Чтобы создать новое хранилище ключей с помощью PowerShell или интерфейса командной строки Azure эти параметры включены, выполните следующие команды. Не забудьте заменить значения заполнителей в квадратные скобки, своими собственными значениями. 

Чтобы создать новое хранилище ключей с помощью Azure CLI, вызовите [создать az keyvault](/cli/azure/keyvault#az-keyvault-create). Не забудьте заменить значения заполнителей в квадратные скобки, своими собственными значениями.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>Настройка политики доступа хранилища ключей

Настройте политики доступа для хранилища ключей, чтобы учетная запись хранения имеет разрешения на доступ к нему. На этом шаге вы используете управляемое удостоверение, которое ранее было назначено учетной записи хранения.

Чтобы задать политику доступа для хранилища ключей, вызовите [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy). Не забудьте заменить значения заполнителей в квадратные скобки, своими собственными значениями.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    -name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get recover unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>Создание ключа

Создайте ключ в хранилище ключей. Чтобы создать ключ, вызовите [создать az keyvault key](/cli/azure/keyvault/key#az-keyvault-key-create). Не забудьте заменить значения заполнителей в квадратные скобки, своими собственными значениями.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Настройка шифрования с помощью управляемых клиентом ключей

По умолчанию шифрование службы хранилища Azure использует ключи, управляемые Майкрософт. Настройка учетной записи хранения Azure для управляемых клиентом ключей и укажите ключ для связи с учетной записью хранения.

Чтобы обновить параметры шифрования для учетной записи хранения, вызовите [обновление учетной записи хранения az](/cli/azure/storage/account#az-storage-account-update). В этом примере также отправляет запрос URI хранилища ключей и версии ключа, оба значения, которые необходимо связать ключ с помощью учетной записи хранения. Не забудьте заменить значения заполнителей в квадратные скобки, своими собственными значениями.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update 
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

## <a name="update-the-key-version"></a>Обновление версии ключа

При создании новой версии ключа, вам потребуется обновить учетную запись хранения для использования новой версии. Во-первых, запрос для хранилища ключей URI путем вызова [az keyvault show](/cli/azure/keyvault#az-keyvault-show)и для версии ключа, вызвав [az keyvault версии ключей в списке-](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Затем вызовите [обновление учетной записи хранения az](/cli/azure/storage/account#az-storage-account-update) для обновления параметров шифрования учетной записи хранения для использования новой версии ключа, как показано в предыдущем разделе.

## <a name="next-steps"></a>Дальнейшие действия

- [Шифрование хранилища Azure для неактивных данных](storage-service-encryption.md) 
- [Что такое хранилище ключей Azure](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
