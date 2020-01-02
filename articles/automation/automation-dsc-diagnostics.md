---
title: Пересылка данных отчетов о настройке состояния службы автоматизации Azure в журналы Azure Monitor
description: В этой статье показано, как отправить данные отчетов о настройке требуемого состояния (DSC) из конфигурации состояния службы автоматизации Azure в журналы Azure Monitor для предоставления дополнительных сведений и управления.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9fa84b5e87581fad4a7ada5fda074429409d2f8f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850352"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Пересылка данных отчетов о настройке состояния службы автоматизации Azure в журналы Azure Monitor

В конфигурации состояния службы автоматизации Azure данные о состоянии узла сосохранены в течение 30 дней.
Вы можете отправить данные о состоянии узла в рабочую область Log Analytics, если вы предпочитаете хранить эти данные в течение более длительного периода.
Сведения о состоянии соответствий узлов и отдельных ресурсов DSC в конфигурации узла можно просмотреть на портале Azure или с помощью PowerShell.
С помощью журналов Azure Monitor можно:

- получать сведения о соответствии требованиям для управляемых узлов и отдельных ресурсов;
- активировать сообщение электронной почты или предупреждение (в зависимости от состояния соответствия);
- создавать сложные запросы к управляемым узлам;
- сопоставлять состояние соответствия в учетных записях службы автоматизации;
- визуализировать журнал соответствия узла с течением времени.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Технические условия

Чтобы начать отправку отчетов о конфигурации состояния автоматизации в журналы Azure Monitor, вам потребуется:

- Выпуск за ноябрь 2016 года или более поздний выпуск [Azure PowerShell](/powershell/azure/overview) (вер. 2.3.0).
- Учетная запись службы автоматизации Azure. Дополнительные сведения см. в статье [Приступая к работе со службой автоматизации Azure](automation-offering-get-started.md)
- Рабочая область Log Analytics с предложением службы **Автоматизация и управление**. Дополнительные сведения см. в статье [Приступая к работе с журналами Azure Monitor](../log-analytics/log-analytics-get-started.md).
- Как минимум один узел службы "Настройка состояния службы автоматизации Azure". Дополнительные сведения см. в статье [Подключение компьютеров для управления с помощью Azure Automation DSC](automation-dsc-onboarding.md).
- Модуль [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) версии 2.7.0.0 или более поздней. Инструкции по установке см. [в разделе Просмотр журналов DSC на узле](./troubleshoot/desired-state-configuration.md#steps-to-troubleshoot-desired-state-configuration-dsc).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Настройка интеграции с журналами Azure Monitor

Чтобы начать импорт данных из Automation DSC Azure в журналы Azure Monitor, выполните следующие действия.

1. Войдите в свою учетную запись Azure в PowerShell. См. статью [Вход с помощью Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
1. Получите идентификатор _ResourceId_ для учетной записи службы автоматизации, выполнив следующую команду PowerShell (при наличии нескольких учетных записей службы автоматизации выберите _ResourceID_ для учетной записи, которую требуется настроить):

   ```powershell
   # Find the ResourceId for the Automation Account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Получите идентификатор _ResourceId_ для рабочей области Log Analytics, выполнив следующую команду PowerShell (при наличии нескольких рабочих областей выберите _ResourceID_ для рабочей области, которую требуется настроить):

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Выполните следующую команду PowerShell, заменив `<AutomationResourceId>` и `<WorkspaceResourceId>` значениями _ResourceId_ из предыдущих шагов:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

Если вы хотите отключить импорт данных из конфигурации состояния службы автоматизации Azure в журналы Azure Monitor, выполните следующую команду PowerShell:

```powershell
Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
```

## <a name="view-the-state-configuration-logs"></a>Просмотр журналов State Configuration

После настройки интеграции с журналами Azure Monitor для данных конфигурации состояния службы автоматизации в колонке **узлы DSC** учетной записи службы автоматизации появится кнопка **поиска по журналам** . Нажмите кнопку **Поиск по журналам**, чтобы просмотреть журналы для данных узла DSC.

![Кнопка поиска по журналам](media/automation-dsc-diagnostics/log-search-button.png)

Откроется колонка **Поиск по журналам**, и для каждого узла State Configuration отобразятся данные операции **DscNodeStatusData**. Для каждого [ресурса DSC](/powershell/scripting/dsc/resources/resources), вызванного в конфигурации такого узла, отобразятся данные операции **DscResourceStatusData**.

Для операции **DscResourceStatusData** отображаются сведения об ошибках при сбое в работе любых ресурсов DSC.

Щелкните каждую операцию в списке, чтобы просмотреть сведения о ней.

Можно также просмотреть журналы, выполнив поиск в журналах Azure Monitor.
См. статью [Поиск данных по журналам](../log-analytics/log-analytics-log-searches.md).
Введите следующий запрос, чтобы найти журналы State Configuration: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus'`.

Можно сузить запрос, использовав имя операции. Например: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus' OperationName='DscNodeStatusData'`

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Отправка сообщения электронной почты при сбое проверки соответствия State Configuration

Наши клиенты часто интересуются возможностью отправки электронного сообщения или SMS-сообщения при возникновении ошибки в конфигурации DSC.

Чтобы создать правило генерации оповещений, начните с создания поиска журналов для записей отчетов State Configuration, которые должны вызывать оповещение. Щелкните кнопку **+ Новое правило генерации оповещений**, чтобы создать и настроить правило генерации оповещений.

1. На странице обзора рабочей области Log Analytics щелкните **журналы**.
1. Поищите по журналам свое оповещение при помощи следующего поискового запроса: `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Если вы настроили для рабочей области журналы из более чем одной учетной записи службы автоматизации или подписки, то можете группировать оповещения по подписке или учетной записи службы автоматизации.
   Имя учетной записи службы автоматизации можно получить из поля "Ресурс" для поискового запроса DscNodeStatusData.
1. Чтобы открыть экран **Создать правило**, щелкните **+ Новое правило генерации оповещений** в верхней части страницы. Дополнительные сведения о параметрах настройки предупреждения см. в разделе [Создание правила генерации оповещений](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Поиск ресурсов DSC со сбоями по всем узлам

Одним из преимуществ использования журналов Azure Monitor является то, что можно выполнять поиск непройденных проверок на разных узлах.
Чтобы найти все экземпляры ресурсов DSC со сбоями, выполните следующие действия:

1. На странице обзора рабочей области Log Analytics щелкните **журналы**.
1. Поищите по журналам свое оповещение при помощи следующего поискового запроса: `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Просмотр состояния узла DSC за предыдущие периоды

Наконец, вам может понадобиться визуализировать изменение состояния узла DSC с течением времени.
Для поиска состояния узла DSC за предыдущие периоды можно использовать следующий запрос:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

При этом отобразится диаграмма состояния узла с течением времени.

## <a name="azure-monitor-logs-records"></a>Записи журналов Azure Monitor

Диагностика из службы автоматизации Azure создает две категории записей в журналах Azure Monitor.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Свойство | Описание |
| --- | --- |
| TimeGenerated |Дата и время запуска проверки соответствия. |
| OperationName |DscNodeStatusData |
| ResultType |Подтверждено ли соответствие узла. |
| NodeName_s |Имя управляемого узла. |
| NodeComplianceStatus_s |Подтверждено ли соответствие узла. |
| DscReportStatus |Успешно ли прошла проверка соответствия. |
| ConfigurationMode | Применение конфигурации к узлу. Возможные значения: __ApplyOnly__, __ApplyandMonitior__ и __ApplyandAutoCorrect__. <ul><li>__ApplyOnly__ — DSC применяет конфигурацию и не выполняет дальнейшие действия, пока новая конфигурация не будет отправлена в целевой узел или получена с сервера. После первоначального применения новой конфигурации DSC не проверяет наличие отклонений от ранее настроенного состояния. Перед вступлением в силу __ApplyOnly__ DSC пытается применить конфигурацию, пока это не будет успешно реализовано. </li><li> __ApplyAndMonitor__ — значение по умолчанию. LCM применяет любую новую конфигурацию. Если после начального применения новой конфигурации состояние целевого узла отклоняется от требуемого, DSC сообщает о расхождении в журналах. Перед вступлением в силу __ApplyAndMonitor__ DSC пытается применить конфигурацию, пока это не будет успешно реализовано.</li><li>__ApplyAndAutoCorrect__ — DSC применяет любые новые конфигурации. Если после начального применения новой конфигурации состояние целевого узла отклоняется от требуемого, DSC сообщает о расхождении в журналах, а затем повторно применяет текущую конфигурацию.</li></ul> |
| HostName_s | Имя управляемого узла. |
| IPAddress | Адрес IPv4 управляемого узла. |
| Категория | DscNodeStatus |
| Ресурс | Имя учетной записи службы автоматизации Azure. |
| Tenant_g | GUID, идентифицирующий клиента для вызывающего объекта. |
| NodeId_g |Идентификатор GUID для определения управляемого узла. |
| DscReportId_g |Идентификатор GUID для определения отчета. |
| LastSeenTime_t |Дата и время последнего просмотра отчета. |
| ReportStartTime_t |Дата и время начала отчета. |
| ReportEndTime_t |Дата и время завершения отчета. |
| NumberOfResources_d |Количество ресурсов DSC, которые вызывались в примененной к узлу конфигурации. |
| SourceSystem | Как Azure Monitor журналы собирают данные. Всегда имеет значение *Azure* для системы диагностики Azure. |
| ResourceId |Указывает учетную запись службы автоматизации Azure. |
| ResultDescription | Описание для этой операции. |
| SubscriptionId | Идентификатор подписки Azure (GUID) для учетной записи службы автоматизации. |
| ResourceGroup | Имя группы ресурсов для учетной записи службы автоматизации. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |Идентификатор GUID для корреляции отчета о соответствии. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Свойство | Описание |
| --- | --- |
| TimeGenerated |Дата и время запуска проверки соответствия. |
| OperationName |DscResourceStatusData|
| ResultType |Подтверждено ли соответствие ресурса. |
| NodeName_s |Имя управляемого узла. |
| Категория | DscNodeStatus |
| Ресурс | Имя учетной записи службы автоматизации Azure. |
| Tenant_g | GUID, идентифицирующий клиента для вызывающего объекта. |
| NodeId_g |Идентификатор GUID для определения управляемого узла. |
| DscReportId_g |Идентификатор GUID для определения отчета. |
| DscResourceId_s |Имя для экземпляра ресурса DSC. |
| DscResourceName_s |Имя ресурса DSC. |
| DscResourceStatus_s |Подтверждено ли соответствие ресурса DSC. |
| DscModuleName_s |Имя модуля PowerShell, который содержит ресурс DSC. |
| DscModuleVersion_s |Версия модуля PowerShell, который содержит ресурс DSC. |
| DscConfigurationName_s |Имя примененной к узлу конфигурации. |
| ErrorCode_s | Код ошибки при сбое ресурса. |
| ErrorMessage_s |Сообщение об ошибке при сбое ресурса. |
| DscResourceDuration_d |Период выполнения ресурса DSC в секундах. |
| SourceSystem | Как Azure Monitor журналы собирают данные. Всегда имеет значение *Azure* для системы диагностики Azure. |
| ResourceId |Указывает учетную запись службы автоматизации Azure. |
| ResultDescription | Описание для этой операции. |
| SubscriptionId | Идентификатор подписки Azure (GUID) для учетной записи службы автоматизации. |
| ResourceGroup | Имя группы ресурсов для учетной записи службы автоматизации. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |Идентификатор GUID для корреляции отчета о соответствии. |

## <a name="summary"></a>Резюме

Отправляя данные конфигурации состояния автоматизации в журналы Azure Monitor, вы сможете получить более подробные сведения о состоянии узлов конфигурации состояния автоматизации, выполнив следующие действия.

- настроить оповещения, уведомляющие вас о проблемах;
- с помощью пользовательских представлений и поисковых запросов визуализировать результаты модуля Runbook, состояние задания Runbook и другие связанные ключевые индикаторы или метрики.

Журналы Azure Monitor обеспечивают более высокую оперативную видимость данных конфигурации состояния службы автоматизации и позволяют быстрее решать инциденты.

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения см. в статье с [обзором "Настройка состояния службы автоматизации Azure"](automation-dsc-overview.md).
- Чтобы приступить к работе со службой "Настройка состояния службы автоматизации Azure", см. сведения в [этой статье](automation-dsc-getting-started.md).
- Сведения о компилировании конфигураций DSC, которые затем можно назначить целевым узлам, см. в статье [Компилирование конфигураций в Azure Automation DSC](automation-dsc-compile.md).
- Справочник по командлетам PowerShell для службы "Настройка состояния службы автоматизации Azure" см. в [этой статье](/powershell/module/azurerm.automation/#automation).
- Сведения о ценах см. на странице [с ценами на службу "Настройка состояния службы автоматизации Azure"](https://azure.microsoft.com/pricing/details/automation/).
- Пример использования службы "Настройка состояния службы автоматизации Azure" и Chocolatey в конвейере непрерывного развертывания см. в [этой статье](automation-dsc-cd-chocolatey.md).
- Дополнительные сведения о том, как создавать различные поисковые запросы и просматривать журналы конфигурации состояния автоматизации с помощью журналов Azure Monitor, см. [в разделе Поиск по журналам в Azure Monitor журналах](../log-analytics/log-analytics-log-searches.md) .
- Дополнительные сведения о Azure Monitor журналах и источниках сбора данных см. [в разделе Сбор данных службы хранилища Azure в журналах Azure Monitor](../azure-monitor/platform/collect-azure-metrics-logs.md) .
