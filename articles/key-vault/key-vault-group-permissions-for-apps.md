---
title: Предоставление приложениям разрешений на доступ к хранилищу ключей Azure — Azure Key Vault | Документация Майкрософт
description: Узнайте, как нескольким приложениям предоставить разрешение на доступ к хранилищу ключей
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/27/2019
ms.author: mbaldwin
ms.openlocfilehash: 10445aba9c52c5367a8ea03729462d14e2d51085
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707182"
---
# <a name="provide-key-vault-authentication-with-an-access-control-policy"></a>Обеспечение проверки подлинности Azure Key Vault с помощью политики управления доступом

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Самый простой способ проверить подлинность облачного приложения в Key Vault — с помощью управляемых удостоверений. Дополнительные сведения см. в разделе [Использование управляемого удостоверения службы приложений для доступа к Azure Key Vault](managed-identity.md).  Если вы создаете локальное приложение, выполняете локальную разработку или не можете использовать управляемое удостоверение, вы можете зарегистрировать субъект-службу вручную и предоставить доступ к хранилищу ключей с помощью политики управления доступом.  

Хранилище ключей поддерживает до 1024 политик доступа, при этом каждая запись предоставляет отдельный набор разрешений для "субъекта":   Например, вот как консольное приложение в разделе [Краткое руководство по клиентской библиотеке Azure Key Vault для .NET](quick-create-net.md) обращается к хранилищу ключей.

Дополнительные сведения об управлении доступом в Key Vault см. в разделе [Azure Key Vault Security: Identity and access management](overview-security.md#identity-and-access-management) (Безопасность Azure Key Vault: управление удостоверениями и доступом). Дополнительные сведения об управлении доступом с использованием [ключей, секретов и сертификатов](about-keys-secrets-and-certificates.md) см.в следующих статьях. 

- [Управление доступом к использованием ключей](about-keys-secrets-and-certificates.md#key-access-control)
- [Управление доступом к использованием секретов](about-keys-secrets-and-certificates.md#secret-access-control)
- [Управление доступом к использованием сертификатов](about-keys-secrets-and-certificates.md#certificate-access-control)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Предварительные требования

- Хранилище ключей. Вы можете использовать существующее хранилище ключей или создать новое, выполнив действия, описанные в одном из этих кратких руководств:
   - [Создание хранилища ключей с помощью интерфейса командной строки Azure](quick-create-cli.md)
   - [Создание хранилища ключей с помощью Azure PowerShell](quick-create-powershell.md)
   - [Создание хранилища ключей с помощью портала Azure](quick-create-portal.md)
- [Интерфейс командной строки Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) или [Azure PowerShell](/powershell/azure/overview). Кроме того, можно использовать [портал Azure](https://portal.azure.com).

## <a name="grant-access-to-your-key-vault"></a>Предоставление доступа к хранилищу ключей

Каждая политика доступа к хранилищу ключей предоставляет участнику отдельный набор разрешений.

- **Приложение** Если это облачное приложение, следует использовать [управляемое удостоверение для получения доступа к Azure Key Vault](managed-identity.md), если это возможно.
- **Группа Azure AD** Хотя хранилище ключей поддерживает только 1024 политики доступа, можно добавить несколько приложений и пользователей в одну группу Azure AD, а затем добавить эту группу в качестве единой записи в политику управления доступом.
- **Пользователь** Предоставление пользователям прямого доступа к хранилищу ключей **не рекомендуется**. В идеале пользователей следует добавить в группу Azure AD, которая, в свою очередь, получит доступ к хранилищу ключей. См. раздел [Azure Key Vault security: Identity and access management](overview-security.md#identity-and-access-management) (Безопасность Azure Key Vault: управление удостоверениями и доступом).


### <a name="get-the-objectid"></a>Получение идентификатора ObjectID

Чтобы предоставить приложению, группе Azure AD или пользователю доступ к хранилищу ключей, необходимо сначала получить соответствующий идентификатор ObjectId.

#### <a name="applications"></a>ПРИЛОЖЕНИЯ

ObjectId для приложений соответствует связанному с ними субъекту-службе. Дополнительные сведения о субъектах-службах см. в статье [Объекты субъектов-служб и приложений в Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Получить objectId для приложения можно двумя способами.  Первый — зарегистрировать приложение с помощью Azure Active Directory. Для этого выполните действия, описанные в кратком руководстве [Регистрация приложения с помощью платформы Microsoft Identity](../active-directory/develop/quickstart-register-app.md). После завершения регистрации идентификатор objectID будет указан как "Application (Client) ID" (Идентификатор приложения (клиента)).

Второй — создание субъекта-службы в окне терминала. В интерфейсе командной строки Azure введите команду [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac).

```azurecli-interactive
az ad sp create-for-rbac -n "http://mySP"
```

Идентификатор objectId будет указан в выходных данных как `clientID`.

В Azure PowerShell выполните командлет [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal?view=azps-2.7.0).


```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName mySP
```

Идентификатор objectId будет указан в выходных данных как `Id` (а не `ApplicationId`).

#### <a name="azure-ad-groups"></a>Группы Azure AD

В группу Azure AD можно добавить несколько приложений и пользователей, а затем предоставить этой группе доступ к хранилищу ключей.  Дополнительные сведения см. в разделе[Создание участников и добавление их в группу Azure AD](#creating-and-adding-members-to-an-azure-ad-group) ниже.

Чтобы узнать идентификатор objectId группы Azure AD в интерфейсе командной строки Azure, введите команду [az ad group list](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list). Из-за большого количества групп, которые могут быть в организации, необходимо также предоставить строку поиска для параметра `--display-name`.

```azurecli-interactive
az ad group list --display-name <search-string>
```
ObjectId будет возвращен в формате JSON:

```json
    "objectId": "48b21bfb-74d6-48d2-868f-ff9eeaf38a64",
    "objectType": "Group",
    "odata.type": "Microsoft.DirectoryServices.Group",
```

Чтобы узнать идентификатор objectId группы Azure AD в Azure PowerShell, выполните командлет [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0). Из-за большого количества групп, которые могут быть в организации, может также понадобиться предоставить строку поиска для параметра `-SearchString`.

```azurepowershell-interactive
Get-AzADGroup -SearchString <search-string>
```

В выходных данных objectId указывается как `Id`:

```console
...
Id                    : 1cef38c4-388c-45a9-b5ae-3d88375e166a
...
```

#### <a name="users"></a>Пользователи

Вы также можете добавить отдельного пользователя в политику управления доступом к хранилищу ключей. **Мы не рекомендуем использовать этот вариант.** Вместо этого мы рекомендуем добавить пользователей в группу Azure AD и затем добавить уже эту группу в политики.

Если вы хотите найти пользователя с помощью интерфейса командной строки Azure, используйте команду [az ad user show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show), передав адрес электронной почты пользователей в параметр `--id`.


```azurecli-interactive
az ad user show --id <email-address-of-user>
```

Идентификатор objectId пользователя будет возвращен в выходных данных:

```console
  ...
  "objectId": "f76a2a6f-3b6d-4735-9abd-14dccbf70fd9",
  "objectType": "User",
  ...
```

Чтобы найти пользователя с помощью Azure PowerShell, используйте командлет [Get-AzADUser](/powershell/module/az.resources/get-azaduser?view=azps-2.7.0), передав адрес электронной почты пользователя в параметр `-UserPrincipalName`.

```azurepowershell-interactive
 Get-AzAdUser -UserPrincipalName <email-address-of-user>
```

Идентификатор objectId пользователя будет возвращен в выходных данных как `Id`.

```console
...
Id                : f76a2a6f-3b6d-4735-9abd-14dccbf70fd9
Type              :
```

### <a name="give-the-principal-access-to-your-key-vault"></a>Предоставление субъекту-службе доступа к хранилищу ключей

Теперь, когда у вас есть objectID субъекта, вы можете создать политику доступа для хранилища ключей, которая предоставляет ему права на получение, перечисление, установку и удаление для ключей и секретов, а также любые дополнительные разрешения, которые могут понадобиться.

В интерфейсе командной строки Azure это делается путем передачи objectId в команду [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy).

```azurecli-interactive
az keyvault set-policy -n <your-unique-keyvault-name> --spn <ApplicationID-of-your-service-principal> --secret-permissions get list set delete --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

В Azure PowerShell это делается путем передачи objectId в командлет [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0). 

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy –VaultName <your-key-vault-name> -PermissionsToKeys create,decrypt,delete,encrypt,get,list,unwrapKey,wrapKey -PermissionsToSecrets get,list,set,delete -ApplicationId <Id>

```

## <a name="creating-and-adding-members-to-an-azure-ad-group"></a>Создание участников и добавление их в группу Azure AD

Вы можете также создать группу Azure AD, добавить в нее приложения и пользователей, а затем предоставить этой группе доступ к хранилищу ключей.  Таким образом можно добавить несколько приложений в хранилище ключей в качестве одной записи политики доступа и избежать необходимости предоставлять пользователям прямой доступ к вашему хранилищу ключей (что не рекомендуется). Дополнительные сведения см. в статье [Управление доступом к приложениям и ресурсам с помощью групп Azure Active Directory](../active-directory/fundamentals/active-directory-manage-groups.md).

### <a name="additional-prerequisites"></a>Дополнительные требования

Помимо требований, [изложенных выше](#prerequisites), вам понадобятся разрешения на создание и изменение групп в клиенте Azure Active Directory. Если у вас нет разрешений, обратитесь к администратору Azure Active Directory.

Если вы планируете использовать PowerShell, потребуется также модуль [Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.2.50)

### <a name="create-an-azure-active-directory-group"></a>Создание группы Azure Active Directory

Создание новой группы Azure Active Directory с помощью команды Azure CLI [az ad group create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) или командлета Azure PowerShell [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup?view=azureadps-2.0).


```azurecli-interactive
az ad group create --display-name <your-group-display-name> --mail-nickname <your-group-mail-nickname>
```

```powershell
New-AzADGroup -DisplayName <your-group-display-name> -MailNickName <your-group-mail-nickname>
```

В любом случае обратите внимание на GroupId только что созданных групп, так как они понадобятся при выполнении описанных ниже действий.

### <a name="find-the-objectids-of-your-applications-and-users"></a>Поиск objectId для приложений и пользователей

Идентификаторы objectId приложений можно найти с помощью Azure CLI, используя команду [az ad sp list](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) с параметром `--show-mine`.

```azurecli-interactive
az ad sp list --show-mine
```

Идентификаторы objectId приложений, можно найти используя командлет Azure PowerShell [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal?view=azps-2.7.0), передав строку поиска в параметр `-SearchString`.

```azurepowershell-interactive
Get-AzADServicePrincipal -SearchString <search-string>
```

Чтобы найти идентификаторы пользователей, выполните действия, описанные в разделе [Пользователи](#users) выше.

### <a name="add-your-applications-and-users-to-the-group"></a>Добавление приложений и пользователей в группу

Теперь добавьте идентификаторы objectId во вновь созданную группу Azure AD.

В Azure CLI используйте команду [az ad group member add](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add), передав objectId в параметр `--member-id`.


```azurecli-interactive
az ad group member add -g <groupId> --member-id <objectId>
```

В Azure PowerShell используйте командлет [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember?view=azps-2.7.0), передав objectId в параметр `-MemberObjectId`.

```azurepowershell-interactive
Add-AzADGroupMember -TargetGroupObjectId <groupId> -MemberObjectId <objectId> 
```

### <a name="give-the-ad-group-access-to-your-key-vault"></a>Предоставление группе AD доступа к хранилищу ключей

Наконец, предоставьте разрешения группе AD для хранилища ключей с помощью команды Azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) или командлета Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0). Примеры см. в разделе [Предоставление приложению, группе Azure AD или пользователю доступа к хранилищу ключей](#give-the-principal-access-to-your-key-vault) выше.

Приложению также требуется по крайней мере одна роль Системы управления идентификацией и доступом (IAM), назначенная хранилищу ключей. В противном случае оно не сможет войти в систему и завершится ошибкой о недостаточных правах доступа к подписке.

## <a name="next-steps"></a>Дополнительная информация

- [Azure Key Vault security: Identity and access management](overview-security.md#identity-and-access-management) (Azure Key Vault security: управление удостоверениями и доступом)
- [Проверка подлинности в Key Vault с помощью управляемого удостоверения службы приложений](managed-identity.md)
- [Сведения о ключах, секретах и сертификатах](about-keys-secrets-and-certificates.md)
- [Обеспечение безопасности хранилища ключей](key-vault-secure-your-key-vault.md).
- [Руководство разработчика Azure Key Vault](key-vault-developers-guide.md)
- [Рекомендации по Azure Key Vault](key-vault-best-practices.md)
