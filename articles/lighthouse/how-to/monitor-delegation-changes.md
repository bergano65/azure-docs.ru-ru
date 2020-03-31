---
title: Мониторинг изменений делегирования в управляющем арендаторе
description: Узнайте, как контролировать активность делегирования от клиентов до управляющего арендатора.
ms.date: 03/16/2020
ms.topic: conceptual
ms.openlocfilehash: 99aa05cb73326e441c0473855c27dc71212cf415
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478231"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Мониторинг изменений делегирования в управляющем арендаторе

Как поставщик услуг, вы можете быть в курсе, когда подписка клиентов или группы ресурсов делегируются арендатору через [управление ресурсами Azure делегированными](../concepts/azure-delegated-resource-management.md)ресурсами, или когда ранее делегированные ресурсы удаляются.

В реумн-арендаторе [журнал деятельности Azure](../../azure-monitor/platform/platform-logs-overview.md) отслеживает активность делегирования на уровне арендатора. Эта зарегистрированная деятельность включает в себя все добавленные или удаленные делегирования от всех клиентов-арендаторов.

В этой теме разъясняются разрешения, необходимые для мониторинга деятельности делегации арендатору (во всех ваших клиентах) и наилучшие методы для этого. Он также включает в себя пример скрипта, который показывает один метод для запроса и отчетности по этим данным.

> [!IMPORTANT]
> Все эти шаги должны выполняться в вашем управляющем арендаторе, а не в каких-либо клиентах-арендаторах.

## <a name="enable-access-to-tenant-level-data"></a>Включить доступ к данным уровня арендатора

Чтобы получить доступ к данным журнала активности на уровне клиента, учетная запись должна быть назначена встроенная роль [читателя мониторинга](../../role-based-access-control/built-in-roles.md#monitoring-reader) в корневой области (/). Это назначение должно выполняться пользователем, который имеет роль Глобального Администратора с дополнительным повышенным доступом.

### <a name="elevate-access-for-a-global-administrator-account"></a>Повысить доступ к учетной записи Глобального администратора

Чтобы назначить роль в корневой области (/), вам необходимо иметь роль Глобального Администратора с повышенным доступом. Этот повышенный доступ должен быть добавлен только тогда, когда вам нужно сделать назначение роли, а затем удален, когда вы сделали.

Подробные инструкции по добавлению и удалению [высоты](../../role-based-access-control/elevate-access-global-admin.md)см.

После повышения доступа ваша учетная запись будет иметь роль администратора пользовательского доступа в Azure в корневой области. Это назначение ролей позволяет просматривать все ресурсы и назначать доступ в любой группе подписки или управления в каталоге, а также делать назначения ролей в корневой области. 

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>Создание новой основной учетной записи службы для доступа к данным уровня клиента

После того как вы повысили доступ, можно назначить соответствующие разрешения учетной записи, чтобы она могла загонять данные журнала активности на уровне арендатора. Эта учетная запись должна иметь встроенную роль [Reader мониторинга,](../../role-based-access-control/built-in-roles.md#monitoring-reader) назначенную в корневой области вашего управляющего арендатора.

> [!IMPORTANT]
> Предоставление назначения ролей в корневой области означает, что одни и те же разрешения будут применяться к каждому ресурсу в арендаторе.

Поскольку это широкий уровень доступа, мы рекомендуем назначить эту роль основной учетной записи службы, а не отдельному пользователю или группе. Кроме того, мы рекомендуем следующие рекомендации:

- [Создайте новую основную учетную запись службы,](../../active-directory/develop/howto-create-service-principal-portal.md) которая будет использоваться только для этой функции, вместо того, чтобы назначать эту роль существующему директору службы, используемому для другой автоматизации.
- Убедитесь, что этот директор службы не имеет доступа к каким-либо делегированным ресурсам клиента.
- [Используйте сертификат для проверки подлинности](../../active-directory/develop/howto-create-service-principal-portal.md#certificates-and-secrets) и [безопасного хранения в Azure Key Vault.](../../key-vault/key-vault-best-practices.md)
- Ограничьте доступ пользователей к действиям от имени директора службы.

Используйте один из следующих методов, чтобы сделать присугнительные назначения области.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ApplicationId $servicePrincipal.ApplicationId 
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>Удалить повышенный доступ для учетной записи Глобального администратора

После того как вы создали основную учетную запись службы и присвоили роль Reader мониторинга в корневой области, не забудьте [удалить повышенный доступ](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) для учетной записи Глобального администратора, так как этот уровень доступа больше не будет нужен.

## <a name="query-the-activity-log"></a>Запрос журнала активности

После создания новой основной учетной записи службы с доступом Reader к корневой области управляющего клиента можно использовать ее для запроса и отчета о деятельности делегации в арендаторе. 

Приведенная ниже проба использует Azure PowerShell для запроса за последний 1 день действия и отчетов о любых добавленных или удаленных делегациях (или попытках, которые не увенчались успехом). Он запрашивает данные [журнала активности арендатора,](https://docs.microsoft.com/rest/api/monitor/TenantActivityLogs/List) а затем строит следующие значения, чтобы сообщить о добавляемых или удаленных делегациях:

- **DelegatedResourceId**: Идентификатор делегированной подписки или группы ресурсов
- **CustomerTenantId**: Идентификатор клиента
- **CustomerSubscriptionId**: Идентификатор подписки, который был делегирован или содержащий группу ресурсов, которая была делегирована
- **CustomerDelegationStatus**: Изменение статуса для делегированного ресурса (успешно или неудачно)
- **EventTimeStamp**: Дата и время, когда изменение делегации было зарегистрировано

При запросе этих данных имейте в виду:

- Если несколько групп ресурсов делегированы в одно развертывание, отдельные записи будут возвращены для каждой группы ресурсов.
- Изменения, внесенные в предыдущую делегацию (например, обновление структуры разрешения), будут зарегистрированы в качестве дополнительной делегации.
- Как отмечалось выше, учетная запись должна иметь роль читателя мониторинга в корневой области (/) для того, чтобы получить доступ к этим данным уровня арендатора.
- Эти данные можно использовать в собственных рабочих процессах и отчетности. Например, можно использовать [API http Data Collector (публичный предварительный просмотр)](../../azure-monitor/platform/data-collector-api.md) для регистрации данных в Azure Monitor от клиента REST API, а затем использовать [группы действий](../../azure-monitor/platform/action-groups.md) для создания уведомлений или оповещений.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the past 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method = 'GET'
}
$list = Invoke-RestMethod @listOperations
$showOperations = $list.value

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action")
{
    $registerOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action"}
    foreach ($registerOutput in $registerOutputs)
    {
    $registerOutputdata = [pscustomobject]@{
        Event = "An Azure customer has delegated resources to your tenant";
        DelegatedResourceId = $registerOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $registerOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $registerOutput.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") 
{
    $unregisterOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action"}
    foreach ($unregisterOutput in $unregisterOutputs)
    {
    $unregisterOutputdata = [pscustomobject]@{
        Event = "An Azure customer has removed delegated resources from your tenant";
        DelegatedResourceId = $unregisterOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $unregisterOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $unregisterOutput.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else 
{
    Write-Output "No new delegation changes."
}   


```

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как на борт клиентов [делегировать ресурсы Azure.](../concepts/azure-delegated-resource-management.md)
- Узнайте больше о [Azure Monitor](../../azure-monitor/index.yml) и [журнале активности Azure.](../../azure-monitor/platform/platform-logs-overview.md)
