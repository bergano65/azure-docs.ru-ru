---
title: Перенапереживайте данные о состоянии автоматизации Azure в журналы Azure Monitor
description: В этой статье показано, как отправлять данные о достоверной конфигурации состояния (DSC) из конфигурации azure Automation State В журналы Azure Monitor для получения дополнительной информации и управления.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 578fcf4cd03a2d4fc8400b9e84f53206750a588c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77430726"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Перенапереживайте данные о состоянии автоматизации Azure в журналы Azure Monitor

Конфигурация состояния автоматизации Azure сохраняет данные о состоянии узлов в течение 30 дней.
Вы можете отправлять данные о состоянии узлов в рабочее пространство Log Analytics, если вы предпочитаете хранить эти данные в течение более длительного периода.
Сведения о состоянии соответствий узлов и отдельных ресурсов DSC в конфигурации узла можно просмотреть на портале Azure или с помощью PowerShell.
С журналами Azure Monitor вы можете:

- получать сведения о соответствии требованиям для управляемых узлов и отдельных ресурсов;
- активировать сообщение электронной почты или предупреждение (в зависимости от состояния соответствия);
- создавать сложные запросы к управляемым узлам;
- сопоставлять состояние соответствия в учетных записях службы автоматизации;
- визуализировать журнал соответствия узла с течением времени.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Предварительные требования

Для начала отправки отчетов о конфигурации состояния автоматизации в журналы Azure Monitor необходимо:

- Выпуск за ноябрь 2016 года или более поздний выпуск [Azure PowerShell](/powershell/azure/overview) (вер. 2.3.0).
- Учетная запись службы автоматизации Azure. Для получения дополнительной [An introduction to Azure Automation](automation-intro.md)информации см.
- Рабочее пространство журнала Analytics с предложением службы управления & автоматизации. Дополнительные сведения см. в статье [Начало работы с Log Analytics в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
- Как минимум один узел службы "Настройка состояния службы автоматизации Azure". Для получения дополнительной информации см. [Onboarding машины для управления Azure Automation State Configuration](automation-dsc-onboarding.md).
- Модуль [xDscDiagnostics,](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) версия 2.7.0.0 или больше. Для шагов установки [см.](./troubleshoot/desired-state-configuration.md)

## <a name="set-up-integration-with-azure-monitor-logs"></a>Настройка интеграции с журналами Azure Monitor

Чтобы начать импорт данных из DSC Azure Automation DSC в журналы Azure Monitor, выполните следующие шаги:

1. Войдите в свою учетную запись Azure в PowerShell. Смотрите [войти в систему с Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
1. Получите идентификатор ресурсов вашей учетной записи автоматизации, запустив следующий cmdlet PowerShell. Если у вас несколько учетных записей автоматизации, выберите идентификатор ресурса для учетной записи, которую вы хотите настроить.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Получите идентификатор ресурсов рабочего пространства Log Analytics, запустив следующий cmdlet PowerShell. Если у вас есть несколько рабочих мест, выберите идентификатор ресурса для рабочего пространства, которое вы хотите настроить.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Выполнить следующие значения PowerShell `<AutomationResourceId>` cmdlet, заменив и `<WorkspaceResourceId>` с *значениями ResourceId* от каждого из предыдущих шагов.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Если вы хотите прекратить импорт данных из конфигурации состояния автоматизации Azure в журналы Azure Monitor, запустите следующий cmdlet PowerShell.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Просмотр журналов State Configuration

После настройки интеграции с журналами Azure Monitor для данных состояния автоматизации можно просмотреть их, выбрав **журналы** в разделе **Мониторинг** в левом стеку страницы конфигурации состояния (DSC).

![Журналы](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

Панель **поиска журнала** открывается областью запроса, прикрытой для ресурса учетной записи Automation. Поиск в журналах Azure Monitor можно искать в журналах «Система состояния» для операций DSC. Записи для операций DSC хранятся в таблице AzureDiagnostics. Например, чтобы найти узлы, которые не соответствуют требованиям, введите следующий запрос.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```
Детали фильтрации:

* Фильтр на *DscNodeStatusData* для возврата операций для каждого узла конфигурации состояния.
* Фильтр на *DscResourceStatusDataData* для возврата операций для каждого ресурса DSC, вызванного в конфигурации узла, применяемой к этому ресурсу. 
* Фильтр на *DscResourceStatusData,* чтобы вернуть информацию об ошибках для любых ресурсов DSC, которые терпят неудачу.

Чтобы узнать больше о построении запросов журналов для поиска данных, смотрите [Обзор запросов журналов в Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Отправка сообщения электронной почты при сбое проверки соответствия State Configuration

Наши клиенты часто интересуются возможностью отправки электронного сообщения или SMS-сообщения при возникновении ошибки в конфигурации DSC.

Чтобы создать правило оповещения, начните с создания поиска журналов для записей отчета о состоянии конфигурации, которые должны вызывать оповещение. Щелкните кнопку **+ Новое правило генерации оповещений**, чтобы создать и настроить правило генерации оповещений.

1. Со страницы обзор рабочего пространства Log Analytics нажмите **«Журналы».**
1. Поищите по журналам свое оповещение при помощи следующего поискового запроса: `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Если вы настроили для рабочей области журналы из более чем одной учетной записи службы автоматизации или подписки, то можете группировать оповещения по подписке или учетной записи службы автоматизации. Выизуем название учетной записи Automation из поля ресурсов в поиске DscNodeStatusData.
1. Чтобы открыть экран **Создать правило**, щелкните **+ Новое правило генерации оповещений** в верхней части страницы. 

Для получения дополнительной информации о вариантах настройки оповещения [см.](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Поиск ресурсов DSC со сбоями по всем узлам

Одним из преимуществ использования журналов Azure Monitor является то, что вы можете искать неудавшихся проверок в узлах.
Чтобы найти все экземпляры ресурсов DSC, которые не сработали:

1. На странице обзор рабочего пространства Журнала Analytics нажмите **«Журналы».**
1. Поищите по журналам свое оповещение при помощи следующего поискового запроса: `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Просмотр состояния узла DSC за предыдущие периоды

Чтобы визуализировать историю состояния dSC-узла с течением времени, вы можете использовать этот запрос:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Этот запрос отображает диаграмму состояния узла с течением времени.

## <a name="azure-monitor-logs-records"></a>Записи журналов Azure Monitor

Диагностика Azure Automation создает две категории записей в журналах Azure Monitor:

* Данные о состоянии узлов (DscNodeStatusData)
* Данные о состоянии ресурсов (DscResourceStatusData)

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Свойство | Описание |
| --- | --- |
| TimeGenerated |Дата и время запуска проверки соответствия. |
| OperationName |DscNodeStatusData. |
| ResultType |Подтверждено ли соответствие узла. |
| NodeName_s |Имя управляемого узла. |
| NodeComplianceStatus_s |Подтверждено ли соответствие узла. |
| DscReportStatus |Успешно ли прошла проверка соответствия. |
| ConfigurationMode | Применение конфигурации к узлу. Возможны следующие значения: <ul><li>*ApplyOnly* — DSC применяет конфигурацию и не выполняет дальнейшие действия, пока новая конфигурация не будет отправлена в целевой узел или получена с сервера. После первоначального применения новой конфигурации DSC не проверяет наличие отклонений от ранее настроенного состояния. DSC пытается применить конфигурацию до тех пор, пока она не будет успешной до вступления в силу значения *ApplyOnly.* </li><li>*ApplyAndMonitor*: Это значение по умолчанию. LCM применяет любую новую конфигурацию. Если после начального применения новой конфигурации состояние целевого узла отклоняется от требуемого, DSC сообщает о расхождении в журналах. DSC пытается применить конфигурацию до тех пор, пока она не будет успешной до вступления в силу значения *ApplyAndMonitor.*</li><li>*ApplyAndAutoCorrect*: DSC применяет все новые конфигурации. Если после начального применения новой конфигурации состояние целевого узла отклоняется от требуемого, DSC сообщает о расхождении в журналах, а затем повторно применяет текущую конфигурацию.</li></ul> |
| HostName_s | Имя управляемого узла. |
| IPAddress | Адрес IPv4 управляемого узла. |
| Категория | DscNodeStatus. |
| Ресурс | Имя учетной записи службы автоматизации Azure. |
| Tenant_g | GUID, идентифицирующий клиента для вызывающего объекта. |
| NodeId_g |Идентификатор GUID для определения управляемого узла. |
| DscReportId_g |Идентификатор GUID для определения отчета. |
| LastSeenTime_t |Дата и время последнего просмотра отчета. |
| ReportStartTime_t |Дата и время начала отчета. |
| ReportEndTime_t |Дата и время завершения отчета. |
| NumberOfResources_d |Количество ресурсов DSC, которые вызывались в примененной к узлу конфигурации. |
| SourceSystem | Как журналы Azure Monitor собирали данные. Всегда "Azure" для диагностики Azure. |
| ResourceId |Идентификатор учетной записи Azure Automation. |
| ResultDescription | Описание для этой операции. |
| SubscriptionId | Идентификатор подписки Azure (GUID) для учетной записи Автоматизации. |
| ResourceGroup | Имя группы ресурсов для учетной записи службы автоматизации. |
| ResourceProvider | Microsoft. Автоматизации. |
| ResourceType | АВТОМАТИЗАЦИЯСЧЕТОВ. |
| CorrelationId |GUID является идентификатором корреляции отчета о соответствии. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Свойство | Описание |
| --- | --- |
| TimeGenerated |Дата и время запуска проверки соответствия. |
| OperationName |DscResourceStatusData|
| ResultType |Подтверждено ли соответствие ресурса. |
| NodeName_s |Имя управляемого узла. |
| Категория | DscNodeStatus. |
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
| SourceSystem | Как журналы Azure Monitor собирали данные. Всегда имеет значение *Azure* для системы диагностики Azure. |
| ResourceId |Указывает учетную запись службы автоматизации Azure. |
| ResultDescription | Описание для этой операции. |
| SubscriptionId | Идентификатор подписки Azure (GUID) для учетной записи Автоматизации. |
| ResourceGroup | Имя группы ресурсов для учетной записи службы автоматизации. |
| ResourceProvider | Microsoft. Автоматизации. |
| ResourceType | АВТОМАТИЗАЦИЯСЧЕТОВ. |
| CorrelationId |GUID является идентификатором корреляции отчета о соответствии. |

## <a name="summary"></a>Сводка

Отправив данные о конфигурации состояния автоматизации в журналы Azure Monitor, вы сможете лучше понять состояние узлов состояния автоматизации:

- настроить оповещения, уведомляющие вас о проблемах;
- с помощью пользовательских представлений и поисковых запросов визуализировать результаты модуля Runbook, состояние задания Runbook и другие связанные ключевые индикаторы или метрики.

Журналы Azure Monitor обеспечивают большую оперативную видимость данных автоматизированной конфигурации состояния и могут помочь быстрее устранить инциденты.

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения см. в статье с [обзором "Настройка состояния службы автоматизации Azure"](automation-dsc-overview.md).
- Чтобы приступить к работе со службой "Настройка состояния службы автоматизации Azure", см. сведения в [этой статье](automation-dsc-getting-started.md).
- Сведения о компилировании конфигураций DSC, которые затем можно назначить целевым узлам, см. в статье [Компилирование конфигураций в Azure Automation DSC](automation-dsc-compile.md).
- Справочник по командлетам PowerShell для службы "Настройка состояния службы автоматизации Azure" см. в [этой статье](/powershell/module/azurerm.automation/#automation).
- Сведения о ценах см. на странице [с ценами на службу "Настройка состояния службы автоматизации Azure"](https://azure.microsoft.com/pricing/details/automation/).
- Пример использования службы "Настройка состояния службы автоматизации Azure" и Chocolatey в конвейере непрерывного развертывания см. в [этой статье](automation-dsc-cd-chocolatey.md).
- Чтобы узнать больше о том, как создавать различные поисковые запросы и просматривать журналы конфигурации состояния автоматизации с помощью журналов Azure Monitor, смотрите [поиск в журналах Azure Monitor](../log-analytics/log-analytics-log-searches.md)
- Подробнее о журналах и источниках сбора данных Azure Monitor читайте [в обзоре журналов журналов Azure Monitor](../azure-monitor/platform/collect-azure-metrics-logs.md)
