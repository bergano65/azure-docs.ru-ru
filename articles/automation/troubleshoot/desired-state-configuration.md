---
title: Устранение неполадок конфигурации состояния службы автоматизации Azure
description: В этой статье содержатся сведения об устранении неполадок в конфигурации состояния службы автоматизации Azure.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d0801bb44fc0c08df1adee1f817e8fccab166fb5
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652809"
---
# <a name="troubleshoot-issues-with-azure-automation-state-configuration"></a>Устранение неполадок с конфигурацией состояния службы автоматизации Azure

Эта статья содержит сведения об устранении неполадок, возникающих при компиляции или развертывании конфигураций в конфигурации состояния службы автоматизации Azure.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке модуля Az в гибридной рабочей роли Runbook см. в статье об [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Для учетной записи службы автоматизации можно обновить модули до последней версии, выполнив действия, описанные в статье [обновление модулей Azure PowerShell в службе автоматизации Azure](../automation-update-azure-modules.md).

## <a name="diagnose-an-issue"></a>Диагностика проблемы

При возникновении ошибки компиляции или развертывания для настройки выполните следующие действия, чтобы помочь в диагностике проблемы.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Убедитесь, что конфигурация успешно компилируется на локальном компьютере.

Конфигурация состояния службы автоматизации Azure построена на основе настройки требуемого состояния (DSC) PowerShell. Документацию по языку DSC и синтаксису можно найти в документации по [DSC PowerShell](https://docs.microsoft.com/powershell/scripting/overview).

Компиляция конфигурации DSC на локальном компьютере позволяет обнаружить и устранить распространенные ошибки, например:

   - Отсутствуют модули.
   - Синтаксические ошибки.
   - Логические ошибки.

### <a name="2-view-dsc-logs-on-your-node"></a>2. Просмотр журналов DSC на узле

Если конфигурация успешно компилируется, но при применении к узлу происходит сбой, можно найти подробные сведения в журналах DSC. Сведения о том, где найти эти журналы, см. [в разделе где находятся журналы событий DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

Модуль [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) может помочь при анализе подробных сведений из журналов DSC. При обращении в службу поддержки эти журналы необходимы для диагностики проблемы.

Вы можете установить `xDscDiagnostics` модуль на локальном компьютере, следуя инструкциям в [статье Установка стабильной версии модуля](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Чтобы установить `xDscDiagnostics` модуль на компьютере Azure, используйте [командлет Invoke-азвмрункомманд](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0). Можно также использовать параметр **выполнить команду** в портал Azure, выполнив действия, описанные в разделе [Запуск сценариев PowerShell в виртуальной машине Windows с помощью команды Run](../../virtual-machines/windows/run-command.md).

Сведения об использовании **xDscDiagnostics**см. [в разделе Использование xDscDiagnostics для анализа журналов DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). См. также [командлеты xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Убедитесь, что узлы и Рабочая область автоматизации имеют необходимые модули

DSC зависит от модулей, установленных на узле. При использовании конфигурации состояния службы автоматизации Azure импортируйте необходимые модули в учетную запись службы автоматизации, выполнив действия, описанные в разделе [Импорт модулей](../shared-resources/modules.md#import-modules). Конфигурации могут также иметь зависимость от конкретных версий модулей. Дополнительные сведения см. в разделе [Устранение неполадок модулей](shared-resources.md#modules).

## <a name="scenario-a-configuration-with-special-characters-cant-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Сценарий: невозможно удалить конфигурацию с специальными символами с портала

### <a name="issue"></a>Проблема

При попытке удалить конфигурацию DSC с портала появляется следующее сообщение об ошибке:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Причина

Эта ошибка является временной проблемой, которую планируется устранить.

### <a name="resolution"></a>Решение

Используйте командлет [Remove-Азаутоматиондскконфигуратион]https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration?view=azps-3.7.0 (для удаления конфигурации).

## <a name="scenario-failed-to-register-the-dsc-agent"></a><a name="failed-to-register-agent"></a>Сценарий: не удалось зарегистрировать агент DSC

### <a name="issue"></a>Проблема

Если командлет [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) или другой, то вы получаете следующее сообщение об ошибке:

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

### <a name="cause"></a>Причина

Эта ошибка обычно вызвана брандмауэром, компьютером, который находится за прокси-сервером, или другими сетевыми ошибками.

### <a name="resolution"></a>Решение

Убедитесь, что у компьютера есть доступ к соответствующим конечным точкам DSC, и повторите попытку. Список требуемых портов и адресов см. в разделе [планирование сети](../automation-dsc-overview.md#network-planning).

## <a name="a-nameunauthorizedscenario-status-reports-return-the-response-code-unauthorized"></a><a name="unauthorized"><a/>Сценарий: отчеты о состоянии возвращают несанкционированный код ответа

### <a name="issue"></a>Проблема

При регистрации узла в конфигурации состояния службы автоматизации Azure появляется одно из следующих сообщений об ошибке:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Причина

Эта проблема вызвана неверным или просроченным сертификатом. См. статью [истечение срока действия сертификата и](../automation-dsc-onboarding.md#re-registering-a-node)повторная регистрация.

Эта проблема также может быть вызвана конфигурацией прокси-сервера, которая не разрешает доступ к ***. Azure-Automation.NET**. Дополнительные сведения см. в разделе [Настройка частных сетей](../automation-dsc-overview.md#network-planning). 

### <a name="resolution"></a>Решение

Чтобы повторно зарегистрировать неисправный узел DSC, выполните следующие действия.

#### <a name="step-1-unregister-the-node"></a>Шаг 1. Отмена регистрации узла

1. В портал Azure перейдите на **главную страницу** > **службы автоматизации** > (ваша учетная запись службы автоматизации) > **Configuration State (DSC)**.
1. Выберите **узлы**и выберите узел с проблемами.
1. Нажмите кнопку **отменить регистрацию** , чтобы отменить регистрацию узла.

#### <a name="step-2-uninstall-the-dsc-extension-from-the-node"></a>Шаг 2. Удаление расширения DSC с узла

1. В портал Azure перейдите в раздел **Домашняя** > **Виртуальная машина** > (узел сбоя) > **расширения**.
1. Выберите **Microsoft. PowerShell. DSC**, расширение PowerShell DSC.
1. Выберите **Удалить** , чтобы удалить расширение.

#### <a name="step-3-remove-all-bad-or-expired-certificates-from-the-node"></a>Шаг 3. Удаление всех испорченных или истекших сертификатов с узла

На узле, на котором произошел сбой, в командной строке PowerShell с повышенными привилегиями выполните следующие команды:

```powershell
$certs = @()
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC"}
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC-OaaS Client Authentication"}
$certs += dir cert:\localmachine\CA | ?{$_.subject -like "CN=AzureDSCExtension*"}
"";"== DSC Certificates found: " + $certs.Count
$certs | FL ThumbPrint,FriendlyName,Subject
If (($certs.Count) -gt 0)
{ 
    ForEach ($Cert in $certs) 
    {
        RD -LiteralPath ($Cert.Pspath) 
    }
}
```

#### <a name="step-4-reregister-the-failing-node"></a>Шаг 4. Повторная регистрация узла, на котором произошел сбой

1. В портал Azure перейдите на **главную страницу** > **службы автоматизации** > (ваша учетная запись службы автоматизации) > **Configuration State (DSC)**.
1. Выберите **узлы**.
1. Нажмите **Добавить**.
1. Выберите узел, на котором произошел сбой.
1. Нажмите кнопку **подключить**и выберите нужные параметры.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Сценарий: узел находится в состоянии сбоя с ошибкой "Не найден"

### <a name="issue"></a>Проблема

Узел имеет отчет с состоянием сбоя и содержит ошибку:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Причина

Эта ошибка обычно возникает, когда узлу назначается имя конфигурации, например **ABC**, а не имя файла конфигурации узла (MOF), например **abc. Сервер**.

### <a name="resolution"></a>Решение

* Убедитесь, что вы назначаете узлу имя конфигурации узла, а не имя конфигурации.
* Конфигурацию узла можно назначить узлу с помощью портал Azure или командлета PowerShell.

  * В портал Azure перейдите на **главную страницу** > **службы автоматизации** > (ваша учетная запись службы автоматизации) > **Configuration State (DSC)**. Затем выберите узел и щелкните **назначить конфигурацию узла**.
  * Используйте командлет [Set-азаутоматиондскноде](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationDscNode?view=azps-3.7.0) .

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Сценарий: при компиляции конфигурации не создавались конфигурации узлов (MOF-файлы)

### <a name="issue"></a>Проблема

Задание компиляции DSC приостановлено с ошибкой:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Причина

Когда выражение `$null`, следующее за `Node` ключевым словом в конфигурации DSC, принимает значение, конфигурации узлов не создаются.

### <a name="resolution"></a>Решение

Чтобы устранить проблему, используйте одно из следующих решений.

* Убедитесь, что выражение рядом с `Node` ключевым словом в определении конфигурации не оценивается как null.
* Если вы передаете [ConfigurationData](../automation-dsc-compile.md) при компиляции конфигурации, убедитесь, что вы передаете значения, которые требуется настроить из данных конфигурации.

## <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Сценарий: отчет узла DSC перестает оставаться в состоянии "выполняется"

### <a name="issue"></a>Проблема

Агент DSC выводит такое сообщение:

```error
No instance found with given property values
```

### <a name="cause"></a>Причина

Вы обновили версию Windows Management Framework (WMF) и повреждены инструментарий управления Windows (WMI) (WMI).

### <a name="resolution"></a>Решение

Следуйте инструкциям в статье [Известные проблемы и ограничения в DSC](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc).

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Сценарий: не удается использовать учетные данные в конфигурации DSC

### <a name="issue"></a>Проблема

Задание компиляции DSC приостановлено с ошибкой:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Причина

Вы использовали учетные данные в конфигурации, но не указали `ConfigurationData` `PSDscAllowPlainTextPassword` значение true для каждой конфигурации узла.

### <a name="resolution"></a>Решение

Обязательно передавайте `ConfigurationData` `PSDscAllowPlainTextPassword` значение true для каждой конфигурации узла, указанной в конфигурации. См. раздел [Компиляция конфигураций DSC в конфигурации состояния службы автоматизации Azure](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-onboarding-from-a-dsc-extension"></a><a name="failure-processing-extension"></a>Сценарий: ошибка "сбой при обработке расширения" при подключении из расширения DSC

### <a name="issue"></a>Проблема

При подключении с помощью расширения DSC происходит сбой, который содержит ошибку:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Причина

Эта ошибка обычно возникает, когда узлу назначено имя конфигурации узла, которое не существует в службе.

### <a name="resolution"></a>Решение

* Убедитесь, что вы назначаете узлу имя, которое точно соответствует имени в службе.
* Можно выбрать не включать имя конфигурации узла, что приводит к адаптации узла, но не назначению конфигурации узла.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-by-using-powershell"></a><a name="cross-subscription"></a>Сценарий: ошибка "произошла одна или несколько ошибок" при регистрации узла с помощью PowerShell

### <a name="issue"></a>Проблема

При регистрации узла с помощью командлета [Register-азаутоматиондскноде](https://docs.microsoft.com/powershell/module/az.automation/register-azautomationdscnode?view=azps-3.7.0) или [Register-AzureRMAutomationDSCNode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-6.13.0)появляется следующее сообщение об ошибке:

```error
One or more errors occurred.
```

### <a name="cause"></a>Причина

Эта ошибка возникает при попытке зарегистрировать узел в отдельной подписке, которая используется учетной записью службы автоматизации.

### <a name="resolution"></a>Решение

Рассматривайте узел перекрестной подписки так, как если бы он был определен для отдельного облака или локальной среды. Зарегистрируйте узел, используя один из следующих вариантов адаптации:

* Windows: [физические или виртуальные машины Windows в локальной среде или в облаке, отличном от Azure/AWS](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines).
* Linux: [физические или виртуальные машины Linux в локальной среде или в облаке, отличном от Azure](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines).

## <a name="scenario-provisioning-has-failed-error-message"></a><a name="agent-has-a-problem"></a>Сценарий: сообщение об ошибке "подготовка не выполнена"

### <a name="issue"></a>Проблема

При регистрации узла отображается сообщение об ошибке:

```error
Provisioning has failed
```

### <a name="cause"></a>Причина

Это сообщение появляется при наличии проблем с подключением между узлом и Azure.

### <a name="resolution"></a>Решение

Определите, находится ли узел в виртуальной частной сети (VPN) или возникли другие проблемы с подключением к Azure. См. раздел [Устранение ошибок при адаптации решений](onboarding.md).

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Сценарий: сбой с общей ошибкой при применении конфигурации в Linux

### <a name="issue"></a>Проблема

При применении конфигурации в Linux происходит сбой, который содержит ошибку:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Причина

Если для **/tmp** `noexec`расположения/tmp задано значение, то текущей версии DSC не удается применить конфигурации.

### <a name="resolution"></a>Решение

Удалите `noexec` параметр из расположения **/tmp** .

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-a-bad-release"></a><a name="compilation-node-name-overlap"></a>Сценарий. имена конфигураций узлов, которые перекрываются, могут привести к повреждению выпуска

### <a name="issue"></a>Проблема

При использовании одного скрипта конфигурации для создания нескольких конфигураций узлов, а некоторые имена конфигураций узлов являются подмножествами других имен, служба компиляции может назначить неправильную конфигурацию. Эта проблема возникает только при использовании одного сценария для создания конфигураций с данными конфигурации на узле и только в том случае, если имя перекрывается в начале строки. Примером является один скрипт конфигурации, используемый для создания конфигураций на основе данных узла, передаваемых в виде хэш-таблицы с помощью командлетов, а данные узла включают серверы с именами **Server** и **1server**.

### <a name="cause"></a>Причина

Это известная проблема со службой компиляции.

### <a name="resolution"></a>Решение

Наилучшим решением является компиляция локально или в конвейере CI/CD и передача MOF-файлов конфигурации узла непосредственно в службу. Если компиляция в службе является требованием, то следующим решением будет разделение заданий компиляции так, чтобы имена не перекрывались.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Сценарий: ошибка времени ожидания шлюза при отправке конфигурации DSC

#### <a name="issue"></a>Проблема

При отправке `GatewayTimeout` конфигурации DSC появляется сообщение об ошибке. 

### <a name="cause"></a>Причина

Эта ошибка может быть вызвана настройками DSC, которые занимают много времени для компиляции.

### <a name="resolution"></a>Решение

Можно выполнить синтаксический анализ конфигураций DSC, явно включив `ModuleName` параметр для всех вызовов [Import-DSCResource](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1) .

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите проблему здесь или вы не можете решить проблему, попробуйте использовать один из следующих каналов для получения дополнительной поддержки:

* Получите ответы от экспертов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключайтесь с помощью официальной учетной записи Microsoft Azure для улучшения качества взаимодействия с [@AzureSupport](https://twitter.com/azuresupport)клиентами. Служба поддержки Azure подключается к сообществу Azure для получения ответов, поддержки и экспертов.
* Отправьте запрос в службу поддержки Azure Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/)и выберите **получить поддержку**.
