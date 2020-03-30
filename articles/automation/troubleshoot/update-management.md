---
title: Ошибки устранения неполадок с управлением обновлениями Azure
description: Узнайте, как устранить неполадки и устранить проблемы с решением управления обновлением в Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/17/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 91bcdc65a7ff3bcaf09f12d69ba4c7aaeb84ffa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132860"
---
# <a name="troubleshooting-issues-with-update-management"></a>Устранение неполадок c помощью управления обновлениями

В этой статье рассматриваются решения проблем, с которыми вы можете столкнуться при использовании управления обновлением.

Для агента Hybrid Worker есть средство устранения неполадок агента Hybrid Worker, чтобы определить основную проблему. Для дополнительных сведений о средстве устранения неполадок см. [Общие сведения о результатах проверки агента в службе "Управление обновлениями"](update-agent-issues.md). Для всех остальных проблем используйте следующее руководство по устранению неполадок.

Если вы столкнулись с проблемами во время попытки на борту решения на виртуальной машине (VM), проверьте журнал **менеджера операций** в **журнале приложений и служб** на локальной машине для событий с идентификатором событий 4502 и сведениями о событиях, которые содержат `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`.

В следующем разделе освещаются конкретные сообщения об ошибках и возможные разрешения для каждого из них. Для других вопросов, связанных с посадкой на борт, [см.](onboarding.md)

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a>Сценарий: Вы получаете ошибку "Не удалось включить решение обновления"

### <a name="issue"></a>Проблема

При попытке включить решение управления обновлением в учетной записи Автоматизации вы сталкиваетесь со следующей ошибкой:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Причина

Эта ошибка может возникать по следующим причинам.

* Требования сетевого брандмауэра к агенту Log Analytics могут быть неправильно настроены, в результате чего агент сбой при решении URL-адресов DNS.

* Таргетинг решений неправильно настроен, и машина не получает обновлений, как ожидалось.

* Вы также можете заметить, что `Non-compliant` машина показывает статус под **соответствием**. В то же время, агент обновления `Disconnected` **готовности** сообщает агент как .

### <a name="resolution"></a>Решение

* Запустите устранение неполадок для [Windows](update-agent-issues.md#troubleshoot-offline) или [Linux](update-agent-issues-linux.md#troubleshoot-offline), в зависимости от ОС.

* Перейдите в [сеть планирования,](../automation-hybrid-runbook-worker.md#network-planning) чтобы узнать о том, какие адреса и порты должны быть разрешены для управления обновлением для работы.  

* Перейдите в [сеть планирования,](../../azure-monitor/platform/log-analytics-agent.md#network-requirements) чтобы узнать о том, какие адреса и порты должны быть разрешены для агента журнала Analytics для работы.

* Проверьте наличие проблем с конфигурацией области. [Конфигурация области](../automation-onboard-solutions-from-automation-account.md#scope-configuration) определяет, какие машины настраиваться для решения. Если машина отображается в рабочем пространстве, но не на портале **управления обновлением,** вам нужно настроить конфигурацию области для таргетинга на машины. Чтобы узнать о конфигурации области, [см.](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)

* Удалите конфигурацию рабочего, выпустив следующие действия в [деле удаления гибридного сотрудника runbook.](../automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker) 

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Сценарий: Застеченное обновление, указанное как отсутствующее в управлении обновлением

### <a name="issue"></a>Проблема

Старые обновления появляются в обновлении управления в учетной записи автоматизации, как отсутствует, даже если они были заменены. Замененное обновление — это обновление, которое не должно быть установлено, потому что доступно более позднее обновление, исправляющее ту же уязвимость. Управление обновлением игнорирует замененное обновление и делает его неприменимым в пользу заменяющего обновления. Для получения информации о связанной с ней проблеме [см.](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)

### <a name="cause"></a>Причина

Заменяемые обновления не правильно указано как отклоненные, так что они могут считаться неприменимыми.

### <a name="resolution"></a>Решение

Когда заменяемое обновление становится на 100 процентов неприменимым, `Declined`следует изменить состояние утверждения этого обновления на . Для этого для всех ваших обновлений:

1. В учетной записи Automation выберите **управление обновлением** для просмотра состояния машины. Смотрите [оценки обновления просмотра](../manage-update-multi.md#view-an-update-assessment).

2. Проверьте замененное обновление, чтобы убедиться, что оно на 100 процентов неприменимо. 

3. Отметьте обновление как отклоненное, если у вас нет вопроса об обновлении. 

4. Выберите **Компьютеры** и в столбце **соответствия** принудите повторное сканирование для соответствия требованиям. Смотрите [Обновления Управления для нескольких машин.](../manage-update-multi.md)

5. Повторите вышеприведенные шаги для других замененных обновлений.

6. Выполнить мастер очистки, чтобы удалить файлы из отклоненных обновлений. 

7. Для WSUS вручную очистите все замененные обновления для обновления инфраструктуры.

8. Регулярно повторяйте эту процедуру, чтобы исправить проблему с дисплеем и свести к минимуму количество дискового пространства, используемого для управления обновлением.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Сценарий: Машины не отображаются на портале под управлением обновления

### <a name="issue"></a>Проблема

Ваши машины имеют следующие симптомы:

* Ваша машина `Not configured` показывает из представления управления обновлением VM.

* Ваши машины отсутствуют в представлении управления обновлением учетной записи Azure Automation.

* У вас есть `Not assessed` машины, которые показывают, как в **соответствии с .** Однако данные о сердцебиении можно увидеть в журналах Azure Monitor для гибридного сотрудника Runbook, но не для управления обновлением.

### <a name="cause"></a>Причина

Эта проблема может быть вызвана локальными проблемами конфигурации или неправильно настроенной конфигурацией области.

Возможно, вам придется перерегистрировать и переустановить гибридный Runbook worker.

Возможно, вы определили квоту в рабочей области, которая была достигнута, что препятствует дальнейшему хранению данных.

### <a name="resolution"></a>Решение

* Запустите устранение неполадок для [Windows](update-agent-issues.md#troubleshoot-offline) или [Linux](update-agent-issues-linux.md#troubleshoot-offline), в зависимости от ОС.

* Убедитесь, что ваша машина отчитывается перед правильным рабочим пространством. Для получения рекомендаций о том, как проверить этот аспект, [см.](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics) Также убедитесь, что это рабочее пространство связано с учетной записью Azure Automation. Чтобы подтвердить, перейдите на свой аккаунт Автоматизации и выберите **связанное рабочее пространство** под **ссылками ресурсов.**

* Убедитесь, что машины отображаются в рабочем пространстве Log Analytics. Выполнить следующий запрос в рабочей области Log Analytics, который связан с учетной записью Автоматизация:

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```
 
  Если вы не видите свою машину в результатах запроса, она недавно зарегистрировалась, что означает, что, вероятно, существует проблема локальной конфигурации, и вы должны [переустановить агент.](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows) Если машина отображается в результатах запроса, необходимо проверить конфигурацию области, указанную в следующем элементе в этом списке.

* Проверьте наличие проблем с конфигурацией области. [Конфигурация области](../automation-onboard-solutions-from-automation-account.md#scope-configuration) определяет, какие машины настраиваться для решения. Если машина отображается в рабочем пространстве, но не на портале **управления обновлением,** вам нужно настроить конфигурацию области для таргетинга на машины. Чтобы узнать, как это сделать, смотрите [бортовые машины в рабочей области.](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)

* В рабочей области запустите следующий запрос:

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```

  Если вы `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` получаете результат, в рабочем пространстве определена квота, которая была достигнута и которая не позволяет сохранить данные. В рабочем пространстве перейдите в управление**объемом данных** **Usage и estimated costs,** > проверьте свою квоту или удалите ее.

* Если эти действия не решат проблему, выполните следующие действия в [развертывании рабочего с гибридным Runbook Windows](../automation-windows-hrw-install.md) для переустановки гибридного рабочего для Windows. Для Linux выполните последующие шаги в [развертывании Linux Hybrid Runbook Worker.](../automation-linux-hrw-install.md)

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Сценарий: Невозможно зарегистрировать поставщика ресурсов автоматизации для подписок

### <a name="issue"></a>Проблема

Когда вы работаете с решениями в учетной записи Автоматизации, вы сталкиваетесь со следующей ошибкой:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Причина

Поставщик ресурсов автоматизации не зарегистрирован в подписке.

### <a name="resolution"></a>Решение

Чтобы зарегистрировать поставщика ресурсов автоматизации, выполните следующие действия на портале Azure:

1. В списке служб Azure в нижней части портала выберите **все службы,** а затем выберите **Подписки** в общей группе обслуживания.
2. Выберите свою подписку.
3. В **настройках**выберите **поставщиков ресурсов.**
4. Из списка поставщиков ресурсов убедитесь, что поставщик `Microsoft.Automation` ресурсов зарегистрирован.
5. Если он не указан, `Microsoft.Automation` зарегистрируйте поставщика, выполнив следующие действия по [ошибкам Resolve для регистрации поставщика ресурсов.](/azure/azure-resource-manager/resource-manager-register-provider-errors)

## <a name="scenario-components-for-update-management-solution-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Сценарий: Включены компоненты для решения управления обновлением, в то время как VM продолжает отображаться как настроенный

### <a name="issue"></a>Проблема

Приведенное ниже сообщение на виртуальной машине не исчезает в течение 15 минут после подключения:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Причина

Эта ошибка может возникать по следующим причинам.

* Связь с учетной записью Automation блокируется.

* Существует дубликат названия компьютера с различными иди кто-то из исходных компьютеров. Этот сценарий возникает, когда VM с определенным именем компьютера создается в разных группах ресурсов и отчитывается перед тем же рабочим пространством агента логистики в подписке.

* Изображение VM, находясь на борту, может исходить от клонированной машины, которая не была подготовлена с system Preparation (sysprep) с установленным агентом мониторинга Microsoft (MMA).

### <a name="resolution"></a>Решение

Чтобы помочь определить точную проблему с VM, запустите следующий запрос в рабочей области Log Analytics, которая связана с учетной записью Автоматизация:

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Связь с аккаунтом Автоматизация заблокирована

Перейдите в [сеть планирования,](../automation-update-management.md#ports) чтобы узнать о том, какие адреса и порты должны быть разрешены для управления обновлением для работы.

#### <a name="duplicate-computer-name"></a>Двойное имя компьютера

Переименуйте свои вм: Для обеспечения уникальных имен в их среде.

#### <a name="onboarded-image-from-cloned-machine"></a>На борту изображения с клонированной машины

Если вы используете клонированное изображение, разные имена компьютеров имеют один и тот же идентификатор исходного компьютера. В данном случае:

1. В рабочем пространстве журнала Analytics удалите VM `MicrosoftDefaultScopeConfig-Updates` из сохраненного поиска конфигурации области, если она отображается. Сохраненные поисковые запросы хранятся на вкладке **Общие** рабочей области.

2. Выполните следующий командлет:

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Выполнить, `Restart-Service HealthService` чтобы перезапустить службу здравоохранения. Эта операция воссоздает ключ и генерирует новый UUID.

4. Если этот подход не работает, запустите sysprep на изображении, а затем установить MMA.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Сценарий: При создании развертывания обновления для машин в другом клиенте Azure вы получаете связанную ошибку подписки

### <a name="issue"></a>Проблема

Вы сталкиваетесь со следующей ошибкой при попытке создания развертывания обновлений для машин в другом арендаторе Azure:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Причина

Эта ошибка возникает при создании развертывания обновления с помощью mMs Azure в другом элементе, включенном в развертывание обновлений.

### <a name="resolution"></a>Решение

Используйте следующий обходной путь, чтобы получить эти элементы запланированы. Для создания расписания можно использовать смдлет `ForUpdate` [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) с параметром. Затем используйте смдлет [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) и передайте машины `NonAzureComputer` другому арендатору по параметру. В приведенном ниже примере показано, как это сделать.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Сценарий: Необъяснимые перезагрузки

### <a name="issue"></a>Проблема

Даже если вы установили опцию **управления перезагрузкой,** **чтобы никогда не перезагрузить,** машины по-прежнему перезагрузки после установки обновлений.

### <a name="cause"></a>Причина

Обновление Windows может быть изменено несколькими ключами реестра, любой из которых может изменить поведение перезагрузки.

### <a name="resolution"></a>Решение

Просмотрите ключи реестра, перечисленные в соответствии [с настройкой автоматических обновлений, редактируя](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) ключи реестра и [реестра, используемые для управления перезагрузкой,](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) чтобы убедиться, что ваши машины настроены должным образом.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Сценарий: Машина показывает "Не удалось начать" в развертывании обновления

### <a name="issue"></a>Проблема

Машина показывает `Failed to start` статус. При просмотре конкретных деталей для машины вы видите следующую ошибку:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Причина

Ошибка может быть вызвана одной из следующих причин:

* Машина больше не существует.
* Машина выключена и недосягаема.
* Машина имеет проблему подключения к сети, и поэтому гибридный работник на машине недосягаем.
* Было обновление мМА, которое изменило идентификатор исходного компьютера.
* Запуск обновления был задушен, если вы нажмете предел 2000 одновременных заданий в учетной записи Автоматизация. Каждое развертывание считается заданием, и каждая машина в развертывании обновлений считается заданием. Любое другое задание автоматизации или развертывание обновлений, в настоящее время запущенное в учетной записи Automation, учитывается в пределах одновременного ограничения задания.

### <a name="resolution"></a>Решение

Когда это применимо, используйте [динамические группы](../automation-update-management-groups.md) для развертывания обновлений. Дополнительно:

* Убедитесь, что машина все еще существует и достижима. 
* Если машина не существует, отоверьте развертывание и удалите его.
* Ознакомьтесь с разделом [планирования сети](../automation-update-management.md#ports) для списка портов и адресов, необходимых для управления обновлением, а затем убедитесь, что ваша машина соответствует этим требованиям.
* Проверить подключение к гибридной Runbook работника с помощью гибридного агента Runbook работника устранение неполадок. Для дополнительных сведений о средстве устранения неполадок см. [Общие сведения о результатах проверки агента в службе "Управление обновлениями"](update-agent-issues.md).
* Выполнить следующий запрос в журнале Analytics, чтобы найти машины в вашей среде, для которых идентификатор исходного компьютера изменился. Ищите компьютеры, `Computer` которые имеют `SourceComputerId` одинаковое значение, но другое значение.

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

* После того, как вы найдете затронутые машины, откорректировать развертывание обновлений, нацеленных на эти машины, а затем удалите и повторно добавьте их так, чтобы это `SourceComputerId` отражало правильное значение.

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Сценарий: Обновления устанавливаются без развертывания

### <a name="issue"></a>Проблема

При регистрации машины Windows в управлении обновлением вы видите обновления, установленные без развертывания.

### <a name="cause"></a>Причина

На Windows обновления устанавливаются автоматически, как только они доступны. Такое поведение может привести к путанице, если вы не запланировали развертывание обновления в машине.

### <a name="resolution"></a>Решение

Ключ `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` реестра по умолчанию к `auto download and install`параметру 4: .

Для клиентов Update Management мы рекомендуем `auto download but do not auto install`установить этот ключ до 3: .

Для получения дополнительной [информации см.](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates)

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Сценарий: компьютер уже зарегистрирован на другой учетной записи

### <a name="issue"></a>Проблема

Отображается следующее сообщение об ошибке:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Причина

Машина уже была на борту в другое рабочее пространство для управления обновлением.

### <a name="resolution"></a>Решение

1. Следуйте шагам под [машины не отображаются на портале под управлением обновления,](#nologs) чтобы убедиться, что машина сообщает в правильное рабочее пространство.
2. Очистите артефакты на машине, [удалив гибридную группу runbook,](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)а затем повторите попытку.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Сценарий: Машина не может общаться с службой

### <a name="issue"></a>Проблема

Отображается одно из следующих сообщений об ошибке.

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>Причина

Прокси, шлюз или брандмауэр могут блокировать сетевую связь. 

### <a name="resolution"></a>Решение

Просмотрите свои сети и убедитесь, что соответствующие порты и адреса разрешены. Ознакомьтесь [с сетевыми требованиями](../automation-hybrid-runbook-worker.md#network-planning) к списку портов и адресов, которые требуются сотрудникамupdate и гибридным Runbook.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Сценарий: не удалось создать самозаверяющий сертификат

### <a name="issue"></a>Проблема

Отображается одно из следующих сообщений об ошибке.

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Причина

Гибридный Runbook Worker не смог создать сертификат, подписанный самостоятельно.

### <a name="resolution"></a>Решение

Убедитесь, что учетная запись системы прочитала доступ к папке **C: «ProgramData»Microsoft,Crypto-RSA,** и повторите попытку.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Сценарий: Запланированное обновление не удалось с ошибкой MaintenanceWindowExceededure

### <a name="issue"></a>Проблема

Окно обслуживания по умолчанию для обновлений составляет 120 минут. Окно обслуживания можно увеличить максимум до 6 часов или 360 минут.

### <a name="resolution"></a>Решение

Отсечь все неудачные запланированные развертывания обновлений и увеличить окно обслуживания.

Для получения дополнительной информации о окнах обслуживания, [см.](../automation-tutorial-update-management.md#schedule-an-update-deployment)

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Сценарий: Машина показывает, как "Не оценивается" и показывает исключение HRESULT

### <a name="issue"></a>Проблема

* У вас есть `Not assessed` машины, которые отображались как в соответствии с **требованиями,** и вы видите сообщение об исключении под ними.
* На портале видна код ошибки HRESULT.

### <a name="cause"></a>Причина

Агент обновления (агент обновления Windows на Windows; менеджер пакетов для дистрибутива Linux) настроен неправильно. Управление обновлением полагается на агента обновления машины, чтобы предоставить необходимые обновления, состояние патча и результаты развернутых исправлений. Без этой информации управление обновлением не может должным образом сообщать о необходимых или установленных патчах.

### <a name="resolution"></a>Решение

Попробуйте выполнить обновления локально на машине. Если это не удается, это обычно означает, что есть ошибка конфигурации с агентом обновления.

Эта проблема часто вызвана проблемами конфигурации сети и брандмауэра. Попробуйте сделать следующее.

* Для Linux проверьте соответствующую документацию, чтобы убедиться, что вы можете достичь конечной точки сети репозитория пакета.
* Для Windows проверьте конфигурацию агента, как указано в [обновлениях, не загружая с конечной точки интрасети (WSUS/SCCM).](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)
  * Если машины настроены для обновления Windows, убедитесь, что вы можете достичь конечных точек, описанных в [вопросах, связанных с HTTP/прокси.](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)
  * Если машины настроены для служб обновления серверов Windows (WSUS), убедитесь, что вы можете достичь сервера WSUS, настроенного [ключом реестра WUServer.](/windows/deployment/update/waas-wu-settings)

Если вы видите HRESULT, дважды щелкните исключение, отображаемые красным цветом, чтобы увидеть все сообщение исключения. Просмотрите следующую таблицу потенциальных решений или рекомендуемых действий:

|Исключение  |Резолюция или действие  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Поиск соответствующего кода ошибки в [списке ошибок обновления Windows,](https://support.microsoft.com/help/938205/windows-update-error-code-list) чтобы найти дополнительные сведения о причине исключения.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Они указывают на проблемы с подключением к сети. Убедитесь, что ваша машина имеет сетевое подключение к управлению обновлением. Ознакомьтесь с разделом [планирования сети](../automation-update-management.md#ports) для получения списка необходимых портов и адресов.        |
|`0x8024001E`| Операция обновления не завершена из-за того, что служба или система были выключены.|
|`0x8024002E`| Служба обновления Windows отключена.|
|`0x8024402C`     | Если вы используете сервер WSUS, убедитесь, `WUServer` что `WUStatusServer` значения `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` реестра для и под ключом реестра указать правильный сервер WSUS.        |
|`0x80072EE2`|В разговоре с настроенным сервером WSUS возникает проблема с подключением к сети или проблема. Проверьте настройки WSUS и убедитесь, что услуга доступна от клиента.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Убедитесь, что служба обновления Windows (wuauserv) работает и не отключена.        |
|`0x80070005`| Ошибка, отклоняемых при доступе, может быть вызвана любой из следующих проблем:<br> Зараженный компьютер<br> Настройки обновления Windows не настроены правильно<br> Ошибка разрешения файла с папкой %WinDir%-SoftwareDistribution<br> Недостаточное пространство диска на системном диске (C:).
|Другое универсальное исключение     | Выполнить поиск в Интернете для возможных решений, и работать с локальной ИТ-поддержки.         |

Просмотр файла %Windir%-Windowsupdate.log также может помочь вам определить возможные причины. Для получения дополнительной информации о том, как читать журнал, смотрите [Как читать Windowsupdate.log файл](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Вы также можете скачать и запустить [windows Update устранение неполадок,](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) чтобы проверить любые проблемы с обновлением Windows на машине.

> [!NOTE]
> Документация [по устранению неполадок обновления Windows](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) указывает на то, что она используется клиентамwindows Windows, но также работает на Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Сценарий: Обновление запуска возвращает "Неудалось" статус (Linux)

### <a name="issue"></a>Проблема

Запускается запуск обновления, но во время выполнения он сталкивается с ошибками.

### <a name="cause"></a>Причина

Возможные причины:

* Менеджер пакетов неработоспособен.
* Обновление Агент (WUA для Windows, дистро-специфический менеджер пакетов для Linux) неправильно настроен.
* Конкретные пакеты мешают облачному исправлению.
* Машина недосягаема.
* Обновления имели зависимости, которые не были решены.

### <a name="resolution"></a>Решение

Если сбои возникают во время выполнения обновления после его успешного запуска, [проверьте выход задания](../manage-update-multi.md#view-results-of-an-update-deployment) из пострадавшей машины в запуске. Вы можете найти конкретные сообщения об ошибках от ваших машин, которые вы можете исследовать и принять меры. Для успешного обновления развертываний управлению обновлениями необходима работоспособность диспетчера пакетов.

Если конкретные патчи, пакеты или обновления видны непосредственно перед сбой задания, можно попробовать [исключить](../automation-tutorial-update-management.md#schedule-an-update-deployment) их из следующего развертывания обновления. Чтобы собрать информацию о [Windows Update log files](/windows/deployment/update/windows-update-logs)журнале из windows Update, см.

Если вы не можете решить проблему с исправлением, сделайте копию следующего файла журнала и сохраните его для устранения неполадок до начала следующего развертывания обновления:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-arent-installed"></a>Патчи не установлены

### <a name="machines-dont-install-updates"></a>Обновления не устанавливаются на компьютеры

Попробуйте выполнить обновление непосредственно на компьютере. Если машина не может применить обновления, проконсультируйтесь со [списком потенциальных ошибок в руководстве по устранению неполадок.](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult)

Если обновления заходят локально, попробуйте удалить и перезаложить агента на машину, следуя указаниям на [Удаление VM из управления обновлением.](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources)

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Я знаю, что обновления доступны, но они не отображаются, как доступны на моих машинах

Это часто происходит, если машины настроены для получения обновлений от WSUS или Microsoft Endpoint Configuration Manager, но WSUS и Configuration Manager не одобрили обновления.

Вы можете проверить, если машины настроены для WSUS и SCCM путем перекрестного ссылки на ключ `UseWUServer` реестра к ключам реестра в [настройке автоматических обновлений путем редактирования раздела реестра этой статьи](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).

Если обновления не утверждены в WSUS, они не устанавливаются. Вы можете проверить неутвержденные обновления в журнале Analytics, запустив следующий запрос.

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Обновления отображаются как установленные, но найти их на компьютере не удается

Обновления часто заменяются другими обновлениями. Для получения дополнительной информации, см [Обновление заменяется](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) в Руководстве по устранению проблем windows Update.

### <a name="installing-updates-by-classification-on-linux"></a>Установка обновлений с учетом классификации на платформе Linux

При развертывании обновлений на Linux с учетом классификации (критические обновления и обновления системы безопасности) следует учитывать некоторые важные ограничения, особенно для CentOS. Эти ограничения задокументированы на [странице обзора управления обновлениями](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 постоянно отсутствует

Статья базы знаний № 2267602 касается [обновления определений Защитника Windows](https://www.microsoft.com/wdsi/definitions). Он обновляется ежедневно.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не увидели проблему или не можете решить проблему, попробуйте один из следующих каналов для дополнительной поддержки:

* Получите ответы от экспертов Azure через [форумы Azure.](https://azure.microsoft.com/support/forums/)
* Связаться [@AzureSupport](https://twitter.com/azuresupport)с официальной учетной записью Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.
* Отправьте запрос в службу поддержки Azure Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **«Получите поддержку».**
