---
title: Использование Azure CLI для настройки ключей, управляемых клиентом
titleSuffix: Azure Storage
description: Узнайте, как использовать Azure CLI для настройки ключей, управляемых клиентом, с помощью Azure Key Vault для шифрования службы хранилища Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/24/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 25ee5d389bc70d82730c7056c752de393a6bf4c5
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799151"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>Настройка ключей, управляемых клиентом, с помощью Azure Key Vault Azure CLI

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

В этой статье показано, как настроить Azure Key Vault с управляемыми клиентом ключами с помощью Azure CLI. Сведения о создании хранилища ключей с помощью Azure CLI см. в разделе [Краткое руководство. Установка и извлечение секрета из Azure Key Vault с помощью Azure CLI](../../key-vault/secrets/quick-create-cli.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Назначение удостоверения учетной записи хранения

Чтобы включить управляемые клиентом ключи для учетной записи хранения, сначала назначьте назначенное системой управляемое удостоверение учетной записи хранения. Вы будете использовать это управляемое удостоверение, чтобы предоставить учетной записи хранения разрешения на доступ к хранилищу ключей.

Чтобы назначить управляемое удостоверение с помощью Azure CLI, вызовите команду [AZ Storage учетная запись Update](/cli/azure/storage/account#az-storage-account-update). Не забудьте заменить значения заполнителей в квадратных скобках собственными значениями.

```azurecli-interactive
az login
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

Чтобы узнать, как включить **обратимое удаление** и **не выполнять очистку** в существующем хранилище ключей с Azure CLI, ознакомьтесь с разделом **Включение обратимого удаления** и **Включение защиты от вирусов** в разделе [Использование обратимого удаления с помощью интерфейса командной строки](../../key-vault/general/soft-delete-cli.md).

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
    --key-permissions get unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>Создание ключа

Затем создайте ключ в хранилище ключей. Чтобы создать ключ, вызовите команду [AZ keyvault Key Create](/cli/azure/keyvault/key#az-keyvault-key-create). Не забудьте заменить значения заполнителей в квадратных скобках собственными значениями.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

Шифрование службы хранилища Azure поддерживает ключи RSA и RSA-HSM размером 2048, 3072 и 4096. Дополнительные сведения о ключах см. в разделе **Key Vault Keys** раздела [о Azure Key Vault ключах, секретах и сертификатах](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="configure-encryption-with-customer-managed-keys"></a>Настройка шифрования с помощью управляемых клиентом ключей

По умолчанию шифрование службы хранилища Azure использует ключи, управляемые корпорацией Майкрософт. На этом шаге настройте учетную запись хранения Azure для использования ключей, управляемых клиентом, с Azure Key Vault, а затем укажите ключ, связываемый с учетной записью хранения.

При настройке шифрования с помощью ключей, управляемых клиентом, можно выбрать автоматическое обновление ключа, используемого для шифрования при изменении версии ключа в связанном хранилище ключей. Кроме того, можно явно указать версию ключа, которая будет использоваться для шифрования до тех пор, пока не будет обновлена версия ключа вручную.

> [!NOTE]
> Чтобы повернуть ключ, создайте новую версию ключа в Azure Key Vault. Служба хранилища Azure не обрабатывает вращение ключа в Azure Key Vault, поэтому вам потребуется вручную сменить ключ или создать функцию, чтобы ее можно было поворачивать по расписанию.

### <a name="configure-encryption-to-automatically-update-the-key-version"></a>Настройка шифрования для автоматического обновления версии ключа

Чтобы настроить шифрование с помощью управляемых клиентом ключей для автоматического обновления версии ключа, установите [Azure CLI версии 2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) или более поздней. Дополнительные сведения см. в статье [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Чтобы автоматически обновить ключ для управляемого клиентом ключа, не указывайте версию ключа при настройке шифрования с помощью управляемых клиентом ключей для учетной записи хранения. Чтобы обновить параметры шифрования учетной записи хранения, вызовите команду [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) , как показано в следующем примере. Включите `--encryption-key-source` параметр и установите его в значение, `Microsoft.Keyvault` чтобы включить управляемые клиентом ключи для учетной записи. Не забудьте заменить значения заполнителей в квадратных скобках собственными значениями.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>Настройка шифрования для обновления основных версий вручную

Чтобы явно указать версию ключа, используемую для шифрования, укажите версию ключа при настройке шифрования с помощью управляемых клиентом ключей для учетной записи хранения. Чтобы обновить параметры шифрования учетной записи хранения, вызовите команду [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) , как показано в следующем примере. Включите `--encryption-key-source` параметр и установите его в значение, `Microsoft.Keyvault` чтобы включить управляемые клиентом ключи для учетной записи. Не забудьте заменить значения заполнителей в квадратных скобках собственными значениями.

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

При ручном обновлении версии ключа необходимо обновить параметры шифрования учетной записи хранения, чтобы использовать новую версию. Сначала запросите универсальный код ресурса (URI) хранилища ключей, вызвав команду [AZ keyvault показывать](/cli/azure/keyvault#az-keyvault-show), а для версии key [— вызов AZ keyvault Key List-Versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Затем вызовите команду [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) , чтобы обновить параметры шифрования учетной записи хранения для использования новой версии ключа, как показано в предыдущем примере.

## <a name="use-a-different-key"></a>Использовать другой ключ

Чтобы изменить ключ, используемый для шифрования службы хранилища Azure, вызовите команду [AZ Storage учетная запись Update](/cli/azure/storage/account#az-storage-account-update) , как показано в разделе [Настройка шифрования с помощью управляемых клиентом ключей](#configure-encryption-with-customer-managed-keys) и предоставление нового имени и версии ключа. Если новый ключ находится в другом хранилище ключей, также обновите URI хранилища ключей.

## <a name="revoke-customer-managed-keys"></a>Отозвать ключи, управляемые клиентом

Вы можете отозвать ключи, управляемые клиентом, удалив политику доступа к хранилищу ключей. Чтобы отозвать ключ, управляемый клиентом, вызовите команду [AZ keyvault Delete-Policy](/cli/azure/keyvault#az-keyvault-delete-policy) , как показано в следующем примере. Не забудьте заменить значения заполнителей в квадратных скобках собственными значениями и использовать переменные, определенные в предыдущих примерах.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

## <a name="disable-customer-managed-keys"></a>Отключение ключей, управляемых клиентом

При отключении управляемых пользователем ключей учетная запись хранения снова шифруется с помощью ключей, управляемых корпорацией Майкрософт. Чтобы отключить управляемые клиентом ключи, вызовите команду [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) и задайте `--encryption-key-source parameter` для значение `Microsoft.Storage` , как показано в следующем примере. Не забудьте заменить значения заполнителей в квадратных скобках собственными значениями и использовать переменные, определенные в предыдущих примерах.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>Дальнейшие действия

- [Шифрование службы хранилища Azure для неактивных данных](storage-service-encryption.md) 
- [Об Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
