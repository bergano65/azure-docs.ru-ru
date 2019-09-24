---
title: Устранение неполадок c помощью управления обновлениями
description: Подробные сведения об устранении неполадок c помощью управления обновлениями
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 48d2463eee2caeaae36118bf736d00eed84c897a
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186223"
---
# <a name="troubleshooting-issues-with-update-management"></a>Устранение неполадок c помощью управления обновлениями

В этой статье рассматриваются решения по устранению неполадок, которые могут возникнуть, с использованием функции "Управление обновлениями".

Агент устранения неполадок определяет первоначальную проблему для агента гибридной рабочей роли. Для дополнительных сведений о средстве устранения неполадок см. [Общие сведения о результатах проверки агента в службе "Управление обновлениями"](update-agent-issues.md). Подробные сведения о других неполадках см. ниже.

## <a name="general"></a>Общее

### <a name="rp-register"></a>Сценарий. Не удалось зарегистрировать поставщик ресурсов службы автоматизации для подписок

#### <a name="issue"></a>Проблемы

При работе с решениями в учетной записи службы автоматизации может появиться следующее сообщение об ошибке.

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

#### <a name="cause"></a>Причина:

Поставщик ресурсов автоматизации не зарегистрирован в подписке.

#### <a name="resolution"></a>Разрешение

Поставщики ресурсов службы автоматизации можно зарегистрировать, выполнив следующие действия в портал Azure.

1. Щелкните **Все службы** внизу списка служб Azure и выберите **Подписки** в группе служб _Общие_.
2. Выберите свою подписку.
3. В разделе _Параметры_щелкните **поставщики ресурсов** .
4. В списке поставщиков ресурсов проверьте, зарегистрирован ли поставщик ресурсов **Microsoft. Automation** .
5. Если поставщик отсутствует в списке, зарегистрируйте поставщик **Microsoft.Automation** , выполнив действия, описанные в разделе [](/azure/azure-resource-manager/resource-manager-register-provider-errors).

### <a name="mw-exceeded"></a>Сценарий. Запланированное обновление управления обновлениями завершилось с ошибкой Маинтенанцевиндовексцеедед

#### <a name="issue"></a>Проблемы

Период обслуживания по умолчанию для обновлений составляет 120 минут. Период обслуживания можно увеличить до шести (6) часов или 360 минут.

#### <a name="resolution"></a>Разрешение

Изменение всех неудачных развертываний обновлений и увеличение периода обслуживания.

Дополнительные сведения о периодах обслуживания см. в разделе [install updates](../automation-update-management.md#install-updates).

### <a name="components-enabled-not-working"></a>Сценарий. Компоненты для решения "Управление обновлениями" включены, и теперь эта виртуальная машина настраивается

#### <a name="issue"></a>Проблемы

Приведенное ниже сообщение на виртуальной машине не исчезает в течение 15 минут после подключения:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>Причина:

Ошибка может быть вызвана следующими причинами.

1. Обмен данными в учетной записи службы автоматизации заблокирован.
2. Подключенная виртуальная машина, возможно, поступила с клонированного компьютера, необработанного командой Sysprep с помощью установленного Microsoft Monitoring Agent.

#### <a name="resolution"></a>Разрешение

1. Дополнительные сведения о том, какие адреса и порты должны быть разрешены, чтобы управление обновлениями работало, см. в статье [Автоматизация ресурсов в центре обработки данных или в облаке с помощью использования гибридной рабочей роли Runbook](../automation-hybrid-runbook-worker.md#network-planning).
2. Если используется клонированный образ:
   1. В рабочей области log Analytics Удалите виртуальную машину из сохраненного поиска для конфигурации `MicrosoftDefaultScopeConfig-Updates` области, если она отображается. Сохраненные поисковые запросы хранятся на вкладке **Общие** рабочей области.
   2. Выполнить `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`
   3. Выполните `Restart-Service HealthService`, чтобы перезапустить `HealthService`. Будет повторно создан ключ, а также сгенерирован новый идентификатор UUID.
   4. Если это не сработает, сначала запустите образ Sysprep и установите агент MMA.

### <a name="multi-tenant"></a>Сценарий. Вы получаете ошибку связанной подписки при создании развертывания обновления для компьютеров в другом клиенте Azure.

#### <a name="issue"></a>Проблемы

Вы получаете следующую ошибку при попытке создания развертывания обновления для компьютеров в другом клиенте Azure:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

#### <a name="cause"></a>Причина:

Эта ошибка возникает при создании развертывания обновления с виртуальными машинами Azure, расположенными в другом клиенте.

#### <a name="resolution"></a>Разрешение

Необходимо использовать обходной путь, чтобы запланировать обновление этих виртуальных машин. Можно использовать командлет [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) с параметром `-ForUpdate`, чтобы создать расписание, а также применить командлет [New AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) и передать компьютеры, расположенные в другом клиенте, в параметр `-NonAzureComputer`. В приведенном ниже примере показано, как это сделать.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

### <a name="updates-nodeployment"></a>Сценарий. Обновления устанавливаются без развертывания

### <a name="issue"></a>Проблемы

При регистрации компьютера Windows в Управление обновлениями можно увидеть, что обновления будут устанавливаться без развертывания.

### <a name="cause"></a>Причина:

В Windows обновления устанавливаются автоматически, как только они становятся доступными. Это может вызвать путаницу, если вы не запланировали развертывание обновления на компьютере.

### <a name="resolution"></a>Разрешение

Раздел реестра Windows по умолчанию имеет значение 4, `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` — **Автоматическая загрузка и установка**.

Для Управление обновлениями клиентов рекомендуется задать для этого раздела значение "3" — **Автоматическая загрузка, но не автоматическая установка**.

Дополнительные сведения см. в разделе [настройка Автоматическое обновление](https://docs.microsoft.com/en-us/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

### <a name="nologs"></a>Сценарий. Компьютеры не отображаются на портале в разделе Управление обновлениями

#### <a name="issue"></a>Проблемы

Вы можете выполнять следующие сценарии:

* Компьютер **не настроен** из управление обновленияминого представления виртуальной машины

* Ваши компьютеры отсутствуют в Управление обновлениями представлении учетной записи службы автоматизации.

* У вас есть компьютеры, которые **не оцениваются** в соответствии с **требованиями**, но вы видите данные пульса в Azure Monitor журналов для гибридной рабочей роли Runbook, но не управление обновлениями.

#### <a name="cause"></a>Причина:

Это может быть вызвано потенциальными проблемами с локальной конфигурацией или неправильной настройкой области.

Возможно, нужно повторно зарегистрировать и установить гибридные рабочие роли Runbook.

Возможно, в рабочей области была определена квота, которая была достигнута и остановлена для хранения данных.

#### <a name="resolution"></a>Разрешение

* Убедитесь, что ваш компьютер сообщает о правильной рабочей области. Проверьте, на какую рабочую область подотчетна ваш компьютер. Инструкции по проверке см. в разделе [Проверка подключения агента к log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Убедитесь, что это Рабочая область, связанная с учетной записью службы автоматизации Azure. Чтобы подтвердить это, перейдите к учетной записи службы автоматизации и щелкните **связанная Рабочая область** в разделе **связанные ресурсы**.

* Убедитесь, что компьютеры отображаются в рабочей области Log Analytics. Выполните следующий запрос в рабочей области Log Analytics, которая связана с учетной записью службы автоматизации. Если вы не видите компьютер в результатах запроса, ваш компьютер не выполняет пульс, что означает наличие проблемы с локальной конфигурацией. Можно запустить средство устранения неполадок для [Windows](update-agent-issues.md#troubleshoot-offline) или [Linux](update-agent-issues-linux.md#troubleshoot-offline) в зависимости от операционной системы или можно [переустановить агент](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). Если компьютер отображается в результатах запроса, необходимо указать конфигурацию области, указанную в следующем маркере.

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```

* Проверьте наличие проблем с конфигурацией области. [Конфигурация области](../automation-onboard-solutions-from-automation-account.md#scope-configuration) определяет, какие компьютеры настраиваются для решения. Если компьютер отображается в рабочей области, но не отображается, необходимо настроить конфигурацию области для целевых устройств. Сведения о том, как это сделать, см. в статье подключение [компьютеров в рабочей области](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Если описанные выше действия не позволят решить проблему, выполните действия, описанные в разделе [развертывание гибридной рабочей роли Runbook Windows](../automation-windows-hrw-install.md) , чтобы переустановить гибридную рабочую роль для Windows или [развернуть гибридную рабочую роль Runbook Linux](../automation-linux-hrw-install.md) для Linux.

* В рабочей области выполните следующий запрос. Если вы видите результат `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` , у вас есть квота, определенная в рабочей области, которая была достигнута и остановила сохранение данных. В рабочей области перейдите к разделу **использование и оценочная стоимость** > **Управление томами данных** и проверьте квоту или удалите имеющуюся квоту.

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```

## <a name="windows"></a>Windows

Если при попытке подключить решение или виртуальную машину возникли проблемы, проверьте журнал событий **Operations Manager** в разделе **Журналы приложений и служб** на локальном компьютере для событий с идентификатором **4502**, а также сообщение события, содержащее **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**.

В разделе ниже выделены отдельные сообщения об ошибках и возможные решения этих ошибок. Для устранения других неполадок с подключением см. в разделе [Troubleshoot solution onboarding](onboarding.md) (Устранение неполадок при подключении).

### <a name="machine-already-registered"></a>Сценарий. Компьютер уже зарегистрирован в другой учетной записи

#### <a name="issue"></a>Проблемы

Отображается следующее сообщение об ошибке:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Причина:

Компьютер уже подключен к другой рабочей области для управления обновлениями.

#### <a name="resolution"></a>Разрешение

Выполните очистку старых артефактов на компьютере путем [удаления группы гибридных модулей Runbook](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) и повторите попытку.

### <a name="machine-unable-to-communicate"></a>Сценарий. Компьютер недоступен для взаимодействия со службой

#### <a name="issue"></a>Проблемы

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

#### <a name="cause"></a>Причина:

Возможно, прокси-сервер, шлюз или брандмауэр блокирует сетевую связь.

#### <a name="resolution"></a>Разрешение

Проверьте сеть и убедитесь, что необходимые порты и адреса разрешены. Раздел [Требования сети](../automation-hybrid-runbook-worker.md#network-planning) содержит информацию о том, как создать список портов и адресов, необходимых для управления обновлениями, и гибридные рабочие роли Runbook.

### <a name="unable-to-create-selfsigned-cert"></a>Сценарий. Невозможно создать самозаверяющий сертификат

#### <a name="issue"></a>Проблемы

Отображается одно из следующих сообщений об ошибке.

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Причина:

Гибридной рабочей роли Runbook не удалось создать самозаверяющий сертификат.

#### <a name="resolution"></a>Разрешение

Убедитесь, что системная учетная запись имеет доступ для чтения к папке **C:\ProgramData\Microsoft\Crypto\RSA** и повторите попытку.

### <a name="failed-to-start"></a>Сценарий. Не удалось запустить компьютер в развертывании обновления

#### <a name="issue"></a>Проблемы

Состояние компьютера **не удалось запустить** для компьютера. При просмотре сведений о компьютере отображается следующая ошибка:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

#### <a name="cause"></a>Причина:

Эта ошибка может возникать по одной из следующих причин.

* Компьютер больше не существует.
* Компьютер отключен и недоступен.
* Компьютер имеет неполадки с сетевым подключением, и гибридная Рабочая роль на компьютере недоступна.
* Произошло обновление Microsoft Monitoring Agent, в котором изменился Саурцекомпутерид
* Возможно, выполнение обновления было отрегулировано при достижении ограничения параллельных заданий 2 000 в учетной записи службы автоматизации. Каждое развертывание считается заданием и каждый компьютер в составе развертывания обновлений в качестве задания. Любое другое задание службы автоматизации или развертывание обновления, выполняющееся в учетной записи службы автоматизации, предельное число параллельных заданий.

#### <a name="resolution"></a>Разрешение

При необходимости используйте [динамические группы](../automation-update-management.md#using-dynamic-groups) для развертываний обновлений.

* Убедитесь, что компьютер по-прежнему существует и доступен. Если она не существует, измените развертывание и удалите компьютер.
* Список портов и адресов, необходимых для Управление обновлениями, и проверки компьютера на соответствие этим требованиям см. в разделе о [планировании сети](../automation-update-management.md#ports) .
* Выполните следующий запрос в log Analytics, чтобы найти компьютеры в вашей среде, `SourceComputerId` которые изменились. Найдите компьютеры с одинаковым `Computer` значением, но разными `SourceComputerId` значениями. После обнаружения затронутых компьютеров необходимо изменить развертывания обновлений, предназначенные для этих компьютеров, а также удалить и повторно добавить компьютеры, чтобы `SourceComputerId` они отражали правильное значение.

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

### <a name="hresult"></a>Сценарий. Компьютер отображается как "Без оценки" и показывает исключение HResult

#### <a name="issue"></a>Проблемы

У вас есть машины, которые отображаются как **Без оценки** под пунктом **Совместимость**, и ниже появляется сообщение об ошибке.

#### <a name="cause"></a>Причина:

Центр обновления Windows или Windows Server Update Services неправильно настроены на компьютере. Решение "Управление обновлениями" зависит от Центра обновлений Windows или от служб WSUS для предоставления необходимых обновлений, состояния исправления и результатов развернутых исправлений. Без этой информации решение "Управление обновлениями" не сможет должным образом предоставлять отчеты об исправлениях, которые требуется установить или которые уже установлены.

#### <a name="resolution"></a>Разрешение

Чтобы просмотреть целиком сообщение об исключении, дважды щелкните на исключение, выделенное красным. Ознакомьтесь со следующей таблицей для выполнения потенциальных решений или действий.

|Исключение  |Разрешение или действие  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Найдите соответствующий код ошибки в [списке кодов ошибок обновления Windows](https://support.microsoft.com/help/938205/windows-update-error-code-list), чтобы узнать о причине исключения.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Проблемы с сетевым подключением. Убедитесь, что компьютер имеет правильное сетевое подключение для "Управление обновлениями". См. в разделе [порты](../automation-update-management.md#ports) список обязательных портов и адресов.        |
|`0x8024001E`| Операция обновления не была завершена из-за завершения работы службы или системы.|
|`0x8024002E`| Служба Центр обновления Windows отключена.|
|`0x8024402C`     | Используя сервер WSUS, убедитесь, что в разделе реестра `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` значение реестра `WUServer` и `WUStatusServer` имеет правильный WSUS-сервер.        |
|`0x80072EE2`|Проблемы с сетевым подключением или обращение к настроенному серверу WSUS. Проверьте параметры WSUS и убедитесь, что они доступны с клиента.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Убедитесь, что служба Центр обновления Windows (wuauserv) работает и не отключена.        |
|Другое универсальное исключение     | Выполните поиск возможных решений и работы с локальной ИТ-поддержкой в Интернете.         |

`windowsupdate.log` Проверка может помочь вам определить возможную причину. Дополнительные сведения о том, как прочитать журнал, см. в разделе [чтение файла windowsupdate. log](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Кроме того, можно загрузить и запустить [средство устранения неполадок Центра обновления Windows](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) для проверки возможных проблем с обновлениями Windows на компьютере.

> [!NOTE]
> [Средство устранения неполадок Центра обновления Windows](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) предназначено для ОС Windows, но также работает в Windows Server.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Сценарий. Сбой запуска обновления

#### <a name="issue"></a>Проблемы

Сбой запуска обновления на компьютере Linux.

#### <a name="cause"></a>Причина:

Гибридная рабочая роль Linux неработоспособна.

#### <a name="resolution"></a>Разрешение

Создайте копию в следующем файле журнала и сохранить его для устранения неполадок.

```bash
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Сценарий. Обновление запускается, но возникают ошибки

#### <a name="issue"></a>Проблемы

Запуск обновления начинается, но возникают ошибки во время выполнения.

#### <a name="cause"></a>Причина:

Возможные причины:

* диспетчер пакетов неработоспособен;
* определенные пакеты могут конфликтовать с установкой исправлений облака;
* Другие причины

#### <a name="resolution"></a>Разрешение

Если сбои происходят во время запуска обновления после того, как он успешно запущен в Linux, проверьте выполнение задания на затронутом компьютере в ходе выполнения. Просмотрите конкретные сообщения об ошибках из диспетчера пакетов вашего компьютера и примите меры. Для успешного обновления развертываний управлению обновлениями необходима работоспособность диспетчера пакетов.

В некоторых случаях пакет обновления может повлиять на предотвращение завершения развертывания обновлений в управлении обновлениями. Если это происходит, необходимо или исключить эти пакеты из запусков будущих обновлений или установить их самостоятельно вручную.

Если не удается это исправить, создайте копию в следующем файле журнала и сохраните его **перед** началом следующего развертывания обновления для устранения неполадок.

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-are-not-installed"></a>Исправления не установлены

### <a name="machines-do-not-install-updates"></a>Компьютеры не устанавливают обновления

* Попробуйте выполнить обновление непосредственно на компьютере. Если не удается обновить программное обеспечение на компьютере, см. [список возможных ошибок в руководстве по устранению неполадок](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).
* Если обновления выполняются локально, попробуйте удалить и переустановить агент на компьютере, следуя инструкциям из раздела [Удаление виртуальной машины для Управления обновлениями](https://docs.microsoft.com/azure/automation/automation-update-management#remove-a-vm-from-update-management).

### <a name="i-know-updates-are-available-but-they-dont-show-as-needed-on-my-machines"></a>Я знал, что доступны обновления, но они не отображаются по необходимости на моих компьютерах

* Это часто происходит, если компьютеры настроены для получения обновлений из Windows Server Update Services (WSUS) или System Center Configuration Manager (SCCM), но WSUS или SCCM не утвердили обновления.
* Проверить, настроены ли компьютеры на получение обновлений из WSUS или SCCM можно с помощью [перекрестной ссылки между разделом реестра UseWUServer и разделами реестра, упомянутыми в разделе "Настройка автоматического обновления путем редактирования реестра" этого документа](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>**Обновления отображаются как установленные, но найти их на компьютере не удается**

* Обновления часто заменяются другими обновлениями. Дополнительные сведения см. в описании причины ["Обновление заменено" в руководстве по устранению неполадок Центра обновлений Windows](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer).

### <a name="installing-updates-by-classification-on-linux"></a>**Установка обновлений с учетом классификации на платформе Linux**

* При развертывании обновлений на Linux с учетом классификации (критические обновления и обновления системы безопасности) следует учитывать некоторые важные ограничения, особенно для CentOS. Эти [ограничения описаны на странице с общими сведениями об управлении обновлениями](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2).

### <a name="kb2267602-is-consistently--missing"></a>**Статья базы знаний № 2267602 постоянно отсутствует**

* Статья базы знаний № 2267602 касается [обновления определений Защитника Windows](https://www.microsoft.com/wdsi/definitions). Она обновляются ежедневно.

## <a name="other"></a>Сценарий. Моя проблема не указана в этой статье

### <a name="issue"></a>Проблемы

У вас возникла проблема, которая не устранена в других сценариях.

### <a name="cause"></a>Причина:

Неправильно настроенные или отсутствующие разделы реестра могут вызвать проблемы с Управление обновлениями.

### <a name="resolution"></a>Разрешение

Удалите раздел `HKLM:\SOFTWARE\Microsoft\HybridRunbookWorker` реестра и перезапустите **HealthService**.

Можно также использовать следующие команды PowerShell.

```powershell
Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
Restart-Service healthservice
```

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.
