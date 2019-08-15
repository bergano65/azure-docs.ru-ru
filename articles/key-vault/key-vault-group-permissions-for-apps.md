---
title: Предоставление разрешения на доступ к хранилищу ключей Azure нескольким приложениям — Azure Key Vault | Документация Майкрософт
description: Узнайте, как нескольким приложениям предоставить разрешение на доступ к хранилищу ключей
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 07ee544057ffeb0a5859cc771b124523ec79c9c0
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976398"
---
# <a name="grant-several-applications-access-to-a-key-vault"></a>Предоставление нескольким приложениям доступа к хранилищу ключей

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Для предоставления нескольким приложениям доступа к хранилищу ключей можно использовать политики управления доступом. Политика управления доступом может поддерживать до 1024 приложений и настроена следующим образом:

1. Создайте группу безопасности Azure Active Directory. 
2. Добавьте все приложения, связанные с субъекты-службами в группу безопасности.
3. Предоставьте доступ к группе безопасности к вашему хранилищу ключей.

## <a name="prerequisites"></a>Предварительные требования

Выполните следующее:
* [Установите Azure PowerShell](/powershell/azure/overview).
* [Установите модуль PowerShell для Azure Active Directory версии 2](https://www.powershellgallery.com/packages/AzureAD).
* Получите разрешения на создание и изменение групп в клиенте Azure Active Directory. Если у вас нет разрешений, обратитесь к администратору Azure Active Directory. Ознакомиться со сведениями о разрешениях политики доступа Key Vault можно в статье [Сведения о ключах, секретах и сертификатах](about-keys-secrets-and-certificates.md).

## <a name="granting-key-vault-access-to-applications"></a>Предоставление Key Vault доступа к приложениям

В PowerShell выполните следующие команды:

```powershell
# Connect to Azure AD 
Connect-AzureAD 
 
# Create Azure Active Directory Security Group 
$aadGroup = New-AzureADGroup -Description "Contoso App Group" -DisplayName "ContosoAppGroup" -MailEnabled 0 -MailNickName none -SecurityEnabled 1 
 
# Find and add your applications (ServicePrincipal ObjectID) as members to this group 
$spn = Get-AzureADServicePrincipal –SearchString "ContosoApp1" 
Add-AzureADGroupMember –ObjectId $aadGroup.ObjectId -RefObjectId $spn.ObjectId 
 
# You can add several members to this group, in this fashion. 
 
# Set the Key Vault ACLs 
Set-AzKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId `
-PermissionsToKeys decrypt,encrypt,unwrapKey,wrapKey,verify,sign,get,list,update,create,import,delete,backup,restore,recover,purge `
–PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge `
–PermissionsToCertificates get,list,delete,create,import,update,managecontacts,getissuers,listissuers,setissuers,deleteissuers,manageissuers,recover,purge,backup,restore `
-PermissionsToStorage get,list,delete,set,update,regeneratekey,getsas,listsas,deletesas,setsas,recover,backup,restore,purge 
 
# Of course you can adjust the permissions as required 
```

Если группе приложений необходимо предоставить другой набор разрешений, создайте для таких приложений отдельную группу безопасности Azure Active Directory.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения см. в статье [Защита хранилища ключей](key-vault-secure-your-key-vault.md).
