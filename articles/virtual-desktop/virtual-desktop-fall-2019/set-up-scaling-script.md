---
title: Масштабирование узлов сеансов с помощью службы автоматизации Azure — Azure
description: Автоматическое масштабирование узлов сеансов Виртуального рабочего стола Windows с помощью службы автоматизации Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f659a40cbb9e3ef2d0e7fe4e527518a76507d5ee
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745713"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Масштабирование узлов сеансов с помощью службы автоматизации Azure

>[!IMPORTANT]
>Это содержимое применимо к выпуску за осень 2019 года, который не поддерживает объекты Azure Resource Manager для Виртуального рабочего стола Windows.

Вы можете сократить общие затраты на развертывание Виртуального рабочего стола Windows путем масштабирования виртуальных машин. Это означает завершение работы и освобождение виртуальных машин узла сеансов в часы непиковой нагрузки и их включение и повторное выделение в часы пиковой нагрузки.

В этой статье вы узнаете о средстве масштабирования, созданном с помощью службы автоматизации Azure и Azure Logic Apps, которое автоматически масштабирует виртуальные машины узла сеансов в среде Виртуального рабочего стола Windows. Чтобы узнать, как использовать средство масштабирования, перейдите к разделу [Предварительные требования](#prerequisites).

## <a name="report-issues"></a>Сообщение о проблемах

Отчеты о проблемах для инструмента масштабирования в настоящее время обрабатываются на GitHub, а не службой поддержки Майкрософт. Если у вас возникли проблемы с масштабированием, вы можете сообщить туда о необходимости открыть ошибку GitHub с меткой "4a-WVD-scaling-logicapps" на странице [RDS GitHub](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps).

## <a name="how-the-scaling-tool-works"></a>Принцип работы средства масштабирования

Средство масштабирования предоставляет недорогой вариант автоматизации для клиентов, желающих оптимизировать затраты на виртуальные машины узла сеансов.

Средство масштабирования можно использовать для выполнения следующих задач.
 
- Планирование запуска и остановки виртуальных машин на основе пиковых и непиковых рабочих часов.
- Увеличение масштаба виртуальных машин на основе числа сеансов на ядро ЦП.
- Уменьшение масштаба виртуальных машин в часы наименьшей нагрузки с минимальным количеством запущенных виртуальных машин.

Инструмент масштабирования использует для работы сочетание модулей PowerShell runbook для службы автоматизации Azure, веб-перехватчиков и Azure Logic Apps. При запуске этого средства Azure Logic Apps вызывает веб-перехватчик для запуска модуля runbook службы автоматизации Azure. Затем модуль runbook создает задание.

Во время пикового использования это задание проверяет текущее количество сеансов и емкость виртуальной машины текущего работающего узла сеансов для каждого пула узлов. Эта информация используется для вычисления того, могут ли виртуальные машины с работающими узлами сеансов поддерживать существующие сеансы на основе параметра *SessionThresholdPerCPU*, определенного для файла **createazurelogicapp.ps1**. Если виртуальные машины узла сеансов не справляются с существующими сеансами, задание запускает в пуле узлов дополнительные виртуальные машины узла сеансов.

>[!NOTE]
>Параметр *SessionThresholdPerCPU* не ограничивает количество сеансов на виртуальной машине. Он определяет только, нужно ли запускать новые виртуальные машины для балансировки нагрузки подключений. Чтобы ограничить количество сеансов, необходимо выполнить инструкции [Set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/), чтобы соответствующим образом настроить параметр *MaxSessionLimit*.

Во время непикового использования задание определяет, какие виртуальные машины узла сеансов должны завершить работу на основе параметра *MinimumNumberOfRDSH*. Задание задаст виртуальным машинам узла сеанса режим стока, чтобы предотвратить подключение новых сеансов к узлам. Если для параметра *LimitSecondsToForceLogOffUser* задано ненулевое положительное значение, задание будет уведомлять всех пользователей, которые вошли в систему, о необходимости сохранить свою работу, будет ожидать настроенного периода времени и затем выполнит принудительный выход пользователей. После выхода всех пользовательских сеансов на виртуальной машине узла сеансов задание завершит работу виртуальной машины.

Если для параметра *LimitSecondsToForceLogOffUser* задано нулевое значение, задание позволит параметру конфигурации сеанса в соответствующих групповых политиках обрабатывать завершение пользовательских сеансов. Чтобы просмотреть эти групповые политики, перейдите в раздел **Конфигурация компьютера** > **Политики** > **Административные шаблоны** > **Компонентов Windows** > **Службы терминалов** > **Сервер терминалов** > **Ограничения времени сеанса**. Если на виртуальной машине узла сеансов есть активные сеансы, то задание оставит ее работающей. Если активных сеансов нет, задание завершит работу виртуальной машины узла сеансов.

Задание выполняется периодически в зависимости от заданного интервала повторения. Этот интервал можно изменить в зависимости от размера среды виртуального рабочего стола Windows, но помните, что запуск и завершение работы виртуальных машин занимают определенное время, поэтому не забывайте учитывать задержку. Рекомендуется устанавливать интервал повторения раз в 15 минут.

Однако это средство также имеет следующие ограничения.

- Это решение применимо только к виртуальным машинам с узлами сеансов в составе пула.
- С помощью этого решения можно управлять виртуальными машинами в любом регионе, но его можно использовать только в той же подписке, что и учетная запись службы автоматизации Azure и Azure Logic Apps.

>[!NOTE]
>Средство масштабирования управляет режимом балансировки нагрузки для пула узлов, который оно масштабирует. Оно устанавливает режим балансировки нагрузки в ширину для периодов пиковой и непиковой нагрузки.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем приступить к настройке средства масштабирования, убедитесь, что у вас настроены следующие компоненты.

- [Клиент Виртуального рабочего стола Windows и пул узлов](create-host-pools-arm-template.md).
- Виртуальные машины пула узлов сеансов, настроенные и зарегистрированные в службе Виртуального рабочего стола Windows.
- Пользователь с правами [участника](../../role-based-access-control/role-assignments-portal.md) в подписке Azure.

На компьютере для развертывания средства должны быть установлены следующие компоненты. 

- Windows PowerShell 5.1 или более поздней версии
- Модуль PowerShell Microsoft AZ

Если у вас все готово, давайте приступим.

## <a name="create-an-azure-automation-account"></a>Создание учетной записи службы автоматизации Azure

Сначала вам потребуется учетная запись службы автоматизации Azure для запуска модуля PowerShell runbook. Для настройки учетной записи выполните следующие действия.

1. Откройте Windows PowerShell от имени администратора.
2. Выполните следующий командлет, чтобы войти в учетную запись Azure.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >Ваша учетная запись должна иметь права участника в подписке Azure, в которой требуется развернуть средство масштабирования.

3. Выполните следующий командлет, чтобы скачать сценарий для создания учетной записи службы автоматизации Azure.

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. Выполните следующий командлет, чтобы выполнить сценарий и создать учетную запись службы автоматизации Azure.

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. Выходные данные командлета будут содержать URI веб-перехватчика. Обязательно сохраните URI, так как он будет использоваться в качестве параметра при настройке расписания выполнения для Azure Logic Apps.

6. После настройки учетной записи службы автоматизации Azure войдите в свою подписку Azure и убедитесь, что ваша учетная запись службы автоматизации Azure и соответствующий модуль runbook появлялись в указанной группе ресурсов, как это показано на следующем рисунке.

![Изображение страницы обзора Azure с созданной учетной записью службы автоматизации и модулем runbook.](../media/automation-account.png)

  Чтобы проверить, что веб-перехватчик находится на своем месте, выберите имя модуля runbook. Затем перейдите к разделу ресурсов модуля runbook и выберите **Веб-перехватчики**.

## <a name="create-an-azure-automation-run-as-account"></a>Создание учетной записи запуска от имени службы автоматизации Azure

Теперь, когда у вас есть учетная запись службы автоматизации Azure, вам также потребуется создать учетную запись запуска от имени службы автоматизации Azure для доступа к ресурсам Azure.

[Учетная запись запуска от имени службы автоматизации Azure](../../automation/manage-runas-account.md) позволяет выполнять проверку подлинности для управления ресурсами в Azure с помощью командлетов Azure. При создании учетной записи запуска от имени создается новый пользователь субъекта-службы в Azure Active Directory и назначается роль участника пользователю субъекта-службы на уровне подписки. Учетная запись запуска от имени Azure — это отличный способ безопасной проверки подлинности с помощью сертификатов и имени субъекта-службы без необходимости хранить имя пользователя и пароль в объекте учетных данных. Дополнительные сведения о проверке подлинности запуска от имени см. в разделе [Ограничения учетной записи запуска от имени](../../automation/manage-runas-account.md#limit-run-as-account-permissions).

Любой пользователь с ролью администратора подписки и соадминистратор подписки может создать учетную запись запуска от имени, следуя инструкциям в следующем разделе.

Чтобы создать учетную запись запуска от имени в учетной записи Azure, выполните следующие действия.

1. На портале Azure щелкните **Все службы**. В списке ресурсов введите и выберите **Учетные записи службы автоматизации**.

2. На странице **Учетные записи службы автоматизации** выберите имя необходимой учетной записи.

3. На панели в левой части окна выберите **Учетные записи запуска от имени** в разделе параметров учетной записи.

4. Выберите **Учетная запись запуска от имени Azure**. Когда откроется область **Добавить учетную запись запуска от имени Azure**, просмотрите общие сведения и нажмите **Создать**, чтобы начать процесс создания учетной записи.

5. Подождите несколько минут, пока Azure создаст учетную запись запуска от имени. Ход создания можно отслеживать в меню в разделе "Уведомления".

6. По завершении процесса будет создан ресурс с именем AzureRunAsConnection в указанной учетной записи службы автоматизации. Этот ресурс содержит идентификаторы приложения, клиента и подписки, а также отпечаток сертификата. Запишите идентификатор приложения, он понадобится вам позже.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>создание назначения роли в Виртуальном рабочем столе Windows;

Затем необходимо создать назначение ролей, чтобы AzureRunAsConnection мог взаимодействовать с Виртуальным рабочим столом Windows. Обязательно войдите в учетную запись в PowerShell, у которой есть разрешения на создание назначений ролей.

Сначала скачайте и импортируйте [модуль PowerShell для Виртуального рабочего стола Windows](/powershell/windows-virtual-desktop/overview/) для использования в сеансе PowerShell (если вы еще это не сделали). Выполните следующие командлеты PowerShell, чтобы подключиться к Виртуальному рабочему столу Windows и отобразить клиенты.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

Когда вы найдете клиента с пулами узлов, которые необходимо масштабировать, выполните инструкции в разделе [Создание учетной записи службы автоматизации Azure](#create-an-azure-automation-account) и используйте имя клиента, полученного из предыдущего командлета, в следующем командлете для создания назначения роли.

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Создание приложения логики Azure и расписания выполнения

Наконец, вам потребуется создать приложение логики Azure и настроить расписание выполнения для нового инструмента масштабирования.

1.  Откройте Windows PowerShell от имени администратора.

2.  Выполните следующий командлет, чтобы войти в учетную запись Azure.

     ```powershell
     Login-AzAccount
     ```

3. Выполните следующий командлет, чтобы скачать файл сценария createazurelogicapp.ps1 на локальный компьютер.

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazurelogicapp.ps1"
     ```

4. Выполните следующий командлет, чтобы войти в службу Виртуальный рабочий стол Windows, используя учетную запись с разрешениями "владелец RDS" или "участник RDS".

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. Выполните следующий сценарий PowerShell, чтобы создать приложение логики Azure и расписание выполнения.

     ```powershell
     $aadTenantId = (Get-AzContext).Tenant.Id
     
     $azureSubscription = Get-AzSubscription | Out-GridView -PassThru -Title "Select your Azure Subscription"
     Select-AzSubscription -Subscription $azureSubscription.Id
     $subscriptionId = $azureSubscription.Id
     
     $resourceGroup = Get-AzResourceGroup | Out-GridView -PassThru -Title "Select the resource group for the new Azure Logic App"
     $resourceGroupName = $resourceGroup.ResourceGroupName
     $location = $resourceGroup.Location
     
     $wvdTenant = Get-RdsTenant | Out-GridView -PassThru -Title "Select your WVD tenant"
     $tenantName = $wvdTenant.TenantName
     
     $wvdHostpool = Get-RdsHostPool -TenantName $wvdTenant.TenantName | Out-GridView -PassThru -Title "Select the host pool you'd like to scale"
     $hostPoolName = $wvdHostpool.HostPoolName
     
     $recurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"
     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"
     
     $automationAccount = Get-AzAutomationAccount -ResourceGroupName $resourceGroup.ResourceGroupName | Out-GridView -PassThru
     $automationAccountName = $automationAccount.AutomationAccountName
     $automationAccountConnection = Get-AzAutomationConnection -ResourceGroupName $resourceGroup.ResourceGroupName -AutomationAccountName $automationAccount.AutomationAccountName | Out-GridView -PassThru -Title "Select the Azure RunAs connection asset"
     $connectionAssetName = $automationAccountConnection.Name
     
     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"
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

     После выполнения сценария приложение логики должно отобразиться в группе ресурсов, как показано на следующем рисунке.

     ![Изображение страницы обзора для примера приложения логики Azure.](../media/logic-app.png)

Чтобы внести изменения в расписание выполнения, например изменить интервал повторения или часовой пояс, перейдите к планировщику автомасштабирования и нажмите **Изменить**, чтобы перейти в конструктор Logic Apps.

![Изображение конструктора Logic Apps. Открыты меню повторения и веб-перехватчика, которые позволяют пользователю изменять время повторения и файл веб-перехватчика.](../media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Управление средством масштабирования

Теперь, когда вы создали средство масштабирования, вы можете получить доступ к его выходным данным. В этом разделе описаны некоторые функции, которые могут оказаться полезными.

### <a name="view-job-status"></a>Просмотр состояния задания

Вы можете просмотреть сводку по состоянию всех заданий runbook или просмотреть подробное состояние определенного задания на портале Azure.

В правой части выбранной учетной записи службы автоматизации в разделе "Статистика заданий" можно просмотреть список сводных данных всех заданий runbook. При открытии страницы **Задания** в левой части окна отображаются текущие состояния заданий, время запуска и время завершения.

![Снимок экрана со страницей состояния заданий.](../media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Просмотр журналов и выходных данных средства масштабирования

Чтобы просмотреть журналы операций увеличения и уменьшения масштаба, откройте модуль runbook и выберите имя задания.

Перейдите к модулю runbook (имя по умолчанию — WVDAutoScaleRunbook) в группе ресурсов, в которой размещена учетная запись службы автоматизации Azure, и выберите **Обзор**. На странице обзора выберите задание в разделе недавних заданий, чтобы просмотреть его выходные данные для средства масштабирования, как показано на следующем рисунке.

![Изображение окна вывода для средства масштабирования.](../media/tool-output.png)

