---
title: Архивация & отчета с помощью Azure Monitor Azure AD "Управление назначениями"
description: Узнайте, как архивировать журналы и создавать отчеты с Azure Monitor в Azure Active Directory управлении назначением.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 02/27/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: f89af42e32783de479c4302b19c0a7ddc1289bb8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202186"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Журналы архивации и отчетность по управлению назначениями Azure AD в Azure Monitor

Azure AD хранит события аудита в течение до 30 дней в журнале аудита. Однако вы можете хранить данные аудита дольше стандартного срока хранения, описанного в разделе [как долго данные отчетов хранятся в Azure AD](../reports-monitoring/reference-reports-data-retention.md), путем их маршрутизации в учетную запись хранения Azure или с помощью Azure Monitor. Затем можно использовать книги и пользовательские запросы и отчеты для этих данных.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Настройка Azure AD для использования Azure Monitor
Прежде чем использовать Azure Monitor книги, необходимо настроить Azure AD для отправки копии журналов аудита в Azure Monitor.

Для архивации журналов аудита Azure AD требуется Azure Monitor в подписке Azure. Дополнительные сведения о предварительных требованиях и предполагаемых затратах на использование Azure Monitor в [журналах действий Azure AD можно узнать в Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md).

**Предварительная роль**: глобальный администратор

1. Войдите в портал Azure в качестве пользователя, который является глобальным администратором. Убедитесь, что у вас есть доступ к группе ресурсов, содержащей рабочую область Azure Monitor.
 
1. Выберите **Azure Active Directory** щелкните **параметры диагностики** в разделе Мониторинг в меню навигации слева. Проверьте, уже существует ли параметр для отправки журналов аудита в эту рабочую область.

1. Если параметр еще не задан, щелкните **Добавить параметр диагностики**. Используйте инструкции в статье [Интеграция журналов Azure AD с Azure Monitor журналами](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) для отправки журнала аудита Azure AD в рабочую область Azure Monitor.

    ![Панель параметров диагностики](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. После отправки журнала в Azure Monitor выберите **log Analytics рабочие области**и выберите рабочую область, содержащую журналы аудита Azure AD.

1. Выберите **использование и предполагаемые затраты** и щелкните **хранение данных**. Измените значение ползунка на число дней, в течение которых данные должны соответствовать требованиям аудита.

    ![Панель "Log Analytics рабочих областей"](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Создание пользовательских запросов Azure Monitor с помощью портал Azure
Вы можете создавать собственные запросы к событиям аудита Azure AD, включая события управления назначениями.  

1. В Azure Active Directory портал Azure щелкните **журналы** в разделе Мониторинг в левом меню навигации, чтобы создать страницу запроса.

1. Рабочая область должна отображаться в левом верхнем углу страницы запроса. Если у вас несколько Azure Monitor рабочих областей, а Рабочая область, которую вы используете для хранения событий аудита Azure AD, не отображается, нажмите кнопку **выбрать область**. Затем выберите подходящую подписку и рабочую область.

1. Затем в текстовом поле запрос удалите строку "Search *" и замените ее следующим запросом:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Нажмите кнопку **Запуск**. 

    ![Нажмите кнопку выполнить, чтобы запустить запрос.](./media/entitlement-management-logs-and-reporting/run-query.png)

В таблице отображаются события журнала аудита для управления назначением за последний час по умолчанию. Вы можете изменить параметр "диапазон времени", чтобы просмотреть старые события. Однако при изменении этого параметра будут показаны только те события, которые произошли после настройки Azure AD для отправки событий в Azure Monitor.

Если вы хотите получить сведения о самых старых и новых событиях аудита, хранящихся в Azure Monitor, используйте следующий запрос:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Дополнительные сведения о столбцах, которые хранятся для событий аудита в Azure Monitor, см. [в разделе Интерпретация схемы журналов аудита Azure AD в Azure Monitor](../reports-monitoring/reference-azure-monitor-audit-log-schema.md).

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Создание пользовательских запросов Azure Monitor с помощью Azure PowerShell

Вы можете получить доступ к журналам через PowerShell после настройки Azure AD для отправки журналов в Azure Monitor. Затем отправляйте запросы из скриптов или из командной строки PowerShell без необходимости быть глобальным администратором в клиенте. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Убедитесь, что пользователь или субъект-служба имеет правильное назначение ролей.

Убедитесь, что пользователь или субъект-служба, которые будут проходить проверку подлинности в Azure AD, находятся в соответствующей роли Azure в Log Analytics рабочей области. Параметры роли могут быть Log Analytics читатель или участник Log Analytics. Если вы уже используете одну из этих ролей, перейдите к ней, чтобы [получить идентификатор log Analytics с одной подпиской Azure](#retrieve-log-analytics-id-with-one-azure-subscription).

Чтобы задать назначение ролей и создать запрос, выполните следующие действия.
1. В портал Azure выберите [рабочую область log Analytics](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
).

1. Выберите **Управление доступом (IAM)** .

1. Затем нажмите кнопку **Добавить** , чтобы добавить назначение роли.

    ![Добавление назначения роли](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Установка модуля Azure PowerShell

Получив соответствующее назначение роли, запустите PowerShell и [установите модуль Azure PowerShell](/powershell/azure/install-az-ps?view=azps-3.3.0) (если вы этого еще не сделали), введя:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
Теперь вы можете пройти проверку подлинности в Azure AD и получить идентификатор Log Analytics рабочей области, к которой выполняется запрос.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Получение идентификатора Log Analytics с одной подпиской Azure
Если у вас есть только одна подписка Azure и одна Log Analytics Рабочая область, введите следующую команду для проверки подлинности в Azure AD, подключитесь к этой подписке и получите эту рабочую область:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Получение идентификатора Log Analytics с несколькими подписками Azure

 [Get-азоператионалинсигхтсворкспаце](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) работает в одной подписке за раз. Таким образом, если у вас несколько подписок Azure, необходимо подключиться к той, у которой есть рабочая область Log Analytics с журналами Azure AD. 
 
 Следующие командлеты выводят список подписок и находят идентификатор подписки с рабочей областью Log Analytics:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
Вы можете повторно пройти проверку подлинности и связать сеанс PowerShell с этой подпиской с помощью команды, например `Connect-AzAccount –Subscription $subs[0].id`. Дополнительные сведения о проверке подлинности в Azure с помощью PowerShell, в том числе в неинтерактивном режиме, см. в разделе [Вход с использованием Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
).

Если в этой подписке имеется несколько Log Analytics рабочих областей, командлет [Get-азоператионалинсигхтсворкспаце](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) возвращает список рабочих областей. Затем можно найти тот, у которого есть журналы Azure AD. Поле `CustomerId`, возвращаемое этим командлетом, совпадает со значением "идентификатор рабочей области", отображаемым в портал Azure в обзоре рабочей области Log Analytics.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>Отправка запроса в рабочую область Log Analytics
Наконец, после определения рабочей области можно использовать [командлет Invoke-азоператионалинсигхтскуери](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
) , чтобы отправить запрос Kusto в эту рабочую область. Эти запросы написаны на [языке запросов Kusto](https://docs.microsoft.com/azure/kusto/query/).
 
Например, можно получить диапазон дат записей событий аудита из рабочей области Log Analytics, используя командлеты PowerShell для отправки запроса, например:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

Вы также можете получать события управления назначением с помощью запроса, например:

```azurepowershell
$bQuery = = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Следующие шаги
- [Создание интерактивных отчетов с помощью Azure Monitor книг](../../azure-monitor/app/usage-workbooks.md) 

