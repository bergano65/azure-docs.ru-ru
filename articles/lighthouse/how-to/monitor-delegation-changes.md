---
title: Мониторинг изменений делегирования в управляющем клиенте
description: Узнайте, как отслеживать действия делегирования от клиентов клиентов до управляющего клиента.
ms.date: 07/07/2020
ms.topic: how-to
ms.openlocfilehash: b30cbc025f97ab76be55f0f83e15603b40092ce3
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86105172"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Мониторинг изменений делегирования в управляющем клиенте

Как поставщик услуг, вы можете захотеть знать, когда клиентские подписки или группы ресурсов делегируются клиенту через [Azure лигхсаусе](../overview.md)или когда удаляются ранее делегированные ресурсы.

В управлении клиентом [Журнал действий Azure](../../azure-monitor/platform/platform-logs-overview.md) отслеживает действия делегирования на уровне клиента. Это зарегистрированное действие включает все добавленные или удаленные делегирования из всех клиентов.

В этом разделе объясняются разрешения, необходимые для отслеживания действий делегирования в клиент (для всех клиентов) и рекомендации по их выполнению. Он также содержит пример скрипта, который показывает один метод для создания запросов и составления отчетов по этим данным.

> [!IMPORTANT]
> Все эти действия должны выполняться в управляющем клиенте, а не в клиентах.

## <a name="enable-access-to-tenant-level-data"></a>Разрешение доступа к данным на уровне клиента

Для доступа к данным журнала действий на уровне клиента учетной записи должна быть назначена встроенная роль [читателя мониторинга](../../role-based-access-control/built-in-roles.md#monitoring-reader) в корневой области (/). Это назначение должно выполняться пользователем с ролью глобального администратора с дополнительными правами доступа.

### <a name="elevate-access-for-a-global-administrator-account"></a>Повышение уровня доступа для учетной записи глобального администратора

Чтобы назначить роль в корневой области (/), необходимо иметь роль глобального администратора с повышенным уровнем доступа. Такой доступ должен быть добавлен только в том случае, если необходимо сделать назначение роли, а затем удалить по завершении.

Подробные инструкции по добавлению и удалению повышения прав см. в статье [повышение уровня доступа для управления всеми подписками Azure и группами управления](../../role-based-access-control/elevate-access-global-admin.md).

После повышения уровня доступа ваша учетная запись будет иметь роль администратора доступа пользователей в Azure в корневой области. Это назначение ролей позволяет просматривать все ресурсы и назначать доступ в любой подписке или группе управления в каталоге, а также выполнять назначения ролей в корневой области. 

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>Создать новую учетную запись субъекта-службы для доступа к данным на уровне клиента

После повышения уровня доступа можно назначить учетной записи соответствующие разрешения, чтобы она могла запрашивать данные журнала действий на уровне клиента. Эта учетная запись должна иметь встроенную роль [модуля чтения мониторинга](../../role-based-access-control/built-in-roles.md#monitoring-reader) , назначенную в корневой области управляемого клиента.

> [!IMPORTANT]
> Предоставление назначения роли в корневой области означает, что те же разрешения будут применяться к каждому ресурсу в клиенте.

Так как это общий уровень доступа, рекомендуется назначить эту роль учетной записи субъекта-службы, а не отдельному пользователю или группе. Кроме того, мы рекомендуем использовать следующие рекомендации:

- [Создайте новую учетную запись субъекта-службы](../../active-directory/develop/howto-create-service-principal-portal.md) , которая будет использоваться только для этой функции, вместо того, чтобы назначать эту роль существующему субъекту-службе, используемому для другой автоматизации.
- Убедитесь, что этот субъект-служба не имеет доступа к каким-либо делегированным ресурсам клиента.
- [Используйте сертификат для проверки подлинности](../../active-directory/develop/howto-create-service-principal-portal.md#upload-a-certificate-or-create-a-secret-for-signing-in) и [безопасного хранения в Azure Key Vault](../../key-vault/general/best-practices.md).
- Ограничьте число пользователей, имеющих доступ к действиям от имени субъекта-службы.

Используйте один из следующих методов для создания назначений корневой области.

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

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>Удаление повышенного доступа для учетной записи глобального администратора

После создания учетной записи субъекта-службы и назначения роли читателя мониторинга в корневой области следует [Удалить доступ с повышенными правами](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) для учетной записи глобального администратора, так как этот уровень доступа больше не потребуется.

## <a name="query-the-activity-log"></a>Запрос журнала действий

После создания новой учетной записи субъекта-службы с наблюдением за доступом к корневой области управляемого клиента вы можете использовать его для запроса и создания отчетов о действиях делегирования в клиенте. 

[Этот Azure PowerShell сценарий](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) можно использовать для запроса последнего 1-дневного действия и отчетов по любому добавленному или удаленному делегированию (или неудачным попыткам). Он запрашивает данные [журнала действий клиента](/rest/api/monitor/TenantActivityLogs/List) , затем конструирует следующие значения, чтобы сообщить о добавлении или удалении делегирований.

- **Делегатедресаурцеид**: идентификатор делегированной подписки или группы ресурсов.
- **Кустомертенантид**: идентификатор клиента клиента.
- **Кустомерсубскриптионид**: идентификатор подписки, который был делегирован или содержит группу ресурсов, делегированных
- **Кустомерделегатионстатус**: изменение состояния делегированного ресурса (успешно или неудачно)
- **Евенттиместамп**: Дата и время записи в журнал изменения делегирования

При запросе этих данных учитывайте следующее.

- Если несколько групп ресурсов делегированы в одном развертывании, для каждой группы ресурсов будут возвращены отдельные записи.
- Изменения, внесенные в предыдущее делегирование (например, обновление структуры разрешений), будут регистрироваться как добавленные делегирования.
- Как отмечалось выше, у учетной записи должна быть встроенная роль читателя мониторинга в корневой области (/) для доступа к данным на уровне клиента.
- Эти данные можно использовать в собственных рабочих процессах и отчетах. Например, можно использовать [API сборщика данных HTTP (общедоступная Предварительная версия)](../../azure-monitor/platform/data-collector-api.md) для записи данных в Azure Monitor из клиента REST API, а затем использовать [группы действий](../../azure-monitor/platform/action-groups.md) для создания уведомлений или предупреждений.

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

- Узнайте, как подключить клиентов к [Azure лигхсаусе](../concepts/azure-delegated-resource-management.md).
- Сведения о [Azure Monitor](../../azure-monitor/index.yml) и [журнале действий Azure](../../azure-monitor/platform/platform-logs-overview.md).
