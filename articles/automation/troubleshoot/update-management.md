---
title: Устранение неполадок c помощью управления обновлениями
description: Узнайте, как устранять неполадки с Управление обновлениями.
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 952bcb85484e885d45876de1e4cf3326db0a146a
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693390"
---
# <a name="troubleshooting-issues-with-update-management"></a>Устранение неполадок c помощью управления обновлениями

В этой статье рассматриваются решения проблем, которые могут возникнуть при использовании Управление обновлениями.

Существует средство устранения неполадок агента для агента гибридной рабочей роли, чтобы определить базовую проблему. Для дополнительных сведений о средстве устранения неполадок см. [Общие сведения о результатах проверки агента в службе "Управление обновлениями"](update-agent-issues.md). Для устранения других проблем используйте следующие рекомендации по устранению неполадок.

Если при попытке подключить решение к виртуальной машине возникли проблемы, проверьте журнал **Operations Manager** в разделе **журналы приложений и служб** на локальном компьютере на наличие событий с идентификатором 4502 и сведения о событии, содержащие **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent**.

В следующем разделе описываются конкретные сообщения об ошибках и возможные способы их устранения. Сведения о других проблемах подключения см. в статье [Устранение неполадок при адаптации решения](onboarding.md).

## <a name="nologs"></a>Сценарий: компьютеры не отображаются на портале в разделе Управление обновлениями

### <a name="issue"></a>Проблема

При этом возникают следующие признаки.

* Компьютер **не настроен** из управление обновленияминого представления виртуальной машины.

* Ваши компьютеры отсутствуют в Управление обновлениями представлении учетной записи службы автоматизации Azure.

* У вас есть компьютеры, которые **не оцениваются** в соответствии с **требованиями**. Однако вы видите данные пульса в журналах Azure Monitor для гибридной рабочей роли Runbook, но не для Управление обновлениями.

### <a name="cause"></a>Причина:

Эта проблема может быть вызвана проблемами локальной конфигурации или неправильно настроенной конфигурацией области.

Возможно, потребуется повторно зарегистрировать и переустановить гибридную рабочую роль Runbook.

Возможно, вы определили квоту в рабочей области, которая была достигнута, и это помешает дополнительному хранению данных.

### <a name="resolution"></a>Разрешение

* Запустите средство устранения неполадок для [Windows](update-agent-issues.md#troubleshoot-offline) или [Linux](update-agent-issues-linux.md#troubleshoot-offline)в зависимости от операционной системы.

* Убедитесь, что компьютер сообщает о правильной рабочей области. Инструкции по проверке этого аспекта см. в разделе [Проверка подключения агента к log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Также убедитесь, что эта Рабочая область связана с учетной записью службы автоматизации Azure. Для подтверждения перейдите в учетную запись службы автоматизации и выберите **связанная Рабочая область** в разделе **связанные ресурсы**.

* Убедитесь, что компьютеры отображаются в рабочей области Log Analytics. Выполните следующий запрос в рабочей области Log Analytics, связанной с учетной записью службы автоматизации:

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```
  Если компьютер не отображается в результатах запроса, он не был недавно возвращен. Это означает, что, возможно, возникла ошибка локальной конфигурации, и необходимо [переустановить агент](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). Если компьютер отображается в результатах запроса, необходимо проверить конфигурацию области, указанную в следующем маркированном элементе этого списка.

* Проверьте наличие проблем с конфигурацией области. [Конфигурация области](../automation-onboard-solutions-from-automation-account.md#scope-configuration) определяет, какие компьютеры настраиваются для решения. Если компьютер отображается в рабочей области, но не на портале **Управление обновлениями** , необходимо настроить конфигурацию области для целевых устройств. Сведения о том, как это сделать, см. в статье подключение [компьютеров в рабочей области](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* В рабочей области выполните следующий запрос:

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```
  Если полученный результат `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota`, то в рабочей области будет определена квота, которая остановила сохранение данных. В рабочей области выберите **использование и оценочные затраты**  > **Управление объемом данных** и проверьте квоту или удалите ее.

* Если эти действия не помогли устранить проблему, выполните действия, описанные в статье [развертывание гибридной рабочей роли Runbook Windows](../automation-windows-hrw-install.md) , чтобы переустановить гибридную рабочую роль для Windows. Или для Linux [разверните гибридную рабочую роль Runbook Linux](../automation-linux-hrw-install.md).

## <a name="rp-register"></a>Сценарий: не удалось зарегистрировать поставщик ресурсов службы автоматизации для подписок

### <a name="issue"></a>Проблема

При работе с решениями в учетной записи службы автоматизации возникает следующая ошибка:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Причина:

Поставщик ресурсов службы автоматизации не зарегистрирован в подписке.

### <a name="resolution"></a>Разрешение

Чтобы зарегистрировать поставщик ресурсов службы автоматизации, выполните следующие действия в портал Azure.

1. В списке служб Azure в нижней части портала выберите **все службы**, а затем щелкните **подписки** в группе "Общие".
2. Выберите свою подписку.
3. В разделе **Параметры**выберите **поставщики ресурсов**.
4. Убедитесь, что в списке поставщиков ресурсов зарегистрирован поставщик ресурсов **Microsoft. Automation** .
5. Если он отсутствует в списке, зарегистрируйте поставщик **Microsoft. Automation** , выполнив действия, описанные в разделе [Устранение ошибок для регистрации поставщика ресурсов](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="components-enabled-not-working"></a>Сценарий: компоненты решения Управление обновлениями включены, и теперь выполняется настройка этой виртуальной машины

### <a name="issue"></a>Проблема

Приведенное ниже сообщение на виртуальной машине не исчезает в течение 15 минут после подключения:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Причина:

Эта ошибка может возникать по следующим причинам.

- Связь с учетной записью службы автоматизации блокируется.
- Подключаемая виртуальная машина может быть взята с клонированного компьютера, который не Sysprep с установленным Microsoft Monitoring Agent (MMA).

### <a name="resolution"></a>Разрешение

1. Перейдите к разделу [планирование сети](../automation-hybrid-runbook-worker.md#network-planning) , чтобы узнать, какие адреса и порты должны быть разрешены для работы Управление обновлениями.
2. Если вы используете клонированное изображение:
   1. В рабочей области Log Analytics Удалите виртуальную машину из сохраненного поиска для конфигурации области `MicrosoftDefaultScopeConfig-Updates`, если она отображается. Сохраненные поисковые запросы хранятся на вкладке **Общие** рабочей области.
   2. Запустите `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`.
   3. Выполните `Restart-Service HealthService`, чтобы перезапустить `HealthService`. При этом создается ключ и создается новый UUID.
   4. Если такой подход не работает, сначала запустите Sysprep на образе, а затем установите MMA.

## <a name="multi-tenant"></a>Сценарий: при создании развертывания обновлений для компьютеров в другом клиенте Azure возникает ошибка связанной подписки

### <a name="issue"></a>Проблема

При попытке создать развертывание обновления для компьютеров в другом клиенте Azure возникает следующая ошибка:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Причина:

Эта ошибка возникает при создании развертывания обновлений с виртуальными машинами Azure в другом клиенте, который входит в развертывание обновлений.

### <a name="resolution"></a>Разрешение

Чтобы получить запланированные элементы, используйте следующее решение. Для создания расписания можно использовать командлет [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) с параметром `-ForUpdate`. Затем используйте командлет [New-азурермаутоматионсофтвареупдатеконфигуратион](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) и передайте компьютеры в другом клиенте в параметр `-NonAzureComputer`. В приведенном ниже примере показано, как это сделать.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="node-reboots"></a>Сценарий: необъясненные перезагрузки

### <a name="issue"></a>Проблема

Несмотря на то, что параметр **управления перезагрузкой** имеет значение **никогда не**перезагружаться, компьютеры по-прежнему перезапускаются после установки обновлений.

### <a name="cause"></a>Причина:

Центр обновления Windows могут быть изменены несколькими разделами реестра, с помощью которых можно изменить поведение при перезагрузке.

### <a name="resolution"></a>Разрешение

Изучите разделы реестра, перечисленные в разделе [настройка Автоматическое обновление, изменив реестр](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-rej7uijui7jgistry) и [разделы реестра, используемые для управления перезагрузкой](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) , чтобы убедиться, что компьютеры настроены должным образом.

## <a name="failed-to-start"></a>Сценарий: на компьютере отображается сообщение "не удалось запустить" в развертывании обновлений

### <a name="issue"></a>Проблема

На компьютере отображается состояние " **не удалось запустить** ". При просмотре конкретных сведений о компьютере отображается следующая ошибка:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Причина:

Ошибка может быть вызвана одной из следующих причин:

* Компьютер больше не существует.
* Компьютер отключен и недоступен.
* Компьютер имеет неполадки с сетевым подключением, поэтому Гибридная Рабочая роль на компьютере недоступна.
* Произошло обновление для MMA, которое изменило Саурцекомпутерид.
* Выполнение обновления было отрегулировано, если достигнуто ограничение в 2 000 параллельных заданий в учетной записи службы автоматизации. Каждое развертывание считается заданием, а каждый компьютер в развертывании обновлений учитывается как задание. Любое другое задание службы автоматизации или развертывание обновления, выполняющееся в вашей учетной записи службы автоматизации, учитывает ограничение на число параллельных заданий.

### <a name="resolution"></a>Разрешение

При необходимости используйте [динамические группы](../automation-update-management-groups.md) для развертываний обновлений. Дополнительного

* Убедитесь, что компьютер по-прежнему существует и доступен. Если он не существует, измените развертывание и удалите компьютер.
* Список портов и адресов, необходимых для Управление обновлениями, см. в разделе [планирование сети](../automation-update-management.md#ports) , а затем убедитесь, что компьютер соответствует этим требованиям.
* Выполните следующий запрос в Log Analytics, чтобы найти компьютеры в среде, чьи `SourceComputerId` изменились. Найдите компьютеры с одинаковым значением `Computer`, но другим значением `SourceComputerId`. 

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```
   После обнаружения затронутых компьютеров измените развертывания обновлений, предназначенные для этих компьютеров, а затем удалите и добавьте их повторно, чтобы `SourceComputerId` отражали правильное значение.

## <a name="updates-nodeployment"></a>Сценарий: обновления устанавливаются без развертывания

### <a name="issue"></a>Проблема

При регистрации компьютера Windows в Управление обновлениями вы увидите обновления, установленные без развертывания.

### <a name="cause"></a>Причина:

В Windows обновления устанавливаются автоматически, как только они станут доступны. Такое поведение может вызвать путаницу, если вы не запланировали развертывание обновления на компьютере.

### <a name="resolution"></a>Разрешение

Раздел реестра `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` по умолчанию имеет значение 4: **Автоматическая загрузка и установка**.

Для Управление обновлениями клиентов рекомендуется задать для этого раздела значение 3: **Автоматическая загрузка, но не автоматическая установка**.

Дополнительные сведения см. в разделе [настройка Автоматическое обновление](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="machine-already-registered"></a>Сценарий: компьютер уже зарегистрирован на другой учетной записи

### <a name="issue"></a>Проблема

Отображается следующее сообщение об ошибке:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Причина:

Компьютер уже подключен к другой рабочей области для Управление обновлениями.

### <a name="resolution"></a>Разрешение

1. Выполните действия, описанные в разделе Компьютеры, которые [не отображаются на портале, в разделе Управление обновлениями](#nologs) , чтобы убедиться, что компьютер сообщает о правильной рабочей области.
2. Очистите старые артефакты на компьютере, [удалив гибридную группу Runbook](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group), и повторите попытку.

## <a name="machine-unable-to-communicate"></a>Сценарий: компьютеру не удается связаться со службой

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

### <a name="cause"></a>Причина:

Прокси-сервер, шлюз или брандмауэр могут блокировать сетевую связь.

### <a name="resolution"></a>Разрешение

Проверьте сеть и убедитесь, что разрешены соответствующие порты и адреса. Список портов и адресов, необходимых для Управление обновлениями и гибридных рабочих ролей Runbook, см. в статье [требования к сети](../automation-hybrid-runbook-worker.md#network-planning) .

## <a name="unable-to-create-selfsigned-cert"></a>Сценарий: не удалось создать самозаверяющий сертификат

### <a name="issue"></a>Проблема

Отображается одно из следующих сообщений об ошибке.

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Причина:

Гибридной рабочей роли Runbook не удалось создать самозаверяющий сертификат.

### <a name="resolution"></a>Разрешение

Убедитесь, что учетная запись System имеет доступ на чтение к папке **к:\програмдата\микрософт\крипто\рса** , и повторите попытку.

## <a name="mw-exceeded"></a>Сценарий: сбой запланированного обновления с ошибкой Маинтенанцевиндовексцеедед

### <a name="issue"></a>Проблема

Период обслуживания по умолчанию для обновлений составляет 120 минут. Период обслуживания можно увеличить до 6 часов или 360 минут.

### <a name="resolution"></a>Разрешение

Изменение всех неудачных развертываний обновлений и увеличение периода обслуживания.

Дополнительные сведения о периодах обслуживания см. в разделе [install updates](../automation-tutorial-update-management.md#schedule-an-update-deployment).

## <a name="hresult"></a>Сценарий: компьютер отображается как "не оценено" и отображает исключение HResult

### <a name="issue"></a>Проблема

* У вас есть машины, которые отображаются как **Без оценки** под пунктом **Совместимость**, и ниже появляется сообщение об ошибке.
* У вас есть компьютеры, не имеющие оценки.
* На портале отобразится код ошибки HRESULT.

### <a name="cause"></a>Причина:

Агент обновления (агент Центр обновления Windows в Windows; Диспетчер пакетов для дистрибутива Linux) настроен неправильно. Управление обновлениями полагается на агент обновления компьютера, чтобы предоставить необходимые обновления, состояние исправления и результаты развернутых исправлений. Без этих сведений Управление обновлениями не может правильно сообщить о необходимых или установленных исправлениях.

### <a name="resolution"></a>Разрешение

Попробуйте выполнить обновления локально на компьютере. Если это не удается, это обычно означает ошибку конфигурации агента обновления.

Эта проблема часто возникает из-за проблем с конфигурацией сети и брандмауэром. Попробуйте сделать следующее.

* Для Linux проверьте соответствующую документацию, чтобы убедиться, что вы можете связаться с конечной точкой сети репозитория пакетов.
* Для Windows проверьте конфигурацию агента, указанную в списке [обновления, не скачиваются из конечной точки интрасети (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).
  * Если компьютеры настроены для Центр обновления Windows, убедитесь, что вы можете связаться с конечными точками, описанными в разделе [проблемы, связанные с HTTP/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Если компьютеры настроены для Windows Server Update Services (WSUS), убедитесь, что вы можете подключиться к серверу WSUS, настроенному с помощью [раздела реестра WUServer](/windows/deployment/update/waas-wu-settings).

Если отображается значение HRESULT, дважды щелкните исключение, отображаемое красным цветом, чтобы просмотреть сообщение об исключении целиком. Ознакомьтесь со следующей таблицей, чтобы получить возможные решения или Рекомендуемые действия.

|Исключение  |Разрешение или действие  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Найдите соответствующий код ошибки в [списке кодов ошибок Центра обновления Windows](https://support.microsoft.com/help/938205/windows-update-error-code-list) , чтобы найти дополнительные сведения о причине исключения.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Они указывают на проблемы с сетевым подключением. Убедитесь, что компьютер имеет сетевое подключение к Управление обновлениями. Список требуемых портов и адресов см. в разделе [планирование сети](../automation-update-management.md#ports) .        |
|`0x8024001E`| Операция обновления не завершена из-за завершения работы службы или системы.|
|`0x8024002E`| Служба Центр обновления Windows отключена.|
|`0x8024402C`     | Если вы используете сервер WSUS, убедитесь, что параметры реестра для `WUServer` и `WUStatusServer` в разделе реестра `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` укажите правильный сервер WSUS.        |
|`0x80072EE2`|Возникла неполадка с сетевым подключением или возникла неполадка при взаимодействии с настроенным сервером WSUS. Проверьте параметры WSUS и убедитесь, что служба доступна с клиента.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Убедитесь, что служба Центр обновления Windows (wuauserv) запущена и не отключена.        |
|Другое универсальное исключение     | Выполните поиск в Интернете, чтобы получить возможные решения, и поработайте с локальной ИТ-поддержкой.         |

Просмотр файла windowsupdate. log также может помочь определить возможные причины. Дополнительные сведения о чтении журнала см. в разделе [чтение файла windowsupdate. log](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Кроме того, можно загрузить и запустить [средство устранения неполадок центр обновления Windows](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) , чтобы проверить наличие проблем с центр обновления Windows на компьютере.

> [!NOTE]
> Документация по [устранению неполадок центр обновления Windows](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) указывает на то, что она предназначена для использования на клиентах Windows, но она также работает в Windows Server.

## <a name="scenario-update-run-returns-failed-status"></a>Сценарий: запуск обновления возвращает состояние "сбой"

### <a name="issue"></a>Проблема

Запуск обновления начинается, но во время выполнения возникают ошибки.

### <a name="cause"></a>Причина:

Возможные причины:

* Диспетчер пакетов неработоспособен.
* Агент обновления (WUA для Windows, дистрибутив для диспетчера пакетов для Linux) неправильно настроен.
* Определенные пакеты мешают установке исправлений на основе облака.
* Компьютер недоступен.
* Обновления имели зависимости, которые не были разрешены.

### <a name="resolution"></a>Разрешение

Если сбои возникают во время выполнения обновления после его успешного запуска, [Проверьте выходные данные задания](../manage-update-multi.md#view-results-of-an-update-deployment) на затронутом компьютере в ходе выполнения. Вы можете найти определенные сообщения об ошибках на ваших компьютерах, которые можно исследовать, и предпринять соответствующие действия. Для успешного обновления развертываний управлению обновлениями необходима работоспособность диспетчера пакетов.

Если конкретные исправления, пакеты или обновления отображаются непосредственно перед сбоем задания, можно попытаться [исключить](../automation-tutorial-update-management.md#schedule-an-update-deployment) их из следующего развертывания обновления. Сведения о сборе данных журнала из Центр обновления Windows см. в разделе [Центр обновления Windows файлы журнала](/windows/deployment/update/windows-update-logs).

Если не удается устранить проблему с исправлением, создайте копию следующего файла журнала и сохраните ее для устранения неполадок *перед* запуском следующего развертывания обновления:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-arent-installed"></a>Исправления не установлены

### <a name="machines-dont-install-updates"></a>Компьютеры не устанавливают обновления

* Попробуйте выполнить обновление непосредственно на компьютере. Если компьютеру не удается применить обновления, просмотрите [список потенциальных ошибок в разделе Руководство по устранению неполадок](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).
* Если обновления выполняются локально, попробуйте удалить и переустановить агент на компьютере, следуя указаниям в статье [Удаление виртуальной машины из Управление обновлениями](https://docs.microsoft.com/azure/automation/automation-update-management#remove-a-vm-from-update-management).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Я знал, что доступны обновления, но они не отображаются как доступные на моих компьютерах

* Это часто происходит, если компьютеры настроены для получения обновлений от WSUS или System Center Configuration Manager (SCCM), но WSUS и SCCM еще не утвердили обновления.
* Чтобы проверить, настроены ли на компьютерах службы WSUS и SCCM, [перекрестно ссылаться на раздел реестра UseWUServer на разделы реестра в разделе "настройка Автоматическое обновление путем изменения реестра" этой статьи](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).
* Если обновления не утверждены в WSUS, они не будут установлены. Вы можете проверить неодобренные обновления в Log Analytics, выполнив следующий запрос:

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Обновления отображаются как установленные, но не удается найти их на моем компьютере

* Обновления часто заменяются другими обновлениями. Дополнительные сведения см. в разделе [Обновление заменяется](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) в центр обновления Windows руководство по устранению неполадок.

### <a name="installing-updates-by-classification-on-linux"></a>Установка обновлений с учетом классификации на платформе Linux

* При развертывании обновлений на Linux с учетом классификации (критические обновления и обновления системы безопасности) следует учитывать некоторые важные ограничения, особенно для CentOS. Эти ограничения описаны на [странице обзор Управление обновлениями](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 постоянно отсутствует

* Статья базы знаний № 2267602 касается [обновления определений Защитника Windows](https://www.microsoft.com/wdsi/definitions). Он обновляется ежедневно.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не нашли проблему или не можете решить проблему, воспользуйтесь одним из следующих каналов для получения дополнительной поддержки:

* Получите ответы от экспертов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключайтесь с помощью [@AzureSupport](https://twitter.com/azuresupport), официальной учетной записи Microsoft Azure для улучшения качества работы пользователей, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.
* Отправьте запрос в службу поддержки Azure Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.
