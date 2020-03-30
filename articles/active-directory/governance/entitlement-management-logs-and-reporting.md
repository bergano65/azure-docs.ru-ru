---
title: Архив& отчет с Azure Monitor - Управление правами Azure AD
description: Узнайте, как архивировать журналы и создавать отчеты с помощью Azure Monitor в управлении активным каталогом Azure.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 070b7c5e0fef7d50f84271190432a65d29699bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128628"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Архивные журналы и отчеты об управлении правами Azure AD в Azure Monitor

Azure AD хранит события аудита на срок до 30 дней в журнале аудита. Тем не менее, данные аудита хранятся дольше, чем период удержания по умолчанию, описанный в том, как долго хранит данные аудита [Azure AD?](../reports-monitoring/reference-reports-data-retention.md)- направив их на учетную запись Azure Storage или используя Azure Monitor. Затем можно использовать трудовые книжки и пользовательские запросы и отчеты по этим данным.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Нанастройка Azure AD для использования Azure Monitor
Прежде чем использовать рабочие книги Azure Monitor, необходимо настроить Azure AD для отправки копии журналов аудита в Azure Monitor.

Архивирование журналов аудита Azure AD требует наличия Azure Monitor в подписке Azure. Вы можете прочитать больше о предпосылках и сметных затратах на использование Azure Monitor в [журналах деятельности Azure AD в журнале Azure Monitor.](../reports-monitoring/concept-activity-logs-azure-monitor.md)

**Предпосылкная роль**: Глобальный админ

1. Вопиюсь на портал Azure в качестве пользователя, являющаяся глобальным амином. Убедитесь, что у вас есть доступ к группе ресурсов, содержащей рабочее пространство Azure Monitor.
 
1. Выберите **Active Directory Azure,** затем нажмите **параметры Диагностики** под мониторингом в левом меню навигации. Проверьте, есть ли уже настройки для отправки журналов аудита в рабочее пространство.

1. Если настройки еще не установлены, нажмите **«Добавить диагностическую настройку».** Используйте инструкции в статье [Integrate Azure AD журналов с журналами Azure Monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) для отправки журнала аудита Azure AD в рабочее пространство Azure Monitor.

    ![Панель параметров диагностики](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. После отправки журнала в Azure Monitor выберите **рабочие места analytics журнала**и выберите рабочее пространство, содержащее журналы аудита Azure AD.

1. Выберите **Использование и сметные затраты** и нажмите **на хранение данных.** Измените ползунок на количество дней, которые вы хотите сохранить, чтобы данные соответствовали вашим требованиям аудита.

    ![Панель рабочих пространств журнала Analytics](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Создание пользовательских запросов Azure Monitor с помощью портала Azure
Вы можете создавать собственные запросы на событиях аудита Azure AD, включая события управления правами.  

1. В Active Directory Azure портала Azure щелкните **журналы** под разделом Мониторинг в меню левой навигации, чтобы создать новую страницу запроса.

1. Рабочее пространство должно отображаться в левом верхнем углу страницы запроса. Если у вас есть несколько рабочих пространств Azure Monitor, а рабочее пространство, которое используется для хранения событий аудита Azure AD, не отображается, нажмите **Select Scope.** Затем выберите правильную подписку и рабочее пространство.

1. Далее, в области текста запроса, удалите строку "поиск" и замените ее следующим запросом:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Нажмите кнопку **Запуск**. 

    ![Нажмите Выполнить, чтобы начать запрос](./media/entitlement-management-logs-and-reporting/run-query.png)

В таблице будут отображаться события журнала аудита для управления правами за последний час по умолчанию. Можно изменить настройки "Диапазон времени" для просмотра старых событий. Однако изменение этого параметра будет отображать только события, возникшие после настройки Azure AD для отправки событий в Azure Monitor.

Если вы хотите узнать старейшие и новейшие аудиторские мероприятия, проводимые в Azure Monitor, используйте следующий запрос:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Для получения дополнительной информации о столбцах, хранящихся для событий аудита в Azure Monitor, [см.](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Создавайте пользовательские запросы Azure Monitor с помощью Azure PowerShell

Вы можете получить доступ к журналам через PowerShell после настройки Azure AD для отправки журналов в Azure Monitor. Затем отправьте запросы из скриптов или командной строки PowerShell, без необходимости быть глобальным админом в арендаторе. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Убедитесь, что у пользователя или директора службы есть правильное назначение ролей

Убедитесь, что вы, пользователь или директор службы, которые будут аутентифицироваться до Azure AD, в соответствующей роли Azure в рабочей области журнала Analytics. Варианты роли являются либо журнала Analytics Reader или журнал Analytics вкладчика. Если вы уже в одной из этих ролей, то перейдите к [retrieve Log Analytics ID с одной подпиской Azure.](#retrieve-log-analytics-id-with-one-azure-subscription)

Чтобы установить назначение ролей и создать запрос, сделайте следующие шаги:
1. На портале Azure найдите [рабочее пространство Log Analytics.](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
)

1. Выберите **элемент управления доступом (IAM)**.

1. Затем нажмите **Добавить,** чтобы добавить назначение роли.

    ![Добавление назначения роли](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Установите модуль Azure PowerShell.

Если у вас есть соответствующее назначение роли, запустите PowerShell и [установите модуль Azure PowerShell](/powershell/azure/install-az-ps?view=azps-3.3.0) (если вы еще не сделали этого), введя:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
Теперь вы готовы к аутентификации AD Azure и получению идентификатора рабочего пространства Log Analytics, который вы задавили.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Извлечение идентификатора аналитики журнала с помощью одной подписки Azure
Если у вас есть только одна подписка Azure и одно рабочее пространство Log Analytics, введите следующее для проверки подлинности Azure AD, подключитесь к этой подписке и получите это рабочее пространство:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Retrieve Log Analytics ID с несколькими подписками Azure

 [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) работает по одной подписке за один раз. Таким образом, если у вас есть несколько подписок Azure, вы хотите, чтобы убедиться, что вы подключитесь к той, которая имеет рабочее пространство журнала Analytics с журналами Azure AD. 
 
 Следующие cmdlets отображают список подписок и находят идентификатор подписки, в который имеется рабочее пространство Log Analytics:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
Вы можете повторно подтвердить подлинность и связать сеанс PowerShell `Connect-AzAccount –Subscription $subs[0].id`с этой подпиской, используя такую команду, как . Подробнее о том, как проверить подлинность Azure от PowerShell, втомимого в неинтерактивном количестве, можно узнать в [Azure PowerShell.](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
)

Если в этой подписке есть несколько рабочих областей анализа журналов, то cmdlet [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) возвращает список рабочих областей. Затем вы можете найти тот, который имеет журналы Azure AD. Поле, `CustomerId` возвращенное этим cmdlet, такое же, как и значение "Идента рабочего пространства", отображаемый на портале Azure в обзоре рабочего пространства журнала Analytics.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>Отправка запроса в рабочее пространство Log Analytics
Наконец, после того, как у вас есть рабочее пространство определены, вы можете использовать [Invoke-AzOperationalInsights's'kery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
) для отправки запроса Kusto в это рабочее пространство. Эти запросы написаны [на языке запросов Kusto.](https://docs.microsoft.com/azure/kusto/query/)
 
Например, можно получить диапазон дат аудиторских записей событий из рабочего пространства Log Analytics, а смдлеты PowerShell отправят запрос, например:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

Вы также можете получить события управления правами с помощью запроса, например:

```azurepowershell
$bQuery = = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Дальнейшие действия:
- [Создание интерактивных отчетов с книгами Azure Monitor](../../azure-monitor/app/usage-workbooks.md) 

