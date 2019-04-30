---
title: Пересылать настройки состояния службы автоматизации Azure, данные отчетов журналов Azure Monitor
description: В этой статье описана процедура отправки Desired State Configuration (DSC) данные из настройки состояния службы автоматизации Azure в Azure Monitor журналы отчетов для получения дополнительных сведений и управления.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0dad74f75fd7b73e7dab0b2dddbdfda193d5b2ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61073945"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Пересылать настройки состояния службы автоматизации Azure, данные отчетов журналов Azure Monitor

Настройки состояния службы автоматизации Azure хранит данные о состоянии узла в течение 30 дней.
В рабочую область Log Analytics можно отправить данные о состоянии узла, если вы хотите сохранить эти данные на более длительное время.
Сведения о состоянии соответствий узлов и отдельных ресурсов DSC в конфигурации узла можно просмотреть на портале Azure или с помощью PowerShell.
С помощью журналов Azure Monitor вы можете:

- получать сведения о соответствии требованиям для управляемых узлов и отдельных ресурсов;
- активировать сообщение электронной почты или предупреждение (в зависимости от состояния соответствия);
- создавать сложные запросы к управляемым узлам;
- сопоставлять состояние соответствия в учетных записях службы автоматизации;
- визуализировать журнал соответствия узла с течением времени.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Технические условия

Чтобы начать отправку отчетов настройки состояния службы автоматизации в журналы Azure Monitor, вам потребуется:

- Выпуск за ноябрь 2016 года или более поздний выпуск [Azure PowerShell](/powershell/azure/overview) (вер. 2.3.0).
- Учетная запись службы автоматизации Azure. Дополнительные сведения см. в статье [Приступая к работе со службой автоматизации Azure](automation-offering-get-started.md)
- Рабочая область Log Analytics с предложением службы **Автоматизация и управление**. Дополнительные сведения см. в разделе [приступить к работе с журналами Azure Monitor](../log-analytics/log-analytics-get-started.md).
- Как минимум один узел службы "Настройка состояния службы автоматизации Azure". Дополнительные сведения см. в статье [Подключение компьютеров для управления с помощью Azure Automation DSC](automation-dsc-onboarding.md).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Настройка интеграции журналов Azure Monitor

Чтобы начать импорт данных из Azure Automation DSC в журналы Azure Monitor, выполните следующие действия:

1. Войдите в свою учетную запись Azure в PowerShell. См. статью [Вход с помощью Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
1. Получите идентификатор _ResourceId_ для учетной записи службы автоматизации, выполнив следующую команду PowerShell (при наличии нескольких учетных записей службы автоматизации выберите _ResourceID_ для учетной записи, которую требуется настроить):

   ```powershell
   # Find the ResourceId for the Automation Account
   Get-AzureRmResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Получите идентификатор _ResourceId_ для рабочей области Log Analytics, выполнив следующую команду PowerShell (при наличии нескольких рабочих областей выберите _ResourceID_ для рабочей области, которую требуется настроить):

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzureRmResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Выполните следующую команду PowerShell, заменив `<AutomationResourceId>` и `<WorkspaceResourceId>` значениями _ResourceId_ из предыдущих шагов:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Categories 'DscNodeStatus'
   ```

Если вы хотите остановить импорт данных из настройки состояния службы автоматизации Azure в Azure Monitor журналы, выполните следующую команду PowerShell:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Categories 'DscNodeStatus'
```

## <a name="view-the-state-configuration-logs"></a>Просмотр журналов State Configuration

После настройки интеграции с журналами Azure Monitor для данных настройки состояния службы автоматизации, **поиска по журналам** кнопки будут отображаться на **узлов DSC** колонке учетной записи службы автоматизации. Нажмите кнопку **Поиск по журналам**, чтобы просмотреть журналы для данных узла DSC.

![Кнопка поиска по журналам](media/automation-dsc-diagnostics/log-search-button.png)

Откроется колонка **Поиск по журналам**, и для каждого узла State Configuration отобразятся данные операции **DscNodeStatusData**. Для каждого [ресурса DSC](/powershell/dsc/resources), вызванного в конфигурации такого узла, отобразятся данные операции **DscResourceStatusData**.

Для операции **DscResourceStatusData** отображаются сведения об ошибках при сбое в работе любых ресурсов DSC.

Щелкните каждую операцию в списке, чтобы просмотреть сведения о ней.

Можно также просмотреть журналы, выполнив поиск в журналах Azure Monitor.
См. статью [Поиск данных по журналам](../log-analytics/log-analytics-log-searches.md).
Введите следующий запрос, чтобы найти журналы State Configuration: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus'`.

Можно сузить запрос, использовав имя операции. Например: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus' OperationName='DscNodeStatusData'`

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Отправка сообщения электронной почты при сбое проверки соответствия State Configuration

Наши клиенты часто интересуются возможностью отправки электронного сообщения или SMS-сообщения при возникновении ошибки в конфигурации DSC.

Чтобы создать правило генерации оповещений, начните с создания поиска журналов для записей отчетов State Configuration, которые должны вызывать оповещение. Щелкните кнопку **+ Новое правило генерации оповещений**, чтобы создать и настроить правило генерации оповещений.

1. На странице обзора рабочей области Log Analytics, щелкните **журналы**.
1. Поищите по журналам свое оповещение при помощи следующего поискового запроса: `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Если вы настроили для рабочей области журналы из более чем одной учетной записи службы автоматизации или подписки, то можете группировать оповещения по подписке или учетной записи службы автоматизации.  
   Имя учетной записи службы автоматизации можно получить из поля "Ресурс" для поискового запроса DscNodeStatusData.  
1. Чтобы открыть экран **Создать правило**, щелкните **+ Новое правило генерации оповещений** в верхней части страницы. Дополнительные сведения о параметрах для настройки оповещения см. в разделе [создать правило генерации оповещений](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Поиск ресурсов DSC со сбоями по всем узлам

Одно из преимуществ с помощью журналов Azure Monitor является возможность поиска проверок со сбоями по узлам.
Чтобы найти все экземпляры ресурсов DSC со сбоями, выполните следующие действия:

1. На странице обзора рабочей области Log Analytics, щелкните **журналы**.
1. Поищите по журналам свое оповещение при помощи следующего поискового запроса: `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Просмотр состояния узла DSC за предыдущие периоды

Наконец, вам может понадобиться визуализировать изменение состояния узла DSC с течением времени.  
Для поиска состояния узла DSC за предыдущие периоды можно использовать следующий запрос:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  

При этом отобразится диаграмма состояния узла с течением времени.

## <a name="azure-monitor-logs-records"></a>Azure Monitor регистрирует записи

При диагностике из службы автоматизации Azure создаются записи двух категорий в журналах Azure Monitor.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Свойство | Описание |
| --- | --- |
| TimeGenerated |Дата и время запуска проверки соответствия. |
| OperationName |DscNodeStatusData |
| ResultType |Подтверждено ли соответствие узла. |
| NodeName_s |Имя управляемого узла. |
| NodeComplianceStatus_s |Подтверждено ли соответствие узла. |
| DscReportStatus |Успешно ли прошла проверка соответствия. |
| ConfigurationMode | Применение конфигурации к узлу. Возможные значения: __ApplyOnly__, __ApplyandMonitior__ и __ApplyandAutoCorrect__. <ul><li>__ApplyOnly__. DSC применяет конфигурацию и не выполняет дальнейшие действия, пока новая конфигурация не будет отправлена в целевой узел или получена с сервера. После первоначального применения новой конфигурации DSC не проверяет наличие отклонений от ранее настроенного состояния. Перед вступлением в силу __ApplyOnly__ DSC пытается применить конфигурацию, пока это не будет успешно реализовано. </li><li> __ApplyAndMonitor__. Это значение по умолчанию. LCM применяет любую новую конфигурацию. Если после начального применения новой конфигурации состояние целевого узла отклоняется от требуемого, DSC сообщает о расхождении в журналах. Перед вступлением в силу __ApplyAndMonitor__ DSC пытается применить конфигурацию, пока это не будет успешно реализовано.</li><li>__ApplyAndAutoCorrect__. DSC применяет любые новые конфигурации. Если после начального применения новой конфигурации состояние целевого узла отклоняется от требуемого, DSC сообщает о расхождении в журналах, а затем повторно применяет текущую конфигурацию.</li></ul> |
| HostName_s | Имя управляемого узла. |
| IPAddress | Адрес IPv4 управляемого узла. |
| Category | DscNodeStatus |
| Resource | Имя учетной записи службы автоматизации Azure. |
| Tenant_g | GUID, идентифицирующий клиента для вызывающего объекта. |
| NodeId_g |Идентификатор GUID для определения управляемого узла. |
| DscReportId_g |Идентификатор GUID для определения отчета. |
| LastSeenTime_t |Дата и время последнего просмотра отчета. |
| ReportStartTime_t |Дата и время начала отчета. |
| ReportEndTime_t |Дата и время завершения отчета. |
| NumberOfResources_d |Количество ресурсов DSC, которые вызывались в примененной к узлу конфигурации. |
| SourceSystem | Как Azure Monitor журналы сбора данных. Всегда имеет значение *Azure* для системы диагностики Azure. |
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
| Category | DscNodeStatus |
| Resource | Имя учетной записи службы автоматизации Azure. |
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
| SourceSystem | Как Azure Monitor журналы сбора данных. Всегда имеет значение *Azure* для системы диагностики Azure. |
| ResourceId |Указывает учетную запись службы автоматизации Azure. |
| ResultDescription | Описание для этой операции. |
| SubscriptionId | Идентификатор подписки Azure (GUID) для учетной записи службы автоматизации. |
| ResourceGroup | Имя группы ресурсов для учетной записи службы автоматизации. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |Идентификатор GUID для корреляции отчета о соответствии. |

## <a name="summary"></a>Сводка

Отправляя данные конфигурации состояния службы автоматизации в Azure Monitor журналы, вы можете получить лучше понять состояние узлов путем настройки состояния службы автоматизации:

- настроить оповещения, уведомляющие вас о проблемах;
- с помощью пользовательских представлений и поисковых запросов визуализировать результаты модуля Runbook, состояние задания Runbook и другие связанные ключевые индикаторы или метрики.  

Журналы Azure Monitor предоставляет больший оперативный контроль над данные конфигурации состояния службы автоматизации и может помочь быстрее устранять инциденты.

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения см. в статье с [обзором "Настройка состояния службы автоматизации Azure"](automation-dsc-overview.md).
- Чтобы приступить к работе со службой "Настройка состояния службы автоматизации Azure", см. сведения в [этой статье](automation-dsc-getting-started.md).
- Сведения о компилировании конфигураций DSC, которые затем можно назначить целевым узлам, см. в статье [Компилирование конфигураций в Azure Automation DSC](automation-dsc-compile.md).
- Справочник по командлетам PowerShell для службы "Настройка состояния службы автоматизации Azure" см. в [этой статье](/powershell/module/azurerm.automation/#automation).
- Сведения о ценах см. на странице [с ценами на службу "Настройка состояния службы автоматизации Azure"](https://azure.microsoft.com/pricing/details/automation/).
- Пример использования службы "Настройка состояния службы автоматизации Azure" и Chocolatey в конвейере непрерывного развертывания см. в [этой статье](automation-dsc-cd-chocolatey.md).
- Дополнительные сведения о том, как создавать различные поисковые запросы и просматривать журналы настройки состояния службы автоматизации с помощью журналов Azure Monitor, см. в разделе [при поиске по журналам в журналах Azure Monitor](../log-analytics/log-analytics-log-searches.md)
- Дополнительные сведения о журналах Azure Monitor и источниках собираемых данных см. в разделе [Обзор записывает данные в хранилище Azure, сбор в Azure Monitor](../azure-monitor/platform/collect-azure-metrics-logs.md)
