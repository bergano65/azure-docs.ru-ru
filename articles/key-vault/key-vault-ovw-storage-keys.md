---
ms.assetid: ''
title: Учетная запись хранения, управляемая с помощью Azure Key Vault, — CLI
description: Ключи учетной записи хранения обеспечивают простую интеграцию между Azure Key Vault и доступом по ключу к учетной записи хранения Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 10/03/2018
ms.openlocfilehash: e110a24af6a0d49ea949db6bc30ab344c81d0f48
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52619288"
---
# <a name="azure-key-vault-managed-storage-account---cli"></a>Учетная запись хранения, управляемая с помощью Azure Key Vault, — CLI

> [!NOTE]
> [Служба хранилища Azure теперь поддерживает авторизацию AAD](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Мы рекомендуем использовать Azure Active Directory для аутентификации и авторизации в хранилище, так как пользователям не придется беспокоиться о смене ключей учетной записи хранения.

- Служба хранилища Azure Key Vault управляет ключами учетной записи хранения Azure (ASA).
    - На внутреннем уровне Azure Key Vault может выводить список (синхронизировать) ключи с помощью учетной записи хранения Azure.    
    - Azure Key Vault периодически повторно создает (сменяет) ключи.
    - Значения ключей никогда не возвращаются в ответе вызывающему объекту.
    - Azure Key Vault управляет ключами как учетных записей хранения, так и классических учетных записей хранения.

<a name="prerequisites"></a>Предварительные требования
--------------
1. Установите [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).   
2. [Создайте учетную запись хранения](https://azure.microsoft.com/services/storage/).
    - Следуйте [инструкциям](https://docs.microsoft.com/azure/storage/) по созданию учетной записи хранения.  
    - **Рекомендации по именованию**. Имя учетной записи хранения должно содержать от 3 до 24 знаков и состоять только из цифр и строчных букв.        
      
<a name="step-by-step-instructions-on-how-to-use-key-vault-to-manage-storage-account-keys"></a>Пошаговые инструкции по использованию Key Vault для управления ключами учетной записи хранения
--------------------------------------------------------------------------------
В приведенных ниже инструкциях мы назначаем Key Vault в качестве службы, чтобы у учетной записи хранения были разрешения оператора.

> [!NOTE]
> Обратите внимание, что после настройки ключей учетной записи хранения, которыми управляет Azure Key Vault, их можно изменить **только** через Key Vault. Под управляемыми ключами учетной записи подразумевается, что сменой ключей учетной записи хранения будет управлять Key Vault.

1. После создания учетной записи хранения выполните следующую команду, чтобы получить идентификатор ресурса учетной записи хранения, которой требуется управлять.

    ```
    az storage account show -n storageaccountname (Copy ID field out of the result of this command)
    ```
    
2. Получите идентификатор приложения субъекта-службы Azure Key Vault. 

    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
    
3. Присвойте идентификатору Azure Key Vault роль оператора учетной записи хранения.

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ApplicationIdOfKeyVault> --scope <IdOfStorageAccount>
    ```
    
4. Создайте управляемую учетную запись хранения в Key Vault.     <br /><br />
   Ниже мы указываем для повторного создания период в 90 дней. Через 90 дней хранилище ключей повторно создаст ключ key1 и установит его в качестве активного вместо key2.
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key2 --auto-regenerate-key --regeneration-period P90D --resource-id <Resource-id-of-storage-account>
    ```
    Если пользователь не создал учетную запись хранения и не имеет разрешений для доступа к ней, выполнив описанные ниже действия, можно установить разрешения для учетной записи, чтобы обеспечить возможность управления всеми разрешениями хранилища в Key Vault.
 > [!NOTE] 
    Если пользователь не имеет разрешений для учетной записи хранения, мы сначала получим идентификатор объекта пользователя.

    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```
### <a name="relavant-azure-cli-cmdlets"></a>Соответствующие командлеты Azure CLI
- [Командлеты Azure CLI для службы хранилища](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

### <a name="relevant-powershell-cmdlets"></a>Соответствующие командлеты PowerShell

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="see-also"></a>См. также

- [Сведения о ключах, секретах и сертификатах](https://docs.microsoft.com/rest/api/keyvault/)
- [Официальный блог команды разработчиков Azure Key Vault](https://blogs.technet.microsoft.com/kv/)
