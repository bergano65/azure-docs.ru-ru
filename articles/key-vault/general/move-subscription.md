---
title: Azure Key Vault перемещение хранилища в другую подписку | Документация Майкрософт
description: Руководство по перемещению хранилища ключей в другую подписку.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: d37fae18cd2f3e3bfad647cc176253dc6bb101ab
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585790"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Перемещение Azure Key Vault в другую подписку

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Обзор

> [!IMPORTANT]
> **Перемещение хранилища ключей в другую подписку приведет к критическому изменению среды.**
> Прежде чем принять решение о перемещении хранилища ключей в новую подписку, убедитесь, что вы понимаете последствия этого изменения, и внимательно следуйте указаниям в этой статье.
> Если вы используете управляемые удостоверения службы (MSI), ознакомьтесь с инструкциями после перемещения в конце документа. 

При создании хранилища ключей оно автоматически привязывается к Azure Active Directory ИДЕНТИФИКАТОРу клиента по умолчанию для подписки, в которой он создается. Все записи политики доступа также привязываются к этому идентификатору клиента. Если вы перемещаете подписку Azure из клиента а в клиент б, существующие хранилища ключей будут недоступны субъектам-службам (пользователям и приложениям) в клиенте б. Чтобы устранить эту проблему, необходимо выполнить следующие действия.

* Измените идентификатор клиента, связанный со всеми существующими хранилищами ключей в этой подписке, для клиента Б.
* Удалите все существующие записи политики доступа.
* Добавьте новые записи политики доступа, связанные с клиентом Б.

## <a name="limitations"></a>Ограничения

Некоторые субъекты-службы (пользователи и приложения) привязаны к конкретному клиенту. Если вы перемещаете хранилище ключей в подписку другого клиента, то есть вероятность, что вы не сможете восстановить доступ к определенному субъекту-службе. Убедитесь, что все основные субъекты-службы существуют в клиенте, где вы перемещаете хранилище ключей.

## <a name="design-considerations"></a>Рекомендации по проектированию

В Организации может быть реализована политика Azure с принудительным применением или исключениями на уровне подписки. Может существовать другой набор назначений политик в подписке, где уже существует хранилище ключей, и подписка, в которую вы перемещаете хранилище ключей. Конфликт в требованиях политики может привести к нарушению работы приложений.

### <a name="example"></a>Пример

У вас есть приложение, подключенное к хранилищу ключей, которое создает сертификаты, действительные в течение двух лет. Подписка, в которую вы пытаетесь переместить хранилище ключей, имеет назначение политики, блокирующее создание сертификатов, которые действительны дольше одного года. После перемещения хранилища ключей в новую подписку операция создания сертификата, действующего в течение двух лет, будет заблокирована назначением политики Azure.

### <a name="solution"></a>Решение

Перейдите на страницу политики Azure на портал Azure и просмотрите назначения политик для текущей подписки, а также подписку, на которую вы перемещаетесь, и убедитесь в отсутствии несоответствий.

## <a name="prerequisites"></a>Предварительные требования

* Уровень участника доступа или выше текущей подписки, в которой существует хранилище ключей.
* Доступ на уровне участника или более высокий уровень к подписке, в которую необходимо переместить хранилище ключей.
* Группа ресурсов в новой подписке.

## <a name="procedure"></a>Процедура

### <a name="initial-steps-moving-key-vault"></a>Начальные шаги (перемещение Key Vault)

1. Войдите на портал Azure.
2. Перейдите в хранилище ключей.
3. Перейдите на вкладку "Обзор".
4. Нажмите кнопку "Переместить".
5. Выберите пункт "переместить в другую подписку" из раскрывающегося списка параметров.
6. Выберите группу ресурсов, в которую необходимо переместить хранилище ключей.
7. Подтверждение предупреждения о перемещении ресурсов
8. Нажатие кнопки "ОК"

### <a name="additional-steps-post-move"></a>Дополнительные действия (после перемещения)

Теперь, когда хранилище ключей перемещено в новую подписку, необходимо обновить идентификатор клиента и удалить старые политики доступа. Ниже приведены руководства для этих шагов в PowerShell и Azure CLI.

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
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

Теперь, когда хранилище связано с правильным идентификатором клиента, и старые записи политики доступа удалены, задайте новые записи политики доступа с помощью командлета [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) или команды Azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy).

Если вы используете управляемое удостоверение для ресурсов Azure, необходимо также обновить его до нового клиента Azure AD. Дополнительные сведения об управляемых удостоверениях см. в разделе [Выполнение проверки подлинности Key Vault с помощью управляемого удостоверения](managed-identity.md).

Если вы используете MSI, необходимо также обновить идентификатор MSI, так как старый идентификатор больше не будет находиться в правильном клиенте AAD. Чтобы устранить эту проблему, см. следующие документы. 

* [Обновление MSI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)
* [Перенос подписки в новый каталог](https://docs.microsoft.com/azure/role-based-access-control/transfer-subscription)


