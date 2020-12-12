---
title: Мониторинг изменений делегирования в управляющем клиенте
description: Узнайте, как отслеживать действия делегирования от клиентов клиентов до управляющего клиента.
ms.date: 12/11/2020
ms.topic: how-to
ms.openlocfilehash: 8d254c90881d23255dda5c38a868eb61e51b4f08
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356418"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Мониторинг изменений делегирования в управляющем клиенте

Как поставщик услуг, вы можете захотеть знать, когда клиентские подписки или группы ресурсов делегируются клиенту через [Azure лигхсаусе](../overview.md)или когда удаляются ранее делегированные ресурсы.

В управлении клиентом [Журнал действий Azure](../../azure-monitor/platform/platform-logs-overview.md) отслеживает действия делегирования на уровне клиента. Это зарегистрированное действие включает все добавленные или удаленные делегирования из всех клиентов.

В этом разделе объясняются разрешения, необходимые для отслеживания действий делегирования в клиент (для всех клиентов) и рекомендации по их выполнению. Он также содержит пример скрипта, который показывает один метод для создания запросов и составления отчетов по этим данным.

> [!IMPORTANT]
> Все эти действия должны выполняться в управляющем клиенте, а не в клиентах.

## <a name="enable-access-to-tenant-level-data"></a>Разрешение доступа к данным на уровне клиента

Для доступа к данным журнала действий на уровне клиента учетной записи должна быть назначена встроенная роль Azure [Monitoring Reader](../../role-based-access-control/built-in-roles.md#monitoring-reader) в корневой области (/). Это назначение должно выполняться пользователем с ролью глобального администратора с дополнительными правами доступа.

### <a name="elevate-access-for-a-global-administrator-account"></a>Повышение уровня доступа для учетной записи глобального администратора

Чтобы назначить роль в корневой области (/), необходимо иметь роль глобального администратора с повышенным уровнем доступа. Такой доступ должен быть добавлен только в том случае, если необходимо сделать назначение роли, а затем удалить по завершении.

Подробные инструкции по добавлению и удалению повышения прав см. в статье [повышение уровня доступа для управления всеми подписками Azure и группами управления](../../role-based-access-control/elevate-access-global-admin.md).

После повышения уровня доступа ваша учетная запись будет иметь роль администратора доступа пользователей в Azure в корневой области. Это назначение ролей позволяет просматривать все ресурсы и назначать доступ в любой подписке или группе управления в каталоге, а также выполнять назначения ролей в корневой области.

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>Создать новую учетную запись субъекта-службы для доступа к данным на уровне клиента

После повышения уровня доступа можно назначить учетной записи соответствующие разрешения, чтобы она могла запрашивать данные журнала действий на уровне клиента. Эта учетная запись должна иметь встроенную роль Azure [Monitoring Reader](../../role-based-access-control/built-in-roles.md#monitoring-reader) , назначенную в корневой области управляемого клиента.

> [!IMPORTANT]
> Предоставление назначения роли в корневой области означает, что те же разрешения будут применяться к каждому ресурсу в клиенте.

Так как это общий уровень доступа, рекомендуется назначить эту роль учетной записи субъекта-службы, а не отдельному пользователю или группе.

 Кроме того, мы рекомендуем использовать следующие рекомендации:

- [Создайте новую учетную запись субъекта-службы](../../active-directory/develop/howto-create-service-principal-portal.md) , которая будет использоваться только для этой функции, вместо того, чтобы назначать эту роль существующему субъекту-службе, используемому для другой автоматизации.
- Убедитесь, что этот субъект-служба не имеет доступа к каким-либо делегированным ресурсам клиента.
- [Используйте сертификат для проверки подлинности](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) и [безопасного хранения в Azure Key Vault](../../key-vault/general/best-practices.md).
- Ограничьте число пользователей, имеющих доступ к действиям от имени субъекта-службы.

> [!NOTE]
> Можно также назначить встроенную роль Azure Monitoring Reader в корневой области отдельным пользователям или группам пользователей. Это может быть полезно, если вы хотите, чтобы пользователь мог [просматривать сведения о делегировании непосредственно в портал Azure](#view-delegation-changes-in-the-azure-portal). При этом имейте в виду, что это общий уровень доступа, который должен быть ограничен минимальным числом возможных пользователей.

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
- Как отмечалось выше, для доступа к данным на уровне клиента учетная запись должна иметь встроенную роль Azure Monitoring Reader в корневой области (/).
- Эти данные можно использовать в собственных рабочих процессах и отчетах. Например, можно использовать [API сборщика данных HTTP (общедоступная Предварительная версия)](../../azure-monitor/platform/data-collector-api.md) для записи данных в Azure Monitor из клиента REST API, а затем использовать [группы действий](../../azure-monitor/platform/action-groups.md) для создания уведомлений или предупреждений.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the last 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri     = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization  = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method  = 'GET'
}
$list = Invoke-RestMethod @listOperations

# First link can be empty - and point to a next link (or potentially multiple pages)
# While you get more data - continue fetching and add result
while($list.nextLink){
    $list2 = Invoke-RestMethod $list.nextLink -Headers $listOperations.Headers -Method Get
    $data+=$list2.value;
    $list.nextLink = $list2.nextlink;
}

$showOperations = $data;

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action") {
    $registerOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action" }
    foreach ($registerOutput in $registerOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $registerOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has registered delegated resources to your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId; 
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp           = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") {
    $unregisterOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action" }
    foreach ($unregisterOutput in $unregisterOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $unregisterOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has unregistered delegated resources from your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId;
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp           = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else {
    Write-Output "No new delegation events for tenant: $($currentContext.Tenant.TenantId)"
}
```

> [!TIP]
> Хотя в этом разделе мы будем называть поставщиков услуг и клиентов, [предприятия, управляющие несколькими клиентами](../concepts/enterprise.md) , могут использовать одни и те же процессы.

## <a name="view-delegation-changes-in-the-azure-portal"></a>Просмотр изменений делегирования в портал Azure

Пользователи, которым назначена встроенная роль Azure Monitoring Reader в корневой области, могут просматривать изменения делегирования непосредственно в портал Azure.

1. Перейдите на страницу **Мои клиенты** , а затем в меню навигации слева выберите **Журнал действий** .
1. Убедитесь, что в фильтре в верхней части экрана выбрано **действие каталог** .

Появится список изменений делегирования. Можно выбрать пункт **изменить столбцы** , чтобы показать или скрыть **состояние**, **категорию событий**, **время**, **метку времени**, **подписку**, **событие, инициированное**, **группой ресурсов**, **типом ресурса** и значениями **ресурсов** .

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как подключить клиентов к [Azure лигхсаусе](../concepts/azure-delegated-resource-management.md).
- Сведения о [Azure Monitor](../../azure-monitor/index.yml) и [журнале действий Azure](../../azure-monitor/platform/platform-logs-overview.md).
- В образце книги " [журналы действий по доменам](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) " содержатся сведения о том, как отображать журналы действий Azure в нескольких подписках с возможностью фильтрации по доменному имени.
