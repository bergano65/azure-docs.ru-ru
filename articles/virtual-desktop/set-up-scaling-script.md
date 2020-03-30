---
title: Масштабная сессия хостов Azure Автоматизация - Azure
description: Как автоматически масштабировать хосты сеанса виртуального рабочего стола Windows с помощью Azure Automation.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3a853dc32f8716f3f2ba32896a7a4a239efcc5bd
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349878"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Масштабирование сеанса с помощью автоматизации Azure

Вы можете уменьшить общую стоимость развертывания Windows Virtual Desktop путем масштабирования виртуальных машин (VM). Это означает закрытие и распределение vMs-миноностов в непиковые часы использования, а затем их включение и перераспределение в часы пик.

В этой статье вы узнаете об инструменте масштабирования, построенном с помощью приложений Azure Automation и Azure Logic Apps, которые будут автоматически масштабировать виртуальные компьютеры для размещения сеансов в среде Windows Virtual Desktop. Чтобы узнать, как использовать инструмент масштабирования, перейдите вперед к [предпосылкам.](#prerequisites)

## <a name="how-the-scaling-tool-works"></a>Как работает инструмент масштабирования

Инструмент масштабирования предоставляет недорогие варианты автоматизации для клиентов, которые хотят оптимизировать свои расходы на размещение сеансов VM.

Инструмент масштабирования можно использовать для:
 
- Расписание VMs, чтобы начать и остановить на основе пиковых и off-Peak рабочих часов.
- Масштабируйте VMs на основе количества сеансов на ядро процессора.
- Масштабирование в VMs в непиковые часы, оставляя минимальное количество хостов сеансов.

Для функции инструмента масштабирования используется комбинация рундляов Azure Automation PowerShell, веб-крючков и приложений логики Azure. При запуске инструмента Azure Logic Apps вызывает веб-крюк для запуска запуска запуска Azure Automation. Затем в runbook создается задание.

В пиковое время использования задание проверяет текущее количество сеансов и емкость ВМ текущего хоста сеанса для каждого пула хоста. Он использует эту информацию для расчета, может ли хост-мнобудет для выполнения сеанса поддерживать существующие сеансы на основе параметра *SessionThresholdPerCPU,* определенного для файла **createazurelogicapp.ps1.** Если VMs-хосена сеанса не могут поддерживать существующие сеансы, задания запускает дополнительные ми-хост сеанса в пуле хоста.

>[!NOTE]
>*SessionThresholdPerCPU* не ограничивает количество сеансов на VM. Этот параметр определяет только то, когда новые ВМ необходимо начать для загрузки-баланс соединения. Чтобы ограничить количество сеансов, необходимо следовать инструкциям [Set-RdsHostPool,](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) чтобы настроить параметр *MaxSessionLimit* соответствующим образом.

Во время непикового времени использования задание определяет, какие vMs-хозяйские часы должны быть закрыты на основе параметра *MinimumNumberOfRDSH.* Задание установит, что VMs-хосты сеанса сливной режим, чтобы предотвратить подключение новых сеансов к хостам. Если вы установите параметр *LimitSecondsToForceLogOffUser* на ненулевое положительное значение, задание уведомит всех пользователей, которые в настоящее время подписаны, чтобы сохранить их работу, подождать настроенное количество времени, а затем заставить пользователей выйти из системы. После того, как все пользовательские сеансы на хосте Сессии VM будут подписаны, задание выключит VM.

Если параметр *LimitSecondsToForceLogOffUser* будет сведен к нулю, задания позволит настройке конфигурации сеанса в определенных парабищах группы обрабатывать подписание сеансов. Чтобы увидеть эти групповые политики, перейдите к **политике компьютерной** > **конфигурации Административные** > **шаблоны** > Windows**Компоненты** > Терминал услуг**терминала** > **сервера** > **сроки.** При наличии активных сеансов на vM-сессии задания задания оставляет хост-хост-сессии ВМ. Если активных сеансов нет, задание выключит хост-хост-сессии VM.

Задание выполняется периодически на основе заданным интервалом рецидива. Вы можете изменить этот интервал в зависимости от размера среды Windows Virtual Desktop, но помните, что запуск и выключение виртуальных машин может занять некоторое время, поэтому не забудьте учесть задержку. Мы рекомендуем устанавливать интервал рецидива каждые 15 минут.

Тем не менее, инструмент также имеет следующие ограничения:

- Это решение применяется только к объединенным хост-множесяк сеанса.
- Это решение управляет ввосай-мыву в любом регионе, но может использоваться только в той же подписке, что и учетная запись Azure Automation и приложения Логика Azure.

>[!NOTE]
>Инструмент масштабирования контролирует режим балансировки нагрузки пула хоста, который масштабируется. Он устанавливает его на ширину первой нагрузки балансировки для пиковых и внепиковых часов.

## <a name="prerequisites"></a>Предварительные требования

Перед тем, как приступить к настройке инструмента масштабирования, убедитесь, что у вас есть следующие вещи готовы:

- Арендатор [и пул хостов виртуального рабочего стола Windows](create-host-pools-arm-template.md)
- Виртуальный пул носов для сеансов, настроенный и зарегистрированный в службе Виртуального рабочего стола Windows
- Пользователь с [доступом к автору](../role-based-access-control/role-assignments-portal.md) по подписке Azure

Машина, используемая для развертывания инструмента, должна иметь: 

- Windows PowerShell 5.1 или позже
- Модуль Microsoft Az PowerShell

Если у вас есть все готово, то давайте начнем.

## <a name="create-an-azure-automation-account"></a>Создание учетной записи службы автоматизации Azure

Во-первых, для запуска runbook PowerShell потребуется учетная запись Azure Automation. Вот как настроить учетную запись:

1. Откройте Windows PowerShell от имени администратора.
2. Запустите следующий cmdlet, чтобы войти в свою учетную запись Azure.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >Ваша учетная запись должна иметь права на участников в подписке Azure, на которых вы хотите развернуть инструмент масштабирования.

3. Запустите следующий cmdlet, чтобы загрузить скрипт для создания учетной записи Azure Automation:

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. Выполнить следующий cmdlet для выполнения скрипта и создать учетную запись Azure Automation:

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. Выход cmdlet будет включать в себя webhook URI. Обязательно сохраните запись URI, потому что вы будете использовать его в качестве параметра при настройке графика выполнения приложений Azure Logic.

6. После настройки учетной записи Azure Automation вопийте подписку На Azure и проверьте, чтобы ваша учетная запись Azure Automation и соответствующий runbook появились в указанной группе ресурсов, как показано на следующем изображении:

   ![Изображение страницы обзора Azure, на которой изображена недавно созданная учетная запись автоматизации и беговая книга.](media/automation-account.png)

  Чтобы проверить, если ваш веб-крюк, где он должен быть, выберите имя вашего runbook. Далее перейдите в раздел Ресурсов вашего запуска и выберите **Webhooks.**

## <a name="create-an-azure-automation-run-as-account"></a>Создание системы автоматизации Azure как учетная запись

Теперь, когда у вас есть учетная запись Azure Automation, вам также необходимо создать учетную запись Azure Automation Run As для доступа к ресурсам Azure.

Учетная [запись Azure Automation Run As](../automation/manage-runas-account.md) обеспечивает аутентификацию для управления ресурсами в Azure с помощью cmdlets Azure. При создании учетной записи Run As создается новый основной пользователь службы в active Directory Azure и назначает роль автора основному пользователю службы на уровне подписки, учетная запись Azure Run As — это отличный способ безопасной аутентификации с сертификаты и основное имя службы без необходимости хранения имени пользователя и пароля в объекте учетных данных. Чтобы узнать больше о run As authentication, [см.](../automation/manage-runas-account.md#limiting-run-as-account-permissions)

Любой пользователь, являющиеся участником роли администраторов подписки и соуправляющим сотрудником подписки, может создать учетную запись Run As, следуя инструкциям следующего раздела.

Чтобы создать учетную запись Run As в учетной записи Azure:

1. На портале Azure выберите **все сервисы.** В списке ресурсов введите и выберите **учетные записи автоматизации.**

2. На странице **учетных записей автоматизации** выберите имя учетной записи Автоматизации.

3. В панели на левой стороне окна выберите **«Запуск как учетные записи»** в разделе «Настройки учетной записи».

4. Выберите **Azure Run в качестве учетной записи.** При отображдении панели **«Добавить Azure Run As»** панель учета просмотрите информацию об обзоре, а затем выберите **«Создать»** для запуска процесса создания учетной записи.

5. Подождите несколько минут, чтобы Azure создала учетную запись Run As. Вы можете отслеживать ход создания в меню под уведомлениями.

6. По завершении процесса в указанной учетной записи Автоматизации будет создан актив под названием AzureRunAsConnection. Актив подключения содержит идентификатор приложения, идентификатор клиента, идентификатор подписки и отпечаток пальца сертификата. Запомните идентификатор приложения, потому что вы будете использовать его позже.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>создание назначения роли в Виртуальном рабочем столе Windows;

Далее необходимо создать ролевую задачу, чтобы AzureRunAsConnection мог взаимодействовать с Виртуальным рабочим столом Windows. Убедитесь в том, что использовать PowerShell для вху-си-аккаунта с разрешениями на создание ролевых заданий.

Во-первых, загрузите и импортируйте [модуль Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) для использования в сеансе PowerShell, если вы еще не сделали этого. Выполните следующие командлеты PowerShell, чтобы подключиться к Виртуальному рабочему столу Windows и отобразить клиенты.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

Когда вы находите арендатора с пулами хоста, который вы хотите масштабировать, следуйте инструкциям в [создать учетную запись Azure Automation](#create-an-azure-automation-account) и используйте имя клиента, которое вы получили от предыдущего cmdlet в следующем cmdlet, чтобы создать назначение ролей:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Создание приложения Логика Azure и график выполнения

Наконец, необходимо создать приложение Azure Logic App и настроить график выполнения нового инструмента масштабирования.

1.  Открыть Windows PowerShell в качестве администратора

2.  Запустите следующий cmdlet, чтобы войти в свою учетную запись Azure.

     ```powershell
     Login-AzAccount
     ```

3. Выполнить следующий cmdlet, чтобы загрузить файл сценария createazurelogicapp.ps1 на локальной машине.

     ```powershell
     Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1" -OutFile "your local machine path\ createazurelogicapp.ps1"
     ```

4. Запустите следующий cmdlet, чтобы войти в Windows Virtual Desktop с учетной записью, которая имеет разрешения владельца RDS или RDS-участника.

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. Выполнить следующий скрипт PowerShell для создания приложения Azure Logic и графика выполнения.

     ```powershell
     $resourceGroupName = Read-Host -Prompt "Enter the name of the resource group for the new Azure Logic App"
     
     $aadTenantId = Read-Host -Prompt "Enter your Azure AD tenant ID"

     $subscriptionId = Read-Host -Prompt "Enter your Azure Subscription ID"

     $tenantName = Read-Host -Prompt "Enter the name of your WVD tenant"

     $hostPoolName = Read-Host -Prompt "Enter the name of the host pool you'd like to scale"

     $recurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"

     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"

     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"

     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"

     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"

     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"

     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"

     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"

     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

     $location = Read-Host -Prompt "Enter the name of the Azure region where you will be creating the logic app"

     $connectionAssetName = Read-Host -Prompt "Enter the name of the Azure RunAs connection asset"

     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"

     $automationAccountName = Read-Host -Prompt "Enter the name of the Azure Automation Account"

     $maintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"

     .\createazurelogicapp.ps1 -ResourceGroupName $resourceGroupName `
       -AADTenantID $aadTenantId `
       -SubscriptionID $subscriptionId `
       -TenantName $tenantName `
       -HostPoolName $hostPoolName `
       -RecurrenceInterval $recurrenceInterval `
       -BeginPeakTime $beginPeakTime `
       -EndPeakTime $endPeakTime `
       -TimeDifference $timeDifference `
       -SessionThresholdPerCPU $sessionThresholdPerCPU `
       -MinimumNumberOfRDSH $minimumNumberOfRdsh `
       -LimitSecondsToForceLogOffUser $limitSecondsToForceLogOffUser `
       -LogOffMessageTitle $logOffMessageTitle `
       -LogOffMessageBody $logOffMessageBody `
       -Location $location `
       -ConnectionAssetName $connectionAssetName `
       -WebHookURI $webHookURI `
       -AutomationAccountName $automationAccountName `
       -MaintenanceTagName $maintenanceTagName
     ```

     После запуска скрипта logic App должен отображаться в группе ресурсов, как показано на следующем изображении.

     ![Изображение страницы обзора для например приложения Azure Logic App.](media/logic-app.png)

Чтобы внести изменения в расписание выполнения, такие как изменение интервала повторения или часового пояса, перейдите к планировщику Autoscale и выберите **Edit,** чтобы перейти к Designer Приложений логики.

![Изображение дизайнера логических приложений. Меню Recurrence и Webhook, которые позволяют пользователю отодвигать время повторения, и файл Webhook открыты.](media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Управление инструментом масштабирования

Теперь, когда вы создали инструмент масштабирования, вы можете получить доступ к его выходу. В этом разделе описаны некоторые функции, которые могут оказаться полезными.

### <a name="view-job-status"></a>Просмотр состояния задания

Вы можете просмотреть обобщенный статус всех заданий runbook или более углубленное состояние конкретного задания runbook на портале Azure.

Справа от выбранной учетной записи Automation, в соответствии с "Статистика работы", вы можете просмотреть список резюме всех заданий runbook. Открытие страницы **Вакансии** на левой стороне окна показывает текущие состояния заданий, время начала и время завершения.

![Скриншот страницы о состоянии задания.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Просмотр журналов и вывода инструментов масштабирования

Вы можете просмотреть журналы операций по масштабированию и масштабированию, открыв свой сборник и выбрав название своей работы.

Перейдите к runbook (имя по умолчанию - WVDAutoScaleRunbook) в группе ресурсов, худеяря учетную запись Azure Automation, и выберите **Обзор.** На странице обзора выберите задание под «Последние задания» для просмотра вывода инструмента масштабирования, как показано на следующем изображении.

![Изображение окна вывода для инструмента масштабирования.](media/tool-output.png)

## <a name="report-issues"></a>Сообщение о проблемах

Если вы столкнулись с какими-либо проблемами с инструментом масштабирования, вы можете сообщить о них на [странице RDS GitHub.](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps)
