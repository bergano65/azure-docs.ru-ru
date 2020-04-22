---
title: Конфигурация состояния автоматизации azure (DSC)
description: В этой статье содержится информация о устранении неполадок в конфигурации состояния автоматизации Azure (DSC).
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4c045e110e21ed201278dcd84f38cb4a376ae8db
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679312"
---
# <a name="troubleshoot-issues-with-azure-automation-state-configuration-dsc"></a>Проблемы устранения проблем с конфигурацией состояния автоматизации Azure (DSC)

В этой статье содержится информация о проблемах устранения неполадок, возникающих при компиляции или развертывании конфигураций в конфигурации состояния автоматизации Azure (DSC).

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](../automation-update-azure-modules.md)

## <a name="diagnosing-an-issue"></a>Диагностика проблемы

При получении компиляции или ошибки развертывания для настройки, вот несколько шагов, которые помогут вам диагностировать проблему.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Убедитесь, что конфигурация успешно компилируется на локальной машине

Конфигурация состояния автоматизации Azure (DSC) построена на конфигурации состояния PowerShell Desired State (DSC). Вы можете найти документацию для языка DSC и синтаксиса в [PowerShell DSC Docs.](https://docs.microsoft.com/powershell/scripting/overview)

Компиляция конфигурации DSC на локальной машине позволяет обнаружить и устранить распространенные ошибки, такие как:

   - Отсутствующие модули
   - Синтаксические ошибки
   - Логические ошибки

### <a name="2-view-dsc-logs-on-your-node"></a>2. Просмотр журналов DSC на вашем уде

Если конфигурация успешно компилируется, но не удается при нанесении на узла, вы можете найти подробную информацию в журналах DSC. Для получения информации о том, где найти эти журналы, смотрите [Где находятся журналы событий DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

Модуль [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) может помочь вам в разборе подробной информации из журналов DSC. Если вы обратитесь в службу поддержки, они требуют, чтобы эти журналы диагностировать вашу проблему.

Вы можете `xDscDiagnostics` установить модуль на локальной машине, используя инструкции, найденные в [установке модуля стабильной версии.](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)

Для установки `xDscDiagnostics` модуля на машину Azure используйте [Invoke-AzVMRunCommand.](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0) Вы также можете использовать опцию **команды Run** на портале Azure, следуя шагам, найденным в [сценариях Run PowerShell в Windows VM с командой Run.](../../virtual-machines/windows/run-command.md)

Для получения информации об использовании **xDscDiagnostics** [см.](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs) Смотрите также [xDscДиагностика Cmdlets](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Обеспечить, чтобы узлы и рабочее пространство автоматизации имели необходимые модули

DSC зависит от модулей, установленных на уделе. При использовании конфигурации состояния автоматизации Azure импортируйте все необходимые модули в свою учетную запись Автоматизации, используя шаги в [импортных модулях.](../shared-resources/modules.md#importing-modules) Конфигурации также могут иметь зависимость от определенных версий модулей. Для получения дополнительной [информации см.](shared-resources.md#modules)

## <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Сценарий: Конфигурация со специальными символами не может быть удалена с портала

### <a name="issue"></a>Проблемы

При попытке удалить конфигурацию DSC с портала вы увидите следующую ошибку:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Причина:

Эта ошибка является временной проблемой, которую планируется решить.

### <a name="resolution"></a>Решение

Для удаления конфигурации используйте «Удалите-AzAutomationDscConfiguration» (cmdlet).https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration?view=azps-3.7.0

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>Сценарий: Не удалось зарегистрировать агента Dsc

### <a name="issue"></a>Проблемы

При [установке-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) или другой DSC cmdlet, вы получаете ошибку:

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

### <a name="cause"></a>Причина:

Эта ошибка обычно вызвана брандмауэром, машиной, намиевшей прокси-сервером, или другими сетевыми ошибками.

### <a name="resolution"></a>Решение

Убедитесь, что ваша машина имеет доступ к правильным конечным точкам для DSC и повторите попытку. Для списка необходимых портов и [network planning](../automation-dsc-overview.md#network-planning) адресов см.

## <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>Сценарий: Отчеты о статусе возвращают ответный код несанкционированно

### <a name="issue"></a>Проблемы

При регистрации узла с конфигурацией состояния автоматизации Azure вы получаете одно из следующих сообщений об ошибках:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Причина:

Эта проблема вызвана плохим или просроченным сертификатом. Смотрите [срок действия сертификата и перерегистрацию.](../automation-dsc-onboarding.md#re-registering-a-node)

Эта проблема также может быть вызвана конфигурацией прокси, не позволяющей получить доступ к**azure-automation.net .** Для получения дополнительной информации [см.](../automation-dsc-overview.md#network-planning) 

### <a name="resolution"></a>Решение

Используйте приведенные ниже шаги для перерегистрации неисправного узла DSC.

Шаг 1 - Отменить регистрацию узла.

1. На портале Azure перейдите на**учетные записи** **домашней** -> автоматизации -> (ваша учетная запись автоматизации) - > **конфигурации состояния (DSC).**
2. Выберите **узлы**и нажмите на узла, имеющий проблемы.
3. Нажмите **Unregister,** чтобы отменить регистрацию узла.

Шаг 2 - Удалить расширение DSC из узла.

1. На портале Azure перейдите на **Домашнюю** -> **Виртуальную** Машину-> (неузлый узел) -> **расширения.**
2. Выберите **Microsoft.Powershell.DSC**, расширение PowerShell DSC.
3. **Нажмите Удалить,** чтобы удалить расширение.

Шаг 3 - Удалить все плохие или просроченные сертификаты из узла.

На неисправном узлах из повышенного запроса PowerShell запустите следующие команды:

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

Шаг 4 - Перерегистрация неисправного узла.

1. На портале Azure перейдите к учетным записям **домашней** -> **автоматизации** -> (ваша учетная запись автоматизации) - > **конфигурации состояния (DSC)**
2. Выберите **узлы**.
3. Нажмите кнопку **Добавить**.
4. Выберите неисправный узла.
5. Нажмите **Connect** и выберите нужные варианты.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Сценарий: узел находится в состоянии сбоя с ошибкой "Не найден"

### <a name="issue"></a>Проблемы

В отчете об узле указано состояние сбоя и содержится сообщение об ошибке:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Причина:

Эта ошибка обычно возникает, когда узла присваивается имени конфигурации, например, **ABC,** вместо имени конфигурации узла (файл MOF), **например, ABC. WebServer**.

### <a name="resolution"></a>Решение

* Убедитесь, что узла назначат с именем конфигурации узла, а не с именем конфигурации.
* Конфигурацию узла можно назначить узлу с помощью портала Azure или с помощью командлета PowerShell.

  * На портале Azure перейдите к**учетным записям** **домашней** -> автоматизации -> (ваша учетная запись автоматизации) - > **конфигурации состояния (DSC),** затем выберите конфигурацию узла и нажмите **кнопку «Назначить узел».**
  * Используйте [Set-AzAutomationDscnode](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationDscNode?view=azps-3.7.0) cmdlet.

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Сценарий: При компилировании конфигурации не было произведено конфигураций узлов (файлов MOF)

### <a name="issue"></a>Проблемы

Задание компиляции DSC приостановлено с ошибкой:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Причина:

Когда выражение, `Node` следуя ключевому слову в `$null`конфигурации DSC, оценивается до, конфигурации узлов не производятся.

### <a name="resolution"></a>Решение

Используйте одно из следующих решений для решения проблемы:

* Убедитесь, что выражение `Node` рядом с ключевым словом в определении конфигурации не оценивается до Null.
* Если вы передаете [ConfigurationData](../automation-dsc-compile.md) при компиляции конфигурации, убедитесь, что вы передаете значения, которые конфигурация ожидает от данных конфигурации.

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Сценарий: Отчет узла DSC застревает в состоянии In Progress

### <a name="issue"></a>Проблемы

Агент DSC выводит такое сообщение:

```error
No instance found with given property values
```

### <a name="cause"></a>Причина:

Вы обновили версию системы управления Windows (WMF) и повредили инструменты управления Windows (WMI).

### <a name="resolution"></a>Решение

Следуйте инструкциям в [DSC известных проблем и ограничений](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc).

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Сценарий: Невозможно использовать учетные данные в конфигурации DSC

### <a name="issue"></a>Проблемы

Задание компиляции DSC приостановлено с ошибкой:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Причина:

Вы использовали учетные данные в конфигурации, `ConfigurationData` но `PSDscAllowPlainTextPassword` не обеспечили правильного значения для настройки каждой конфигурации узлов.

### <a name="resolution"></a>Решение

Убедитесь в том, `ConfigurationData` чтобы `PSDscAllowPlainTextPassword` пройти в надлежащем установить на истину для каждого узла конфигурации, которая упоминается в конфигурации. [См. Настройки DSC-компиляции в конфигурации состояния автоматизации Azure.](../automation-dsc-compile.md)

## <a name="scenario-failure-processing-extension-error-when-onboarding-from-dsc-extension"></a><a name="failure-processing-extension"></a>Сценарий: ошибка "Отказ в продлении обработки" при посадке из расширения DSC

### <a name="issue"></a>Проблемы

При посадке с использованием расширения DSC возникает сбой, содержащий ошибку:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Причина:

Эта ошибка обычно возникает, когда узла присваивается имя конфигурации узла, которое не существует в службе.

### <a name="resolution"></a>Решение

* Убедитесь, что вы назначаете узла с именем, которое точно соответствует имени в службе.
* Вы можете не включать имя конфигурации узла, что приводит к посадке узла, но не назначению конфигурации узла.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-using-powershell"></a><a name="cross-subscription"></a>Сценарий: ошибка «Одна или несколько ошибок» при регистрации узла с помощью PowerShell

### <a name="issue"></a>Проблемы

При регистрации узла с помощью [Register-AzAutomationDSCNode](https://docs.microsoft.com/powershell/module/az.automation/register-azautomationdscnode?view=azps-3.7.0) или [Register-AzureRMAutomationDSCNode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-6.13.0), вы получаете следующую ошибку:

```error
One or more errors occurred.
```

### <a name="cause"></a>Причина:

Эта ошибка возникает при попытке зарегистрировать узла в отдельной подписке, используемой учетной записью Automation.

### <a name="resolution"></a>Решение

Относитесь к узлам по перекрестной подписке так, как будто он определен для отдельного облака или локальных. Зарегистрируйте узла, используя один из этих вариантов посадки:

* Windows - [Физические/виртуальные windows-машины на месте или в облаке, кроме Azure/AWS.](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines)
* Linux - [Физические/виртуальные машины Linux на месте, или в облаке, кроме Azure](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines).

## <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>Сценарий: Сообщение об ошибке - "Обеспечение не удалось"

### <a name="issue"></a>Проблемы

При регистрации узла вы видите ошибку:

```error
Provisioning has failed
```

### <a name="cause"></a>Причина:

Это сообщение возникает при возникновении проблемы с подключением между узелом и Azure.

### <a name="resolution"></a>Решение

Определите, находится ли ваш узел в виртуальной частной сети (VPN) или есть другие проблемы, связанные с Azure. Просматривайте [ошибки, при посадке на бортовые решения.](onboarding.md)

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Сценарий: Сбой с общей ошибкой при применении конфигурации в Linux

### <a name="issue"></a>Проблемы

При применении конфигурации в Linux возникает сбой, содержащий ошибку:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Причина:

Если местоположение **/tmp** настроено на, `noexec`текущая версия DSC не может применить конфигурации.

### <a name="resolution"></a>Решение

Удалите опцию `noexec` из места **/tmp.**

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>Сценарий: Имена конфигурации узлов, которые перекрываются, могут привести к плохому выпуску

### <a name="issue"></a>Проблемы

Когда вы используете один сценарий конфигурации для генерации нескольких конфигураций узлов, а некоторые имена конфигураций узлов являются подмножествами других имен, служба компиляции может в конечном итоге назначить неправильную конфигурацию. Эта проблема возникает только при использовании одного скрипта для генерации конфигураций с данными конфигурации на узла и только при перекрытии имени в начале строки. Примером может служить единый сценарий конфигурации, используемый для генерации конфигураций на основе данных узлов, переданных как хэш-таблица с использованием cmdlets, а данные узлов включают серверы, названные **сервером** и **1server.**

### <a name="cause"></a>Причина:

Это известная проблема со службой компиляции.

### <a name="resolution"></a>Решение

Наилучшим обходным решением является компилировать локально или в конвейере CI/CD и загружать файлы MOF конфигурации узлов непосредственно в службу. Если компиляция в службе является требованием, следующим лучшим решением является разделение заданий компиляции таким образом, чтобы не было перекрытия имен.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Сценарий: Ошибка тайм-аута шлюза на загрузке конфигурации DSC

#### <a name="issue"></a>Проблемы

Вы получаете ошибку `GatewayTimeout` при загрузке конфигурации DSC. 

### <a name="cause"></a>Причина:

Настройки DSC, на компиляцию которого требуется много времени, могут привести к этой ошибке.

### <a name="resolution"></a>Решение

Конфигурации DSC можно разбирать быстрее, явно `ModuleName` включив параметр для любых вызовов [Import-DSCResource.](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1)

## <a name="next-steps"></a>Следующие шаги

Если вы не видите проблему выше или не можете решить вашу проблему, попробуйте один из следующих каналов для дополнительной поддержки:

* Получите ответы от экспертов Azure через [форумы Azure.](https://azure.microsoft.com/support/forums/)
* Связаться [@AzureSupport](https://twitter.com/azuresupport)с официальной учетной записью Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.
* Отправьте запрос в службу поддержки Azure Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **«Получите поддержку».**