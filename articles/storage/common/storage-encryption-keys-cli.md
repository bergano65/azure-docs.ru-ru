---
title: Используйте Azure CLI для настройки ключей, управляемых клиентами
titleSuffix: Azure Storage
description: Узнайте, как использовать Azure CLI для настройки ключей, управляемых клиентами, с помощью Azure Key Vault для шифрования Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c20fcdf6a725da9d7b053f0ad98efec6aca88f81
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478209"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>Настройка ключей, управляемых клиентами, с помощью Azure Key Vault с помощью Azure CLI

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

В этой статье показано, как настроить Хранилище ключей Azure с помощью ключей, управляемых клиентами, с помощью Azure CLI. Чтобы узнать, как создать хранилище ключей с помощью Azure CLI, [см.](../../key-vault/quick-create-cli.md)

## <a name="assign-an-identity-to-the-storage-account"></a>Присвоить удостоверение личности учетной записи хранилища

Чтобы включить управляемые клиентом ключи для учетной записи хранилища, сначала назначьте учетную запись, назначенную системой, управляемую учетную запись. Это управляемое удостоверение будет использоваться для предоставления разрешения учетной записи хранилища на доступ к хранилищу ключей.

Чтобы назначить управляемое удостоверение с помощью Azure CLI, позвоните [в обновление учетной записи](/cli/azure/storage/account#az-storage-account-update)аз-хранилища. Не забудьте заменить значения заполнителя в скобках с вашими собственными значениями.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Для получения дополнительной информации о настройке назначенных системами управляемых идентификаторов с Azure CLI см. [Нанастройка управляемых идентификаторов для ресурсов Azure в Azure VM с помощью Azure CLI.](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)

## <a name="create-a-new-key-vault"></a>Создание нового хранилища ключей

В хранилище ключей, которое используется для хранения управляемых клиентом ключей для шифрования Azure Storage, должно быть включено два ключевых настройки защиты: **Soft Delete** и Do **Not Purge.** Чтобы создать новое хранилище ключей с помощью PowerShell или Azure CLI с включенными настройками, выполните следующие команды. Не забудьте заменить значения заполнителя в скобках с вашими собственными значениями.

Чтобы создать новое хранилище ключей с помощью Azure CLI, позвоните [в создание az keyvault.](/cli/azure/keyvault#az-keyvault-create) Не забудьте заменить значения заполнителя в скобках с вашими собственными значениями.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

Чтобы узнать, как включить **Soft Delete** и Do **Not Purge** на существующем хранилище ключей с Azure CLI, смотрите разделы под названием **Включение мягкого удаления** и включение защиты от **очистки** в [как использовать мягкое удаление с CLI.](../../key-vault/key-vault-soft-delete-cli.md)

## <a name="configure-the-key-vault-access-policy"></a>Настройка политики доступа к хранилищу ключей

Затем наверсните политику доступа для хранилища ключей таким образом, чтобы учетная запись хранилища получила разрешение на доступ к нему. На этом этапе вы будете использовать управляемую идентификацию, которую вы ранее назначили учетной записи хранилища.

Чтобы установить политику доступа для хранилища ключей, позвоните [в сет-политику az keyvault.](/cli/azure/keyvault#az-keyvault-set-policy) Не забудьте заменить значения заполнителя в скобках с вашими собственными значениями.

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

Затем создайте ключ в хранилище ключей. Чтобы создать ключ, позвоните [в ключ az keyvault.](/cli/azure/keyvault/key#az-keyvault-key-create) Не забудьте заменить значения заполнителя в скобках с вашими собственными значениями.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

Только 2048-разрядные клавиши RSA и RSA-HSM поддерживаются шифрованием Azure Storage. Для получения дополнительной информации о ключах смотрите **ключи Убежища ключей** в [о клавишах, секретах и сертификатах Azure Key Vault.](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)

## <a name="configure-encryption-with-customer-managed-keys"></a>Настройка шифрования с помощью ключей, управляемых клиентом

По умолчанию шифрование Azure Storage использует ключи, управляемые корпорацией Майкрософт. Навлаживайте учетную запись Azure Storage для ключей, управляемых клиентом, и укажите ключ, который можно связать с учетной записью хранилища.

Чтобы обновить настройки шифрования учетной записи хранилища, позвоните [в обновление учетной записи аз-хранилища,](/cli/azure/storage/account#az-storage-account-update)как показано в следующем примере. Включите `--encryption-key-source` параметр `Microsoft.Keyvault` и установите его для включения ключей, управляемых клиентом для учетной записи хранилища. Пример также запрашивает для хранилища ключей URI и последнюю ключевую версию, оба из которых значения необходимы для ассоциировать ключ с учетной записью хранилища. Не забудьте заменить значения заполнителя в скобках с вашими собственными значениями.

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

## <a name="update-the-key-version"></a>Обновление ключевой версии

При создании новой версии ключа необходимо обновить учетную запись хранилища, чтобы использовать новую версию. Во-первых, запрос для ключа хранилище URI, позвонив [аз keyvault шоу](/cli/azure/keyvault#az-keyvault-show), и для ключевой версии, позвонив [аз Keyvault список-версии.](/cli/azure/keyvault/key#az-keyvault-key-list-versions) Затем позвоните [в обновление учетной записи аз для](/cli/azure/storage/account#az-storage-account-update) обновления настроек шифрования учетной записи хранилища, чтобы использовать новую версию ключа, как показано в предыдущем разделе.

## <a name="use-a-different-key"></a>Используйте другой ключ

Чтобы изменить ключ, используемый для шифрования Хранилища Az, позвоните [в обновление учетной записи хранилища az,](/cli/azure/storage/account#az-storage-account-update) как показано в [шифровании Настройки с ключами, управляемыми клиентом,](#configure-encryption-with-customer-managed-keys) и предоставьте новое ключевое имя и версию. Если новый ключ находится в другом хранилище ключей, также обновите хранилище ключей URI.

## <a name="revoke-customer-managed-keys"></a>Отозвать ключи, управляемые клиентами

Если вы считаете, что ключ может быть скомпрометирован, вы можете отозвать ключи, управляемые клиентом, удалив политику доступа к хранилищу ключей. Чтобы отозвать ключ, управляемый клиентом, позвоните в команду [удаления клавиш az keyvault,](/cli/azure/keyvault#az-keyvault-delete-policy) как показано в следующем примере. Не забудьте заменить значения заполнителя в скобках на собственные значения и использовать переменные, определенные в предыдущих примерах.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

## <a name="disable-customer-managed-keys"></a>Отключить ключи, управляемые клиентом

При отключении ключей, управляемых клиентом, ваша учетная запись хранилища снова шифруется ключами, управляемыми корпорацией Майкрософт. Чтобы отключить клавиши, управляемые клиентом, позвоните `--encryption-key-source parameter` `Microsoft.Storage`в [обновление учетной записи az](/cli/azure/storage/account#az-storage-account-update) и установите, как показано в следующем примере. Не забудьте заменить значения заполнителя в скобках на собственные значения и использовать переменные, определенные в предыдущих примерах.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>Следующие шаги

- [Шифрование хранилища Azure для данных в состоянии покоя](storage-service-encryption.md) 
- [Что такое Убежище ключей Azure?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
