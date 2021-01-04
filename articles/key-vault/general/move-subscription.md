---
title: Azure Key Vault перемещение хранилища в другую подписку | Документация Майкрософт
description: Руководство по перемещению хранилища ключей в другую подписку.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: d881394391b7967fe602155eefc9844e013de34e
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724771"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Перемещение Azure Key Vault в другую подписку

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Обзор

> [!IMPORTANT]
> **Перемещение хранилища ключей в другую подписку приведет к критическому изменению среды.**
> Прежде чем принять решение о перемещении хранилища ключей в новую подписку, убедитесь, что вы понимаете последствия этого изменения, и внимательно следуйте указаниям в этой статье.
> Если вы используете управляемые удостоверения службы (MSI), ознакомьтесь с инструкциями после перемещения в конце документа. 

[Azure Key Vault](overview.md) автоматически привязывается к идентификатору клиента [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) по умолчанию для подписки, в которой он создается. Вы можете найти идентификатор клиента, связанный с подпиской, следуя [указаниям](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-to-find-tenant)в этом разделе. Все записи политики доступа и назначения ролей также привязаны к этому ИДЕНТИФИКАТОРу клиента.  Если вы перемещаете подписку Azure из клиента а в клиент б, существующие хранилища ключей будут недоступны субъектам-службам (пользователям и приложениям) в клиенте б. Чтобы устранить эту проблему, необходимо выполнить следующие действия.

* Измените идентификатор клиента, связанный со всеми существующими хранилищами ключей в этой подписке, для клиента Б.
* Удалите все существующие записи политики доступа.
* Добавьте новые записи политики доступа, связанные с клиентом Б.

Дополнительные сведения о Azure Key Vault и Azure Active Directory см. в разделе.
- [Об Azure Key Vault](overview.md)
- [Что такое Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
- [Как найти идентификатор клиента](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-to-find-tenant)

## <a name="limitations"></a>Ограничения

> [!IMPORTANT]
> **Не удается переместить хранилища ключей, используемые для шифрования дисков** Если вы используете хранилище ключей с шифрованием дисков для виртуальной машины, хранилище ключей нельзя переместить в другую группу ресурсов или подписку, пока включено шифрование дисков. Необходимо отключить шифрование дисков перед перемещением хранилища ключей в новую группу ресурсов или подписку. 

Некоторые субъекты-службы (пользователи и приложения) привязаны к конкретному клиенту. Если вы перемещаете хранилище ключей в подписку другого клиента, то есть вероятность, что вы не сможете восстановить доступ к определенному субъекту-службе. Убедитесь, что все основные субъекты-службы существуют в клиенте, где вы перемещаете хранилище ключей.

## <a name="prerequisites"></a>Предварительные требования

* Уровень [участника](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) доступа или выше текущей подписки, в которой существует хранилище ключей. Роль можно назначить с помощью [портал Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)или [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).
* Доступ на уровне [участника](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) или более высокий уровень к подписке, в которую необходимо переместить хранилище ключей. Роль можно назначить с помощью [портал Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)или [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).
* Группа ресурсов в новой подписке. Его можно создать с помощью [портал Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-powershell)или [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-cli).

Можно проверить существующие роли с помощью [портал Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal), [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-powershell), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-cli)или [REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-rest).


## <a name="moving-a-key-vault-to-a-new-subscription"></a>Перемещение хранилища ключей в новую подписку

1. Войдите на портал Azure по адресу https://portal.azure.com.
2. Перейдите в [хранилище ключей](overview.md) .
3. Перейдите на вкладку "Обзор".
4. Нажмите кнопку "Переместить".
5. Выберите пункт "переместить в другую подписку" из раскрывающегося списка параметров.
6. Выберите группу ресурсов, в которую необходимо переместить хранилище ключей.
7. Подтверждение предупреждения о перемещении ресурсов
8. Нажатие кнопки "ОК"

## <a name="additional-steps-when-subscription-is-in-a-new-tenant"></a>Дополнительные действия при создании подписки в новом клиенте

Если вы переместили хранилище ключей в подписку в новом клиенте, необходимо вручную обновить идентификатор клиента и удалить старые политики доступа и назначения ролей. Ниже приведены руководства для этих шагов в PowerShell и Azure CLI. Если вы используете PowerShell, может потребоваться выполнить команду Clear-AzContext, описанную ниже, чтобы просмотреть ресурсы за пределами текущей выбранной области. 

### <a name="update-tenant-id-in-a-key-vault"></a>Обновление идентификатора клиента в хранилище ключей

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource -ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```
### <a name="update-access-policies-and-role-assignments"></a>Обновление политик доступа и назначений ролей

> [!NOTE]
> Если Key Vault использует модель разрешений [RBAC Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) . Необходимо также удалить назначения ролей хранилища ключей. Назначения ролей можно удалить с помощью [портала Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)или [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell). 

Теперь, когда хранилище связано с правильным ИДЕНТИФИКАТОРом клиента, старые записи политики доступа или назначения ролей будут удалены, задайте новые записи политики доступа или назначения ролей.

Сведения о назначении политик см. в следующих статьях:
- [Назначение политики доступа с помощью портала](assign-access-policy-portal.md)
- [Назначение политики доступа с помощью Azure CLI](assign-access-policy-cli.md)
- [Назначение политики доступа с помощью PowerShell](assign-access-policy-powershell.md)

Сведения о добавлении назначений ролей см. в следующих статьях:
- [Добавление назначения ролей с помощью портала](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
- [Добавление назначения ролей с помощью Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)
- [Добавление назначения ролей с помощью PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)


### <a name="update-managed-identities"></a>Обновление управляемых удостоверений

Если вы передаете всю подписку и используете управляемое удостоверение для ресурсов Azure, необходимо также обновить его до нового клиента Azure Active Directory. Дополнительные сведения об управляемых удостоверениях см. в разделе [Общие сведения об управляемом удостоверении](../../active-directory/managed-identities-azure-resources/overview.md).

Если вы используете управляемое удостоверение, вам также потребуется обновить удостоверение, так как старое удостоверение больше не будет находиться в правильном Azure Active Directoryном клиенте. Чтобы устранить эту проблему, см. следующие документы. 

* [Обновление MSI](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [Перенос подписки в новый каталог](../../role-based-access-control/transfer-subscription.md)

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о [ключах, секретах и сертификатах](about-keys-secrets-certificates.md).
- Основные сведения, в том числе анализ журналов Key Vault, см. в разделе [ведение журнала Key Vault](logging.md)
- [Руководство разработчика Azure Key Vault](../general/developers-guide.md)
- [Защита хранилища ключей](secure-your-key-vault.md)
- [Настройка брандмауэров и виртуальных сетей Azure Key Vault](network-security.md)
