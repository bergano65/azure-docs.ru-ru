---
title: Расписания в службе автоматизации Azure
description: Расписания службы автоматизации используются для планирования автоматического выполнения модулей Runbook в службе автоматизации Azure. Рассматривается создание расписания, которое позволяет автоматически запускать модуль Runbook однократно или несколько раз в определенное время, а также управление этим расписанием.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c4898ba62abdc42d95b77b9a77387bfe71fb4771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252666"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Создание расписания для Runbook в службе автоматизации Azure

Чтобы запускать модуль Runbook в определенное время с помощью расписания в службе автоматизации Azure, его необходимо привязать к одному или нескольким расписаниям. На портале Azure расписание для модулей runbook можно настроить для однократного, ежечасного или ежедневного выполнения. Можно также запланировать еженедельное и ежемесячное выполнение, а также выполнение в определенные дни недели или месяца. Один модуль Runbook может быть связан с несколькими расписаниями, и одно расписание может иметь несколько привязанных модулей Runbook.

> [!NOTE]
> В настоящее время расписания не поддерживают конфигурации Azure Automation DSC.

## <a name="powershell-cmdlets"></a>Командлеты PowerShell

Cmdlets в следующей таблице используются для создания и управления графиками с PowerShell в Azure Automation. Они входят в состав [модуля Azure PowerShell](/powershell/azure/overview).

| Командлеты | Описание |
|:--- |:--- |
| [Get-AzureRmAutomationПланклял](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Получает расписание. |
| [Новый-AzureRmAutomationРасписание](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Создает новое расписание. |
| [Удалить-AzureRmAutomationПлан](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Удаляет расписание. |
| [Set-AzureRmAutomationРасписани](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Задает свойства для существующего расписания. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Получает расписание модулей Runbook. |
| [Регистрация-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Связывает Runbook с расписанием. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Отменяет связь Runbook с расписанием. |

## <a name="creating-a-schedule"></a>Создание расписания

Вы можете создать новое расписание для runbooks на портале Azure или с помощью PowerShell.

> [!NOTE]
> При запуске нового запланированного задания служба автоматизации Azure использует модули последней версии в вашей учетной записи службы автоматизации.  Чтобы избежать влияния на модули Runbook и автоматизируемые ими процессы, необходимо сначала проверить все модули Runbook, которые имеют связанные расписания, используя учетную запись автоматизации, предназначенную для тестирования.  Это позволяет обеспечить правильную работу запланированных модулей runbook. В противном случае вы сможете устранить неполадки и применить все необходимые изменения, прежде чем переносить обновленную версию модуля runbook в рабочую среду.
> Учетная запись автоматизации не получает новые версии модулей автоматически, если их не обновить вручную, выбрав параметр [Обновить модули Azure](../automation-update-azure-modules.md) в области **Модули**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Создание нового расписания на портале Azure

1. На портале Azure из учетной записи автоматизации выберите **Расписание** под разделом **Общие ресурсы** слева.
2. Щелкните **Добавить расписание** в верхней части страницы.
3. В области **Новое расписание** введите **имя** и при желании **описание** нового расписания.
4. Выберите режим выполнения расписания: **Однократно** или **Повторение**. Если вы выбрали режим **Однократно**, то укажите **время начала** и нажмите кнопку **Создать**. Если вы выбрали режим **Повторение**, укажите **время начала** и **частоту повторений** для модуля Runbook: каждый **час**, **день**, **неделю** или **месяц**.
    1. Если вы выбираете **неделю,** вам предоставляется список дней недели на выбор. Выберите столько дней, сколько требуется. Первый запуск расписания будет происходить на первый день после времени начала. Например, чтобы выбрать расписание выходных, выбрать **субботу** и **воскресенье**.

       ![Настройка расписания повторяющихся выходных](../media/schedules/week-end-weekly-recurrence.png)

    2. Если вы выбираете **месяц,** вам дают различные варианты. Для **варианта Ежемесячные случаи,** выберите либо **Месяц дней** или **дней недели**. Если вы выбираете **месяц дней,** календарь отображается, что позволяет выбрать столько дней, сколько вы хотите. Если вы выберете дату, такую как 31-я, которая не происходит в текущем месяце, расписание не будет работать. Чтобы запустить его в последний день, выберите **Да** в поле **Последний день месяца**. Если вы выберете **дни недели**, появляется параметр **Повторять каждые**. Выберите **первый**, **второй**, **третий**, **четвертый**, или **последний**. Наконец, выберите день для повторения.

       ![Ежемесячный график на первый, пятнадцатый и последний день месяца](../media/schedules/monthly-first-fifteenth-last.png)

5. По завершении нажмите кнопку **Создать**.

### <a name="to-create-a-new-schedule-with-powershell"></a>Создать новое расписание с PowerShell

Используйте командлет [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) для создания расписаний. Укажите для расписания время начала и частоту выполнения. Ниже приведены следующие примеры, как создать множество различных сценариев расписания.

#### <a name="create-a-one-time-schedule"></a>Создание единого расписания

Следующие команды образца создают одновременный график.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Создание повторяюющегося расписания

Следующие команды образцов показывают, как создать повторяющийся график, который работает каждый день в 1:00 вечера в течение года.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Создание еженедельного расписания повторяющихся

Следующие команды образцов показывают, как создать еженедельное расписание, которое работает только в будние дни.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Создание еженедельного расписания на выходные

Следующие команды образцов показывают, как создать еженедельный график, который работает только по выходным.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Создайте повторяющийся график для первого, 15-го и последних дней месяца

Следующие команды образцов показывают, как создать повторяющийся график, который работает на 1-й, 15-й и последний день месяца.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzureRmAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Связывание расписания с модулем Runbook

Один модуль Runbook может быть связан с несколькими расписаниями, и одно расписание может иметь несколько привязанных модулей Runbook. Если Runbook имеет параметры, для них можно указать значения. Необходимо предоставить значения для всех обязательных параметров, а также значения для необязательных параметров. Значения применяются каждый раз при запуске модуля Runbook с помощью расписания. Один и тот же модуль Runbook можно привязать к другому расписанию и указать другие значения параметров.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Связывание расписания с модулем Runbook с помощью портала Azure

1. На портале Azure в учетной записи автоматизации выберите **Модули Runbook** в разделе **Автоматизация процессов** в левой части экрана.
2. Щелкните имя одного модуля Runbook для привязки к расписанию.
3. Если в настоящее время runbook не связан с расписанием, вам предлагается возможность создать новое расписание или ссылку на существующее расписание.
4. Если в runbook есть параметры, можно выбрать **опцию Modify run settings (Default:Azure)** и представлено панель **параметров,** где можно ввести информацию.

### <a name="to-link-a-schedule-to-a-runbook-with-powershell"></a>Связать расписание с книгой с PowerShell

Вы можете использовать командлет [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook), чтобы связать расписание. С помощью параметра "Параметры" можно указать значения параметров для модуля Runbook. Дополнительные сведения об указании значений параметров см. в статье [Запуск модуля Runbook в службе автоматизации Azure](../automation-starting-a-runbook.md).
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

* Создайте [веб-крюк](../automation-webhooks.md) для runbook и используйте [приложения Для логики Azure](../../logic-apps/logic-apps-overview.md) для вызова веб-крючка. Azure Logic Apps обеспечивает более мелкозернистуянную детализацию при определении расписания.

* Создайте четыре расписания, которые будут запускаться каждые 15 минут в течение часа. Этот сценарий позволяет модулю runbook запускаться каждые 15 минут с разными расписаниями.

## <a name="disabling-a-schedule"></a>Отключение расписания

Если отключить расписание, модуль runbook, связанный с ним, больше не будет запускаться по такому расписанию. Можно отключить расписание вручную или указать срок действия при создании периодических расписаний. Как только время истечения срока действия будет достигнуто, расписание отключается.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Отключение расписания на портале Azure

1. На портале Azure в учетной записи службы автоматизации выберите **Расписания** в разделе **Общие ресурсы** в левой части экрана.
2. Щелкните имя расписания, чтобы открыть область подробных сведений.
3. Задайте значение **Нет** для параметра **Включено**.

> [!NOTE]
> Если вы хотите отключить расписание, которое имеет время начала в прошлом, необходимо изменить дату начала на время в будущем, прежде чем сохранить его.

### <a name="to-disable-a-schedule-with-powershell"></a>Отключить расписание с помощью PowerShell

Вы можете использовать командлет [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule), чтобы изменить свойства имеющегося расписания. Чтобы отключить расписание, задайте значение **False** для параметра **IsEnabled**.

Приведенные ниже примеры команд демонстрируют, как отключить расписание для Runbook, используя командлет Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о том, как начать работу с модулями Runbook в службе автоматизации Azure, см. в статье [Запуск модуля Runbook в службе автоматизации Azure](../automation-starting-a-runbook.md).

