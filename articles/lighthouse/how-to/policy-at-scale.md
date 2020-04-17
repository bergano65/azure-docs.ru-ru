---
title: Развертывание службы "Политика Azure" для делегированных подписок в масштабе
description: Узнайте, как с помощью системы делегированного управления ресурсами Azure развернуть определение и назначение политики в нескольких клиентах.
ms.date: 11/8/2019
ms.topic: conceptual
ms.openlocfilehash: 3fe7e48c56e9a5af93e9642ee16c50cfbce34f9e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481827"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>Развертывание службы "Политика Azure" для делегированных подписок в масштабе

Как поставщик услуг вы можете подключить несколько клиентов для системы делегированного управления ресурсами Azure. [Azure Lighthouse](../overview.md) позволяет поставщикам служб выполнять операции одновременно в нескольких клиентах, что делает задачи управления более эффективными.

В этой статье показано, как с помощью службы [Политика Azure](../../governance/policy/index.yml) развернуть определение и назначение политики в нескольких клиентах с использованием команд PowerShell. В этом примере определение политики обеспечивает защиту учетных записей хранения, разрешая только HTTPS-трафик.

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>Отправка запросов между клиентами с помощью Azure Resource Graph

[Azure Resource Graph](../../governance/resource-graph/index.yml) позволяет отправлять запросы между всеми подписками в клиентах, которыми вы управляете. В этом примере мы идентифицируем любые учетные записи хранилища в этих подписках, которые в настоящее время не требуют трафика HTTPS.  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>Развертывание политики для нескольких клиентов

В приведенном ниже примере показано, как с помощью [шаблона Azure Resource Manager](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json) развернуть определение и назначения политики между делегированными подписками в нескольких клиентах. Это определение политики требует, чтобы все учетные записи хранилища использовали трафик HTTPS, предотвращая создание любых новых учетных записей хранения, которые не соответствуют требованиям, и маркировки существующих учетных записей хранения без установки как несовместимых.

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzSubscriptionDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

## <a name="validate-the-policy-deployment"></a>Проверка развертывания политики

После развертывания шаблона Azure Resource Manager можно подтвердить, что определение политики было успешно применено, пытаясь создать учетную запись хранения с **EnableHttpsTrafficOnly,** установленной **на ложную** в одной из делегированных подписок. Из-за назначения политики вы не сможете создать эту учетную запись хранения.  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>Очистка ресурсов

После завершения работы удалите определение и назначение политики, созданное развертыванием.

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzSubscriptionDeployment -Name mgmt -AsJob

    $Assignment = Get-AzPolicyAssignment | where-object {$_.Name -like "enforce-https-storage-assignment"}

    if ([string]::IsNullOrEmpty($Assignment))
    {
        Write-Output "Nothing to clean up - we're done"
    }
    else
    {

    Remove-AzPolicyAssignment -Name 'enforce-https-storage-assignment' -Scope "/subscriptions/$($ManagedSub.subscriptionId)" -Verbose

    Write-Output "Deployment has been deleted - we're done"
    }
}
```

## <a name="next-steps"></a>Следующие шаги

- Сведения о службе [Политика Azure](../../governance/policy/index.yml).
- Узнайте больше об [интерфейсах управления для различных клиентов](../concepts/cross-tenant-management-experience.md).
