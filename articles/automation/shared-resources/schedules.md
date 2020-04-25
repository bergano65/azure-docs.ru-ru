---
title: Управление расписаниями в службе автоматизации Azure
description: Расписания службы автоматизации используются для планирования автоматического выполнения модулей Runbook в службе автоматизации Azure. Рассматривается создание расписания, которое позволяет автоматически запускать модуль Runbook однократно или несколько раз в определенное время, а также управление этим расписанием.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 17d46ddb738abc812ebfc458e25c745b84a29c2a
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82136606"
---
# <a name="manage-schedules-in-azure-automation"></a>Управление расписаниями в службе автоматизации Azure

Чтобы запускать модуль Runbook в определенное время с помощью расписания в службе автоматизации Azure, его необходимо привязать к одному или нескольким расписаниям. На портале Azure расписание для модулей runbook можно настроить для однократного, ежечасного или ежедневного выполнения. Можно также запланировать еженедельное и ежемесячное выполнение, а также выполнение в определенные дни недели или месяца. Один модуль Runbook может быть связан с несколькими расписаниями, и одно расписание может иметь несколько привязанных модулей Runbook.

> [!NOTE]
> В настоящее время расписания не поддерживают конфигурации Azure Automation DSC.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке AZ Module в гибридной рабочей роли Runbook см. в статье [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Для учетной записи службы автоматизации можно обновить модули до последней версии, используя [обновление модулей Azure PowerShell в службе автоматизации Azure](../automation-update-azure-modules.md).

## <a name="powershell-cmdlets"></a>Командлеты PowerShell

Командлеты, приведенные в следующей таблице, используются для создания расписаний и управления ими с помощью PowerShell в службе автоматизации Azure. Они входят в состав [модуля Azure PowerShell](/powershell/azure/overview).

| Командлеты | Описание |
|:--- |:--- |
| [Get-Азаутоматионсчедуле](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |Получает расписание. |
| [Get-Азаутоматионсчедуледрунбук](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |Получает расписание модулей Runbook. |
| [New-Азаутоматионсчедуле](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |Создает новое расписание. |
| [Register-Азаутоматионсчедуледрунбук](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |Связывает Runbook с расписанием. |
| [Remove-Азаутоматионсчедуле](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |Удаляет расписание. |
| [Set-Азаутоматионсчедуле](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |Задает свойства для существующего расписания. |
| [Отмена регистрации — Азаутоматионсчедуледрунбук](https://docs.microsoft.com/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |Отменяет связь Runbook с расписанием. |

## <a name="creating-a-schedule"></a>Создание расписания

Вы можете создать новое расписание для модулей Runbook в портал Azure или с помощью PowerShell.

> [!NOTE]
> При запуске нового запланированного задания служба автоматизации Azure использует модули последней версии в вашей учетной записи службы автоматизации.  Чтобы избежать влияния на модули Runbook и автоматизируемые ими процессы, необходимо сначала проверить все модули Runbook, которые имеют связанные расписания, используя учетную запись автоматизации, предназначенную для тестирования.  Это позволяет обеспечить правильную работу запланированных модулей runbook. В противном случае вы сможете устранить неполадки и применить все необходимые изменения, прежде чем переносить обновленную версию модуля runbook в рабочую среду.
> Учетная запись автоматизации не получает новые версии модулей автоматически, если их не обновить вручную, выбрав параметр [Обновить модули Azure](../automation-update-azure-modules.md) в области **Модули**.

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Создайте новое расписание на портал Azure

1. В портал Azure в учетной записи службы автоматизации выберите **расписания** в разделе **Общие ресурсы** слева.
2. Щелкните **Добавить расписание** в верхней части страницы.
3. На панели новое расписание введите имя и, при необходимости, описание нового расписания.
4. Выберите режим выполнения расписания: **Однократно** или **Повторение**. Если выбрать **один раз**, укажите время начала и нажмите кнопку **создать**. Если выбрано **повторяющееся**, укажите время начала. В поле **Повторять каждые**выберите частоту повторения модуля Runbook по часам, дню, неделе или месяцу.
    1. Если выбрать вариант **неделя**, то будут предложены дни недели для выбора. Выберите столько дней, сколько требуется. Первый запуск расписания будет происходить на первый день после времени начала. Например, чтобы выбрать расписание выходных дней, выберите Суббота и воскресенье. 
    
       ![Задание повторяющегося расписания для выходных дней](../media/schedules/week-end-weekly-recurrence.png)

    2. Если выбрать **месяц**, вам будут предложены разные варианты. Для параметра **ежемесячные повторения** выберите один из **месяцев** или **дней недели**. Если выбрать **месяц дней**, отобразится календарь, позволяющий выбрать любое количество дней. Если выбрать дату, например 31, которая не наблюдается в текущем месяце, расписание не будет запущено. Чтобы запустить его в последний день, выберите **Да** в поле **Последний день месяца**. Если вы выберете **дни недели**, появляется параметр **Повторять каждые**. Выберите **первый**, **второй**, **третий**, **четвертый**, или **последний**. Наконец, выберите день для повторения.

       ![Ежемесячное расписание на первый, Пятнадцатый и последний день месяца](../media/schedules/monthly-first-fifteenth-last.png)

5. По завершении нажмите кнопку **Создать**.

### <a name="create-a-new-schedule-with-powershell"></a>Создание нового расписания с помощью PowerShell

Используйте командлет [New-азаутоматионсчедуле](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) для создания расписаний. Укажите для расписания время начала и частоту выполнения. В следующих примерах показано, как создать множество различных сценариев расписания.

#### <a name="create-a-one-time-schedule"></a>Создание одноразового расписания

Следующие примеры команд создают расписание однократно.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Создание повторяющегося расписания

В следующем примере показано, как создать повторяющееся расписание, которое выполняется каждый день в 1:12:00 в течение года.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Создание еженедельного повторяющегося расписания

В следующем примере показано, как создать еженедельное расписание, выполняемое только по рабочим дням.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Создание еженедельного повторяющегося расписания для выходных дней

В приведенных ниже примерах команд показано, как создать еженедельное расписание, выполняемое только в выходные дни.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Создание повторяющегося расписания для первых, 15 и последних дней месяца

В следующем примере показано, как создать повторяющееся расписание, которое выполняется в первый, 15-й и последний день месяца.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Связывание расписания с модулем Runbook

Один модуль Runbook может быть связан с несколькими расписаниями, и одно расписание может иметь несколько привязанных модулей Runbook. Если Runbook имеет параметры, для них можно указать значения. Необходимо предоставить значения для всех обязательных параметров, а также значения для необязательных параметров. Значения применяются каждый раз при запуске модуля Runbook с помощью расписания. Один и тот же модуль Runbook можно привязать к другому расписанию и указать другие значения параметров.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Связывание расписания с модулем Runbook с портал Azure

1. В портал Azure в учетной записи службы автоматизации в разделе **Автоматизация процессов**выберите **модули Runbook** .
2. Щелкните имя одного модуля Runbook для привязки к расписанию.
3. Если Runbook в настоящее время не связан с расписанием, вы можете создать новое расписание или связать с существующим расписанием.
4. Если у модуля Runbook есть параметры, можно выбрать параметр **изменить параметры запуска (по умолчанию: Azure)** и область параметров. Здесь можно ввести сведения о параметрах.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Связывание расписания с модулем Runbook с помощью PowerShell

Чтобы связать расписание, используйте командлет [Register-азаутоматионсчедуледрунбук](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) . С помощью параметра "Параметры" можно указать значения параметров для модуля Runbook. Дополнительные сведения об указании значений параметров см. в статье [Запуск модуля Runbook в службе автоматизации Azure](../automation-starting-a-runbook.md).
В следующем примере показано, как связать расписание с модулем Runbook с помощью командлета Azure Resource Manager с параметрами.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-to-run-more-frequently"></a>Планирование выполнения модулей Runbook чаще всего

Самая большая частота расписания в службе автоматизации Azure, которую можно настроить, составляет один час. Если требуется, чтобы расписания выполнялись чаще, есть два варианта:

* Создайте [веб-перехватчик](../automation-webhooks.md) для модуля Runbook и используйте [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) для вызова веб-перехватчика. При определении расписания Azure Logic Apps обеспечивает более детализированную детализацию.

* Создайте четыре расписания, которые будут запускаться каждые 15 минут в течение часа. Этот сценарий позволяет модулю runbook запускаться каждые 15 минут с разными расписаниями.

## <a name="disabling-a-schedule"></a>Отключение расписания

Если отключить расписание, модуль runbook, связанный с ним, больше не будет запускаться по такому расписанию. Можно отключить расписание вручную или указать срок действия при создании периодических расписаний. По истечении срока действия расписание отключается.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Отключение расписания от портал Azure

1. В учетной записи службы автоматизации выберите **расписания** в разделе **Общие ресурсы**.
2. Щелкните имя расписания, чтобы открыть область подробных сведений.
3. Задайте значение **Нет** для параметра **Включено**.

> [!NOTE]
> Если вы хотите отключить расписание с временем начала в прошлом, необходимо изменить дату начала на время в будущем перед сохранением.

### <a name="disable-a-schedule-with-powershell"></a>Отключение расписания с помощью PowerShell

Используйте командлет [Set-азаутоматионсчедуле](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) , чтобы изменить свойства существующего расписания. Чтобы отключить расписание, укажите значение false для `IsEnabled` параметра.

В следующем примере показано, как отключить расписание для модуля Runbook с помощью командлета Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="removing-a-schedule"></a>Удаление расписания

Когда вы будете готовы удалить расписания, можно либо использовать портал Azure, либо `Remove-AzureRmAutomationSchedule` командлет. Помните, что можно удалить только расписание, которое было отключено, как описано в предыдущем разделе.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Удаление расписания с помощью портал Azure

1. В учетной записи службы автоматизации выберите **расписания** в разделе **Общие ресурсы**.
2. Щелкните имя расписания, чтобы открыть область подробных сведений.
3. Щелкните **Удалить**.

### <a name="remove-a-schedule-with-powershell"></a>Удаление расписания с помощью PowerShell

Для удаления существующего расписания можно использовать командлет [Remove-азаутоматионсчедуле](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) . 

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Следующие шаги

* Чтобы приступить к работе с модулями Runbook в службе автоматизации Azure, см. статью [Запуск модуля Runbook в службе автоматизации Azure](../automation-starting-a-runbook.md).