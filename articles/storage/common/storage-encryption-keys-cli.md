---
title: Настройка ключей, управляемых клиентом, с Azure Key Vault с помощью Azure CLI в службе хранилища Azure
description: Узнайте, как использовать Azure CLI для настройки ключей, управляемых клиентом, с помощью Azure Key Vault для шифрования службы хранилища Azure. Ключи, управляемые клиентом, позволяют создавать, поворачивать, отключать и отзывать элементы управления доступом.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: fd3b7767bad104f4074b2460ecba3fe89d5a23e1
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806632"
---
# <a name="configure-customer-managed-keys-for-azure-storage-by-using-azure-cli"></a>Настройка ключей, управляемых клиентом, для службы хранилища Azure с помощью Azure CLI

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

В этой статье показано, как настроить Azure Key Vault с управляемыми клиентом ключами с помощью Azure CLI. Сведения о создании хранилища ключей с помощью Azure CLI см. в разделе [Краткое руководство. Установка и извлечение секрета из Azure Key Vault с помощью Azure CLI](../../key-vault/quick-create-cli.md).

> [!IMPORTANT]
> Для использования управляемых клиентом ключей с шифрованием службы хранилища Azure необходимо, чтобы в хранилище ключей были установлены два свойства: **обратимое удаление** и **не очищать**. По умолчанию эти свойства отключены. Чтобы включить эти свойства, используйте либо PowerShell, либо Azure CLI.
> Поддерживаются только ключи RSA и размер ключа 2048.

## <a name="assign-an-identity-to-the-storage-account"></a>Назначение удостоверения учетной записи хранения

Чтобы включить управляемые клиентом ключи для учетной записи хранения, сначала назначьте назначенное системой управляемое удостоверение учетной записи хранения. Вы будете использовать это управляемое удостоверение, чтобы предоставить учетной записи хранения разрешения на доступ к хранилищу ключей.

Чтобы назначить управляемое удостоверение с помощью Azure CLI, вызовите команду [AZ Storage учетная запись Update](/cli/azure/storage/account#az-storage-account-update). Не забудьте заменить значения заполнителей в квадратных скобках собственными значениями.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Дополнительные сведения о настройке управляемых удостоверений, назначенных системой, с помощью Azure CLI см. [в статье Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Создание нового хранилища ключей

Хранилище ключей, используемое для хранения управляемых клиентом ключей для шифрования службы хранилища Azure, должно включать два параметра защиты ключей: **обратимое удаление** и **не очищать**. Чтобы создать новое хранилище ключей с помощью PowerShell или Azure CLI с включенными параметрами, выполните следующие команды. Не забудьте заменить значения заполнителей в квадратных скобках собственными значениями. 

Чтобы создать новое хранилище ключей с помощью Azure CLI, вызовите команду [AZ keyvault Create](/cli/azure/keyvault#az-keyvault-create). Не забудьте заменить значения заполнителей в квадратных скобках собственными значениями.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>Настройка политики доступа к хранилищу ключей

Затем настройте политику доступа для хранилища ключей, чтобы у учетной записи хранения были разрешения на доступ к ней. На этом шаге вы будете использовать управляемое удостоверение, назначенное ранее учетной записи хранения.

Чтобы задать политику доступа для хранилища ключей, вызовите команду [AZ keyvault Set-Policy](/cli/azure/keyvault#az-keyvault-set-policy). Не забудьте заменить значения заполнителей в квадратных скобках собственными значениями.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
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

Затем создайте ключ в хранилище ключей. Чтобы создать ключ, вызовите команду [AZ keyvault Key Create](/cli/azure/keyvault/key#az-keyvault-key-create). Не забудьте заменить значения заполнителей в квадратных скобках собственными значениями.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Настройка шифрования с помощью управляемых клиентом ключей

По умолчанию шифрование службы хранилища Azure использует ключи, управляемые корпорацией Майкрософт. Настройте учетную запись хранения Azure для ключей, управляемых клиентом, и укажите ключ, связываемый с учетной записью хранения.

Чтобы обновить параметры шифрования учетной записи хранения, вызовите команду [AZ Storage учетная запись Update](/cli/azure/storage/account#az-storage-account-update). В этом примере также запрашиваются универсальный код ресурса (URI) хранилища ключей и последняя версия ключа. Эти значения необходимы для связывания ключа с учетной записью хранения. Не забудьте заменить значения заполнителей в квадратных скобках собственными значениями.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
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

При создании новой версии ключа необходимо обновить учетную запись хранения, чтобы она использовала новую версию. Сначала запросите универсальный код ресурса (URI) хранилища ключей, вызвав команду [AZ keyvault показывать](/cli/azure/keyvault#az-keyvault-show), а для версии key [— вызов AZ keyvault Key List-Versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Затем вызовите команду [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) , чтобы обновить параметры шифрования учетной записи хранения для использования новой версии ключа, как показано в предыдущем разделе.

## <a name="next-steps"></a>Дальнейшие действия

- [Шифрование неактивных данных в службе хранилища Azure](storage-service-encryption.md) 
- [Что такое Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
