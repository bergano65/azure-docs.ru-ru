---
title: Устранение неполадок c помощью управления обновлениями
description: Подробные сведения об устранении неполадок c помощью управления обновлениями
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 22e3ea1c90946902fc2a16d947ff2884e5e0a44b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60597624"
---
# <a name="troubleshooting-issues-with-update-management"></a>Устранение неполадок c помощью управления обновлениями

В этой статье рассматриваются решения по устранению неполадок, которые могут возникнуть, с использованием функции "Управление обновлениями".

Агент устранения неполадок определяет первоначальную проблему для агента гибридной рабочей роли. Для дополнительных сведений о средстве устранения неполадок см. [Общие сведения о результатах проверки агента в службе "Управление обновлениями"](update-agent-issues.md). Подробные сведения о других неполадках см. ниже.

## <a name="general"></a>Общие сведения

### <a name="components-enabled-not-working"></a>Сценарий. Компоненты для решения "Управление обновлениями" включены, и теперь эта виртуальная машина настраивается

#### <a name="issue"></a>Проблема

Приведенное ниже сообщение на виртуальной машине не исчезает в течение 15 минут после подключения:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>Причина:

Ошибка может быть вызвана следующими причинами.

1. Обмен данными в учетной записи службы автоматизации заблокирован.
2. Подключенная виртуальная машина, возможно, поступила с клонированного компьютера, необработанного командой Sysprep с помощью установленного Microsoft Monitoring Agent.

#### <a name="resolution"></a>Способы устранения:

1. Дополнительные сведения о том, какие адреса и порты должны быть разрешены, чтобы управление обновлениями работало, см. в статье [Автоматизация ресурсов в центре обработки данных или в облаке с помощью использования гибридной рабочей роли Runbook](../automation-hybrid-runbook-worker.md#network-planning).
2. Если используется клонированный образ:
   1. В рабочей области Log Analytics, необходимо удалить виртуальную Машину из сохраненного поиска для конфигурации области `MicrosoftDefaultScopeConfig-Updates` если он отображается. Сохраненные поисковые запросы хранятся на вкладке **Общие** рабочей области.
   2. Запустите `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`
   3. Выполните `Restart-Service HealthService`, чтобы перезапустить `HealthService`. Будет повторно создан ключ, а также сгенерирован новый идентификатор UUID.
   4. Если это не сработает, sysprep изображение первого и установите агент MMA за прошедшие периоды.

### <a name="multi-tenant"></a>Сценарий. Вы получаете ошибку связанной подписки при создании развертывания обновления для компьютеров в другом клиенте Azure.

#### <a name="issue"></a>Проблема

Вы получаете следующую ошибку при попытке создания развертывания обновления для компьютеров в другом клиенте Azure:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

#### <a name="cause"></a>Причина:

Эта ошибка возникает при создании развертывания обновления с виртуальными машинами Azure, расположенными в другом клиенте.

#### <a name="resolution"></a>Способы устранения:

Необходимо использовать обходной путь, чтобы запланировать обновление этих виртуальных машин. Можно использовать командлет [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) с параметром `-ForUpdate`, чтобы создать расписание, а также применить командлет [New AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) и передать компьютеры, расположенные в другом клиенте, в параметр `-NonAzureComputer`. В приведенном ниже примере показано, как это сделать.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

### <a name="nologs"></a>Сценарий. Обновление данных управления, не отображаются в журналах Azure Monitor для машины

#### <a name="issue"></a>Проблема

У вас есть компьютеры, которые показывают, как **без оценки** под **соответствия**, но вы видите данные пульса в журналах Azure Monitor для гибридной рабочей роли Runbook, но не для управления обновлениями.

#### <a name="cause"></a>Причина:

Возможно, нужно повторно зарегистрировать и установить гибридные рабочие роли Runbook.

#### <a name="resolution"></a>Способы устранения:

Следуйте инструкциям статьи [Развертывание гибридной рабочей роли Runbook для Windows](../automation-windows-hrw-install.md), чтобы переустановить гибридную рабочую роль для Windows, или статьи [Развертывание гибридной рабочей роли Runbook для Linux](../automation-linux-hrw-install.md) — для Linux.

## <a name="windows"></a> Windows

Если при попытке подключить решение или виртуальную машину возникли проблемы, проверьте журнал событий **Operations Manager** в разделе **Журналы приложений и служб** на локальном компьютере для событий с идентификатором **4502**, а также сообщение события, содержащее **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**.

В разделе ниже выделены отдельные сообщения об ошибках и возможные решения этих ошибок. Для устранения других неполадок с подключением см. в разделе [Troubleshoot solution onboarding](onboarding.md) (Устранение неполадок при подключении).

### <a name="machine-already-registered"></a>Сценарий. Компьютер уже зарегистрирован в другой учетной записи

#### <a name="issue"></a>Проблема

Отображается следующее сообщение об ошибке:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Причина:

Компьютер уже подключен к другой рабочей области для управления обновлениями.

#### <a name="resolution"></a>Способы устранения:

Выполните очистку старых артефактов на компьютере путем [удаления группы гибридных модулей Runbook](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) и повторите попытку.

### <a name="machine-unable-to-communicate"></a>Сценарий. Компьютер недоступен для взаимодействия со службой

#### <a name="issue"></a>Проблема

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

#### <a name="resolution"></a>Способы устранения:

Проверьте сеть и убедитесь, что необходимые порты и адреса разрешены. Раздел [Требования сети](../automation-hybrid-runbook-worker.md#network-planning) содержит информацию о том, как создать список портов и адресов, необходимых для управления обновлениями, и гибридные рабочие роли Runbook.

### <a name="unable-to-create-selfsigned-cert"></a>Сценарий. Невозможно создать самозаверяющий сертификат

#### <a name="issue"></a>Проблема

Отображается одно из следующих сообщений об ошибке.

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Причина:

Гибридной рабочей роли Runbook не удалось создать самозаверяющий сертификат.

#### <a name="resolution"></a>Способы устранения:

Убедитесь, что системная учетная запись имеет доступ для чтения к папке **C:\ProgramData\Microsoft\Crypto\RSA** и повторите попытку.

### <a name="hresult"></a>Сценарий. Компьютер отображается как "Без оценки" и показывает исключение HResult

#### <a name="issue"></a>Проблема

У вас есть машины, которые отображаются как **Без оценки** под пунктом **Совместимость**, и ниже появляется сообщение об ошибке.

#### <a name="cause"></a>Причина:

Центр обновления Windows или Windows Server Update Services неправильно настроены на компьютере. Решение "Управление обновлениями" зависит от Центра обновлений Windows или от служб WSUS для предоставления необходимых обновлений, состояния исправления и результатов развернутых исправлений. Без этой информации решение "Управление обновлениями" не сможет должным образом предоставлять отчеты об исправлениях, которые требуется установить или которые уже установлены.

#### <a name="resolution"></a>Способы устранения:

Чтобы просмотреть целиком сообщение об исключении, дважды щелкните на исключение, выделенное красным. Ознакомьтесь со следующей таблицей для выполнения потенциальных решений или действий.

|Исключение  |Разрешение или действие  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Найдите соответствующий код ошибки в [списке кодов ошибок обновления Windows](https://support.microsoft.com/help/938205/windows-update-error-code-list), чтобы узнать о причине исключения.        |
|`0x8024402C` или `0x8024401C`     | Проблемы с сетевым подключением. Убедитесь, что компьютер имеет правильное сетевое подключение для "Управление обновлениями". См. в разделе [порты](../automation-update-management.md#ports) список обязательных портов и адресов.        |
|`0x8024402C`     | Используя сервер WSUS, убедитесь, что в разделе реестра `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` значение реестра `WUServer` и `WUStatusServer` имеет правильный WSUS-сервер.        |
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Убедитесь, что служба Центр обновления Windows (wuauserv) работает и не отключена.        |
|Другое универсальное исключение     | Выполните поиск возможных решений и работы с локальной ИТ-поддержкой в Интернете.         |

Просмотр `windowsupdate.log` может помочь попытаться определить, а также возможных причин. Дополнительные сведения о том, как чтение журнала см. в разделе [как файла Windowsupdate.log](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Кроме того, можно загрузить и запустить [средство устранения неполадок Центра обновления Windows](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) для проверки возможных проблем с обновлениями Windows на компьютере.

> [!NOTE]
> [Средство устранения неполадок Центра обновления Windows](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) предназначено для ОС Windows, но также работает в Windows Server.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Сценарий. Сбой запуска обновления

#### <a name="issue"></a>Проблема

Сбой запуска обновления на компьютере Linux.

#### <a name="cause"></a>Причина:

Гибридная рабочая роль Linux неработоспособна.

#### <a name="resolution"></a>Способы устранения:

Создайте копию в следующем файле журнала и сохранить его для устранения неполадок.

```bash
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Сценарий. Обновление запускается, но возникают ошибки

#### <a name="issue"></a>Проблема

Запуск обновления начинается, но возникают ошибки во время выполнения.

#### <a name="cause"></a>Причина:

Возможные причины:

* диспетчер пакетов неработоспособен;
* определенные пакеты могут конфликтовать с установкой исправлений облака;
* другие причины.

#### <a name="resolution"></a>Способы устранения:

Если сбои происходят во время запуска обновления после того, как он успешно запущен в Linux, проверьте выполнение задания на затронутом компьютере в ходе выполнения. Просмотрите конкретные сообщения об ошибках из диспетчера пакетов вашего компьютера и примите меры. Для успешного обновления развертываний управлению обновлениями необходима работоспособность диспетчера пакетов.

В некоторых случаях пакет обновления может повлиять на предотвращение завершения развертывания обновлений в управлении обновлениями. Если это происходит, необходимо или исключить эти пакеты из запусков будущих обновлений или установить их самостоятельно вручную.

Если не удается это исправить, создайте копию в следующем файле журнала и сохраните его **перед** началом следующего развертывания обновления для устранения неполадок.

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.
