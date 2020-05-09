---
title: Управление расписаниями в службе автоматизации Azure
description: Узнайте, как создать расписание в службе автоматизации Azure и управлять им, чтобы можно было автоматически запускать модуль Runbook в определенное время или по повторяющемуся расписанию.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4cd6d4236b95a17f404df13e8b50daf989cf6072
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652110"
---
# <a name="manage-schedules-in-azure-automation"></a>Управление расписаниями в службе автоматизации Azure

Чтобы запускать модуль Runbook в определенное время с помощью расписания в службе автоматизации Azure, его необходимо привязать к одному или нескольким расписаниям. Расписание можно настроить так, чтобы оно выполнялось один раз или регулярное или ежедневное расписание для модулей Runbook в портал Azure. Можно также запланировать еженедельное и ежемесячное выполнение, а также выполнение в определенные дни недели или месяца. Один модуль Runbook может быть связан с несколькими расписаниями, и одно расписание может иметь несколько привязанных модулей Runbook.

> [!NOTE]
> В настоящее время расписания не поддерживают конфигурации Automation DSC Azure.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке AZ Module в гибридной рабочей роли Runbook см. в статье [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Для учетной записи службы автоматизации модули можно обновить до последней версии, используя [обновление модулей Azure PowerShell в службе автоматизации Azure](../automation-update-azure-modules.md).

## <a name="powershell-cmdlets-used-to-access-schedules"></a>Командлеты PowerShell, используемые для доступа к расписаниям

Командлеты, приведенные в следующей таблице, создают расписания автоматизации и управляют ими с помощью PowerShell. Они поставляются как часть [модулей AZ](modules.md#az-modules). 

| Командлеты | Описание |
|:--- |:--- |
| [Get-Азаутоматионсчедуле](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |Получает расписание. |
| [Get-Азаутоматионсчедуледрунбук](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |Получает расписание модулей Runbook. |
| [New-Азаутоматионсчедуле](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |Создает новое расписание. |
| [Register-Азаутоматионсчедуледрунбук](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |Связывает Runbook с расписанием. |
| [Remove-Азаутоматионсчедуле](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |Удаляет расписание. |
| [Set-Азаутоматионсчедуле](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |Задает свойства для существующего расписания. |
| [Отмена регистрации — Азаутоматионсчедуледрунбук](https://docs.microsoft.com/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |Отменяет связь Runbook с расписанием. |

## <a name="create-a-schedule"></a>Создание расписания

Вы можете создать новое расписание для модулей Runbook в портал Azure или с помощью PowerShell. Чтобы не влиять на модули Runbook и процессы, которые они автоматизируют, сначала необходимо протестировать все модули Runbook, имеющие связанные расписания, с учетной записью службы автоматизации, выделенной для тестирования. Тест проверяет, что запланированные модули Runbook продолжают работать правильно. Если вы видите проблему, вы можете устранить неполадки и применить все необходимые изменения перед переносом обновленной версии Runbook в рабочую среду.

> [!NOTE]
> Ваша учетная запись службы автоматизации не будет автоматически получать новые версии модулей, если вы не обновили их вручную, выбрав параметр [обновить модули Azure](../automation-update-azure-modules.md) из **модулей**. При запуске нового запланированного задания служба автоматизации Azure использует модули последней версии в вашей учетной записи службы автоматизации. 

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Создайте новое расписание на портал Azure

1. На портале Azure в учетной записи службы автоматизации выберите **Расписания** в разделе **Общие ресурсы** в левой части экрана.
1. Выберите **Добавить расписание** в верхней части страницы.
1. На панели **новое расписание** введите имя и при необходимости введите описание нового расписания.
1. Выберите **один или несколько раз,** чтобы расписание выполнялось один раз или в **повторяющемся**расписании. Если выбрать **один раз**, укажите время начала и нажмите кнопку **создать**. Если выбрано **повторяющееся**, укажите время начала. В поле **Повторять каждые**выберите частоту повторения модуля Runbook. Выберите по часам, дню, неделе или месяцу.
    1. Если выбрать вариант **неделя**, то будут предложены дни недели для выбора. Выберите столько дней, сколько требуется. Первый запуск расписания будет происходить на первый день после времени начала. Например, чтобы выбрать расписание выходных дней, выберите Суббота и воскресенье.
    
       ![Задание повторяющегося расписания для выходных дней](../media/schedules/week-end-weekly-recurrence.png)

    2. Если выбрать **месяц**, вам будут предложены разные варианты. Для параметра **ежемесячные повторения** выберите один из **месяцев** или **дней недели**. Если выбрать **месяц дней**, появится календарь, в котором можно будет выбрать любое количество дней. Если выбрать дату, например 31, которая не наблюдается в текущем месяце, расписание не будет запущено. Если вы хотите, чтобы расписание выполнялось в последний день, выберите **Да** в разделе **запускать в последний день месяца**. Если выбрать **дни недели**, появится параметр **повторять все** . Выберите **первый**, **второй**, **третий**, **четвертый**, или **последний**. Наконец, выберите день для повторения.

       ![Ежемесячное расписание на первый, Пятнадцатый и последний день месяца](../media/schedules/monthly-first-fifteenth-last.png)

1. По завершении нажмите кнопку **создать**.

### <a name="create-a-new-schedule-with-powershell"></a>Создание нового расписания с помощью PowerShell

Используйте командлет [New-азаутоматионсчедуле](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) для создания расписаний. Укажите для расписания время начала и частоту выполнения. В следующих примерах показано, как создать множество различных сценариев расписания.

#### <a name="create-a-one-time-schedule"></a>Создание одноразового расписания

В следующем примере создается одноразовое расписание.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Создание повторяющегося расписания

В следующем примере показано, как создать повторяющееся расписание, которое выполняется каждый день в 1:00 PM в течение года.

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

В следующем примере показано, как создать еженедельное расписание, выполняемое только в выходные дни.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-the-first-fifteenth-and-last-days-of-the-month"></a>Создание повторяющегося расписания для первого, пятнадцатого и последнего дней месяца

В следующем примере показано, как создать повторяющееся расписание, выполняемое в первый, Пятнадцатый и последний день месяца.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="link-a-schedule-to-a-runbook"></a>Связывание расписания с модулем Runbook

Один модуль Runbook может быть связан с несколькими расписаниями, и одно расписание может иметь несколько привязанных модулей Runbook. Если у модуля Runbook есть параметры, можно указать для них значения. Необходимо указать значения для всех обязательных параметров. Кроме того, можно указать значения для любых необязательных параметров. Значения применяются каждый раз при запуске модуля Runbook с помощью расписания. Один и тот же модуль Runbook можно привязать к другому расписанию и указать другие значения параметров.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Связывание расписания с модулем Runbook с портал Azure

1. В портал Azure в учетной записи службы автоматизации в разделе **Автоматизация процессов**выберите **модули Runbook** .
1. Выберите имя модуля Runbook для расписания.
1. Если Runbook в настоящее время не связан с расписанием, вы можете создать новое расписание или связать с существующим расписанием.
1. Если у модуля Runbook есть параметры, можно выбрать параметр **изменить параметры запуска (по умолчанию: Azure)** и область **Параметры** . Здесь можно ввести сведения о параметрах.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Связывание расписания с модулем Runbook с помощью PowerShell

Чтобы связать расписание, используйте командлет [Register-азаутоматионсчедуледрунбук](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) . С помощью параметра "Параметры" можно указать значения параметров для модуля Runbook. Дополнительные сведения об указании значений параметров см. в разделе [Запуск модуля Runbook в службе автоматизации Azure](../automation-starting-a-runbook.md).
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

## <a name="schedule-runbooks-to-run-more-frequently"></a>Планирование выполнения модулей Runbook чаще всего

Наиболее частый интервал, для которого можно настроить расписание в службе автоматизации Azure, составляет один час. Если требуется, чтобы расписания выполнялись чаще, чем это, существует два варианта:

* Создайте [веб-перехватчик](../automation-webhooks.md) для модуля Runbook и используйте [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) для вызова веб-перехватчика. Azure Logic Apps обеспечивает более детализированную детализацию для определения расписания.

* Создайте четыре расписания, которые запускаются в течение 15 минут и выполняются один раз в час. Этот сценарий позволяет модулю runbook запускаться каждые 15 минут с разными расписаниями.

## <a name="disable-a-schedule"></a>Отключение расписания

Если отключить расписание, модуль runbook, связанный с ним, больше не будет запускаться по такому расписанию. Можно отключить расписание вручную или указать срок действия при создании периодических расписаний. Когда срок действия истекает, расписание отключается.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Отключение расписания от портал Azure

1. В учетной записи службы автоматизации выберите **расписания** в разделе **Общие ресурсы**.
1. Выберите имя расписания, чтобы открыть область сведений.
1. Задайте значение **Нет** для параметра **Включено**.

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

## <a name="remove-a-schedule"></a>Удаление расписания

Когда вы будете готовы удалить расписания, можно использовать портал Azure или PowerShell. Помните, что можно удалить только расписание, которое было отключено, как описано в предыдущем разделе.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Удаление расписания с помощью портал Azure

1. В учетной записи службы автоматизации выберите **расписания** в разделе **Общие ресурсы**.
2. Щелкните имя расписания, чтобы открыть область подробных сведений.
3. Щелкните **Удалить**.

### <a name="remove-a-schedule-with-powershell"></a>Удаление расписания с помощью PowerShell

Вы можете использовать `Remove-AzAutomationSchedule` командлет, как показано ниже, чтобы удалить существующее расписание. 

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о командлетах, используемых для доступа к расписаниям, см. в статье [Управление модулями в службе автоматизации Azure](modules.md).
* Общие сведения о модулях Runbook см. [в статье выполнение Runbook в службе автоматизации Azure](../automation-runbook-execution.md).
