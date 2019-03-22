---
title: Расписания в службе автоматизации Azure
description: Расписания службы автоматизации используются для планирования автоматического выполнения модулей Runbook в службе автоматизации Azure. Рассматривается создание расписания, которое позволяет автоматически запускать модуль Runbook однократно или несколько раз в определенное время, а также управление этим расписанием.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 09/18/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d6677733339babf45e7351e10d6de1c002a6ea93
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436516"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Создание расписания для Runbook в службе автоматизации Azure

Чтобы запускать модуль Runbook в определенное время с помощью расписания в службе автоматизации Azure, его необходимо привязать к одному или нескольким расписаниям. На портале Azure расписание для модулей runbook можно настроить для однократного, ежечасного или ежедневного выполнения. Можно также запланировать еженедельное и ежемесячное выполнение, а также выполнение в определенные дни недели или месяца. Один модуль Runbook может быть связан с несколькими расписаниями, и одно расписание может иметь несколько привязанных модулей Runbook.

> [!NOTE]
> В настоящее время расписания не поддерживают конфигурации Azure Automation DSC.

## <a name="windows-powershell-cmdlets"></a>Командлеты Windows PowerShell

Командлеты, представленные в следующей таблице, используются для создания расписаний и управления ими с помощью Windows PowerShell в службе автоматизации Azure. Они входят в состав [модуля Azure PowerShell](/powershell/azure/overview).

| Командлеты | ОПИСАНИЕ |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Получает расписание. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Создает новое расписание. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Удаляет расписание. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Задает свойства для существующего расписания. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Получает расписание модулей Runbook. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Связывает Runbook с расписанием. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Отменяет связь Runbook с расписанием. |

## <a name="creating-a-schedule"></a>Создание расписания

Новое расписание для модулей Runbook вы можете создать на портале Azure или с помощью Windows PowerShell.

> [!NOTE]
> При запуске нового запланированного задания служба автоматизации Azure использует модули последней версии в вашей учетной записи службы автоматизации.  Чтобы избежать влияния на модули Runbook и автоматизируемые ими процессы, необходимо сначала проверить все модули Runbook, которые имеют связанные расписания, используя учетную запись автоматизации, предназначенную для тестирования.  Это позволяет обеспечить правильную работу запланированных модулей runbook. В противном случае вы сможете устранить неполадки и применить все необходимые изменения, прежде чем переносить обновленную версию модуля runbook в рабочую среду.
> Учетная запись автоматизации не получает новые версии модулей автоматически, если их не обновить вручную, выбрав параметр [Обновить модули Azure](automation-update-azure-modules.md) в области **Модули**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Создание нового расписания на портале Azure

1. На портале Azure в учетной записи автоматизации выберите **Расписания** в разделе **Общие ресурсы** в левой части экрана.
1. Щелкните **Добавить расписание** в верхней части страницы.
1. В области **Новое расписание** введите **имя** и при желании **описание** нового расписания.
1. Выберите режим выполнения расписания: **Однократно** или **Повторение**. Если вы выбрали режим **Однократно**, то укажите **время начала** и нажмите кнопку **Создать**. Если вы выбрали режим **Повторение**, укажите **время начала** и **частоту повторений** для модуля Runbook: каждый **час**, **день**, **неделю** или **месяц**.
    1. При выборе **недели** отобразится список дней недели на выбор. Выберите столько дней, сколько требуется. Первый запуск расписания будет происходить на первый день после времени начала.
    2. При выборе **месяца** вы получаете различные параметры. Для **ежемесячные повторы** , либо выберите **дни месяца** или **дни недели**. Если вы выберете **дни месяца**, отображается календарь, где можно выбрать столько дней, сколько требуется. Если дата, например 31, не наступает в текущем месяце, расписание не будет выполняться. Чтобы запустить его в последний день, выберите **Да** в поле **Последний день месяца**. Если вы выберете **дни недели**, появляется параметр **Повторять каждые**. Выберите **первый**, **второй**, **третий**, **четвертый**, или **последний**. Наконец, выберите день для повторения.
1. По завершении нажмите кнопку **Создать**.

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Создание расписания с помощью Windows PowerShell

Используйте командлет [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) для создания расписаний. Укажите для расписания время начала и частоту выполнения.

Приведенные ниже примеры команд демонстрируют, как с помощью командлета Azure Resource Manager создать расписание для запуска модуля в 15-й и 30-й день каждого месяца.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
$scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
-DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"
```

## <a name="linking-a-schedule-to-a-runbook"></a>Связывание расписания с модулем Runbook

Один модуль Runbook может быть связан с несколькими расписаниями, и одно расписание может иметь несколько привязанных модулей Runbook. Если Runbook имеет параметры, для них можно указать значения. Необходимо предоставить значения для всех обязательных параметров, а также значения для необязательных параметров. Значения применяются каждый раз при запуске модуля Runbook с помощью расписания. Один и тот же модуль Runbook можно привязать к другому расписанию и указать другие значения параметров.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Связывание расписания с модулем Runbook с помощью портала Azure

1. На портале Azure в учетной записи автоматизации выберите **Модули Runbook** в разделе **Автоматизация процессов** в левой части экрана.
2. Щелкните имя одного модуля Runbook для привязки к расписанию.
3. Если модуль Runbook сейчас не связан с расписанием, вам будет предложено создать новое расписание или связать модуль с существующим расписанием.
4. Если модуль Runbook имеет параметры, следует выбрать действие **Изменить параметры запуска (по умолчанию: Azure)**, чтобы открыть область **Параметры**, где вы сможете ввести нужные данные.

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Связывание расписания с модулем Runbook с помощью Windows PowerShell

Вы можете использовать командлет [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook), чтобы связать расписание. С помощью параметра "Параметры" можно указать значения параметров для модуля Runbook. Дополнительные сведения об указании значений параметров см. в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).
Приведенные ниже примеры команд демонстрируют, как связать расписание с модулем Runbook, используя командлет управления службами Azure с параметрами.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Частое планирование модулей Runbook

Самая большая частота расписания в службе автоматизации Azure, которую можно настроить, составляет один час. Если требуется, чтобы расписания выполнялись чаще, есть два варианта:

* Создайте [веб-перехватчик](automation-webhooks.md) для модуля runbook и используйте [планировщик Azure](../scheduler/scheduler-get-started-portal.md) для вызова веб-перехватчика. Планировщик Azure обеспечивает большую детализацию при определении расписания.

* Создайте четыре расписания, которые будут запускаться каждые 15 минут в течение часа. Этот сценарий позволяет модулю runbook запускаться каждые 15 минут с разными расписаниями.

## <a name="disabling-a-schedule"></a>Отключение расписания

Если отключить расписание, модуль runbook, связанный с ним, больше не будет запускаться по такому расписанию. Можно отключить расписание вручную или указать срок действия при создании периодических расписаний. Когда срок действия истекает, расписание отключается.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Отключение расписания на портале Azure

1. На портале Azure в учетной записи службы автоматизации выберите **Расписания** в разделе **Общие ресурсы** в левой части экрана.
1. Щелкните имя расписания, чтобы открыть область подробных сведений.
1. Задайте значение **Нет** для параметра **Включено**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Отключение расписания с помощью Windows PowerShell

Вы можете использовать командлет [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule), чтобы изменить свойства имеющегося расписания. Чтобы отключить расписание, задайте значение **False** для параметра **IsEnabled**.

Приведенные ниже примеры команд демонстрируют, как отключить расписание для Runbook, используя командлет Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о том, как начать работу с модулями Runbook в службе автоматизации Azure, см. в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).

