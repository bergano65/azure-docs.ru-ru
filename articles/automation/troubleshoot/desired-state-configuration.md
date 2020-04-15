---
title: Неполадки Azure Автоматизация Желаемая конфигурация состояния (DSC)
description: В этой статье приводятся сведения об устранении неполадок с платформой Desired State Configuration (DSC).
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8e0b874e0ce56d7b4ed9fce15b2d88503d6c49b7
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309145"
---
# <a name="troubleshoot-issues-with-azure-automation-desired-state-configuration-dsc"></a>Проблемы устранения проблем с конфигурацией Azure Automation Desired State (DSC)

В этой статье приводятся сведения об устранении неполадок с платформой Desired State Configuration (DSC).

## <a name="diagnosing-an-issue"></a>Диагностика проблемы

При обнаружении ошибок при компиляции или развертывании конфигураций в конфигурации состояния Azure приведены несколько шагов, которые помогут диагностировать проблему.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Убедитесь, что конфигурация успешно компилируется на локальной машине

Конфигурация состояния Azure построена на PowerShell DSC. Вы можете найти документацию для языка DSC и синтаксиса в [PowerShell DSC Docs.](https://docs.microsoft.com/powershell/scripting/overview)

Компиляция конфигурации DSC на локальной машине позволяет обнаружить и устранить распространенные ошибки, такие как:

   - Отсутствующие модули
   - Синтаксические ошибки
   - Логические ошибки

### <a name="2-view-dsc-logs-on-your-node"></a>2. Просмотр журналов DSC на вашем уде

Если конфигурация успешно компилируется, но не удается при нанесении на узла, вы можете найти подробную информацию в журналах DSC. Для получения информации о том, где найти эти журналы, смотрите [Где находятся журналы событий DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

Модуль [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) может помочь вам в разборе подробной информации из журналов DSC. Если вы обратитесь в службу поддержки, они требуют, чтобы эти журналы диагностировать вашу проблему.

Вы можете установить модуль xDscDiagnostics на локальной машине, используя инструкции, найденные в [установке модуля стабильной версии.](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)

Для установки модуля xDscDiagnostics на машину Azure используйте [Invoke-AzVMRunCommand.](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) Вы также можете использовать опцию **команды Run** с портала, следуя шагам, найденным в [сценариях Run PowerShell в Windows VM с командой Run.](../../virtual-machines/windows/run-command.md)

Для получения информации об использовании xDscDiagnostics [см.](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs) Смотрите также [xDscДиагностика Cmdlets](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Обеспечить, чтобы узлы и рабочее пространство автоматизации имели необходимые модули

DSC зависит от модулей, установленных на уделе. При использовании конфигурации состояния автоматизации Azure импортируйте все необходимые модули в свою учетную запись Автоматизации, используя шаги, перечисленные в [импортных модулях.](../shared-resources/modules.md#importing-modules) Конфигурации также могут иметь зависимость от определенных версий модулей. Для получения дополнительной [информации см.](shared-resources.md#modules)

## <a name="common-errors-when-working-with-dsc"></a>Распространенные ошибки при работе с DSC

### <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Сценарий: Конфигурация со специальными символами не может быть удалена с портала

#### <a name="issue"></a>Проблемы

При попытке удалить конфигурацию DSC с портала вы увидите следующую ошибку:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Причина:

Эта ошибка является временной проблемой, которую планируется решить.

#### <a name="resolution"></a>Решение

* Для удаления конфигурации используйте Az Cmdlet "Удалите-AzAutomationDscConfiguration".
* Документация по этому cmdlet еще не обновлена.  До этого можно будет обратиться к документации для модуля AzureRM.
  * [Удалить-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>Сценарий: Не удалось зарегистрировать агента Dsc

#### <a name="issue"></a>Проблемы

При попытке `Set-DscLocalConfigurationManager` запустить или другой DSC cmdlet вы получаете ошибку:

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

#### <a name="cause"></a>Причина:

Эта ошибка обычно вызвана брандмауэром, машиной, намиевшей прокси-сервером, или другими сетевыми ошибками.

#### <a name="resolution"></a>Решение

Проверить вашу машину имеет доступ к надлежащей конечных точек для Azure Автоматизация DSC и попробуйте еще раз. Для списка необходимых портов и [network planning](../automation-dsc-overview.md#network-planning) адресов см.

### <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>Сценарий: Отчеты о состоянии возвращают код ответа "Несанкционированный"

#### <a name="issue"></a>Проблемы

При регистрации узла в State Configuration (DSC) вы получаете одно из следующих сообщений об ошибке:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Причина:

Эта проблема вызвана плохим или просроченным сертификатом.  Для получения дополнительной информации [см.](../automation-dsc-onboarding.md#re-registering-a-node)

Эта проблема также может быть вызвана конфигурацией прокси, не позволяющей получить доступ к**q .azure-automation.net**. Для получения дополнительной информации [см.](../automation-dsc-overview.md#network-planning) 

### <a name="resolution"></a>Решение

Следуйте перечисленным ниже шагам, чтобы перерегистрировать неисправный узла DSC.

Во-первых, отменить регистрацию узла, используя следующие шаги.

1. С портала Azure, под -> **учетными записями домашней автоматизации**-> «Ваша учетная запись автоматизации» -> **конфигурации состояния (DSC)** **Home**
2. Нажмите "Узлы", и нажмите на узла возникли проблемы.
3. Нажмите кнопку "Unregister", чтобы отменить регистрацию узла.

Во-вторых, удалить расширение DSC из узла.

1. С портала Azure, под **домашней** -> **виртуальной машиной** -> «Failing node» -> **расширения**
2. Нажмите "Microsoft.Powershell.DSC".
3. Нажмите кнопку "Удалить", чтобы удалить расширение PowerShell DSC.

В-третьих, удалите все плохие или просроченные сертификаты из узла.

На неисправном уде же вне силу из повышенного Powershell Prompt запустите следующее:

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

Наконец, перерегистрируйте неисправный узла, используя следующие действия.

1. С портала Azure, под -> **учетными записями домашней автоматизации** -> «Ваша учетная запись автоматизации» -> **конфигурации состояния (DSC)** **Home**
2. Нажмите "Узлы".
3. Нажмите кнопку "Добавить".
4. Выберите неисправный узла.
5. Нажмите кнопку "Подключиться" и выберите нужные варианты.

### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Сценарий: узел находится в состоянии сбоя с ошибкой "Не найден"

#### <a name="issue"></a>Проблемы

В отчете об узле указано состояние **сбоя** и содержится сообщение об ошибке:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Причина:

Эта ошибка обычно возникает из-за того, что узлу назначается имя конфигурации (например, ABC) вместо имени конфигурации узла (например, ABC.WebServer).

#### <a name="resolution"></a>Решение

* Убедитесь, что узла назначат с "именем конфигурации узлов", а не "имя конфигурации".
* Конфигурацию узла можно назначить узлу с помощью портала Azure или с помощью командлета PowerShell.

  * Чтобы назначить конфигурацию узлов узлов с помощью портала Azure, откройте страницу **DSC Узлов,** затем выберите узел и нажмите на кнопку **конфигурации узла Assign.**
  * Чтобы назначить конфигурацию узла узлу с помощью смдлета PowerShell, используйте **Set-AzureRmAutomationDscNode** cmdlet

### <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a><a name="no-mof-files"></a>Сценарий: При компилировании конфигурации не производились конфигурации узлов (файлов MOF)

#### <a name="issue"></a>Проблемы

Задание компиляции DSC приостановлено с ошибкой:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Причина:

Если выражение, указанное в конфигурации DSC рядом с ключевым словом **Node**, возвращает результат `$null`, конфигурация узла не создается.

#### <a name="resolution"></a>Решение

Эту проблему можно устранить одним из следующих способов.

* Убедитесь, что выражение рядом с ключевым словом **Узла** в определении конфигурации не оценивается как $null.
* Если при компиляции конфигурации вы передаете ConfigurationData, то убедитесь, что передаются ожидаемые значения из [ConfigurationData](../automation-dsc-compile.md), которые необходимы конфигурации.

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-progress-state"></a><a name="dsc-in-progress"></a>Сценарий: Отчет узла DSC застревает в состоянии «в прогрессе»

#### <a name="issue"></a>Проблемы

Агент DSC выводит такое сообщение:

```error
No instance found with given property values
```

#### <a name="cause"></a>Причина:

Вы обновили версию WMF, что привело к повреждению WMI.

#### <a name="resolution"></a>Решение

Чтобы исправить проблему, следуйте инструкциям в статье [DSC известных проблем и ограничений.](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc)

### <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Сценарий: Невозможно использовать учетные данные в конфигурации DSC

#### <a name="issue"></a>Проблемы

Задание компиляции DSC приостановлено со следующей ошибкой:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Причина:

Вы использовали учетные данные в конфигурации, но не предоставили надлежащих **ConfigurationData** для установки **PSDscAllowPlainTextPassword** для каждой конфигурации узлов.

#### <a name="resolution"></a>Решение

* Убедитесь в том, чтобы пройти в надлежащем **ConfigurationData** установить **PSDscAllowPlainTextPassword** верно для каждой конфигурации узла, который упоминается в конфигурации. Для получения дополнительной информации см. [Настройка DSC-компиляции в конфигурации состояния автоматизации Azure.](../automation-dsc-compile.md)

### <a name="scenario-onboarding-from-dsc-extension-failure-processing-extension-error"></a><a name="failure-processing-extension"></a>Сценарий: Посадка из расширения dsc, ошибка "Отказ в обработке"

#### <a name="issue"></a>Проблемы

При посадке с использованием расширения DSC возникает сбой, содержащий ошибку:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Причина:

Эта ошибка обычно возникает, когда узла присваивается имя конфигурации узла, которое не существует в службе.

#### <a name="resolution"></a>Решение

* Убедитесь, что узла назначат с именем конфигурации узлов, которое точно соответствует имени службы.
* Вы можете не включать имя конфигурации узла, что приведет к посадке узла, но не присваивает конфигурацию узла

### <a name="scenario-registering-a-node-with-powershell-returns-the-error-one-or-more-errors-occurred"></a><a name="cross-subscription"></a>Сценарий: Регистрация узла с PowerShell возвращает ошибку "Одна или несколько ошибок произошло"

#### <a name="issue"></a>Проблемы

При регистрации узла `Register-AzAutomationDSCNode` `Register-AzureRMAutomationDSCNode`с использованием или при использовании следующей ошибки.

```error
One or more errors occurred.
```

#### <a name="cause"></a>Причина:

Эта ошибка возникает при попытке зарегистрировать узла, который живет в отдельной подписке, чем учетная запись Автоматизации.

#### <a name="resolution"></a>Решение

Относитесь к узлам по перекрестной подписке так, как если бы он живет в отдельном облаке или на месте.

Выполните ниже шаги, чтобы зарегистрировать узла.

* Windows - [Физические/виртуальные windows-машины на месте или в облаке, кроме Azure/AWS.](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)
* Linux - [Физические/виртуальные машины Linux на месте, или в облаке, кроме Azure](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure).

### <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>Сценарий: Сообщение об ошибке - "Обеспечение не удалось"

#### <a name="issue"></a>Проблемы

При регистрации узла вы видите ошибку:

```error
Provisioning has failed
```

#### <a name="cause"></a>Причина:

Это сообщение возникает, когда между узелом и Azure возникает проблема подключения.

#### <a name="resolution"></a>Решение

Определите, находится ли ваш узел в частной виртуальной сети или есть другие проблемы, связанные с Azure.

Для получения дополнительной информации [см.](onboarding.md)

### <a name="scenario-applying-a-configuration-in-linux-a-failure-occurs-with-a-general-error"></a><a name="failure-linux-temp-noexec"></a>Сценарий: Применяя конфигурацию в Linux, сбой происходит с общей ошибкой

#### <a name="issue"></a>Проблемы

При применении конфигурации в Linux возникает сбой, содержащий ошибку:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Причина:

Клиенты определили, `/tmp` что если `noexec`местоположение настроено на, текущая версия DSC не будет применять конфигурации.

#### <a name="resolution"></a>Решение

* Удалите опцию `noexec` из местоположения. `/tmp`

### <a name="scenario-node-configuration-names-that-overlap-could-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>Сценарий: Имена конфигурации узлов, которые перекрываются, могут привести к плохому выпуску

#### <a name="issue"></a>Проблемы

Если один сценарий конфигурации используется для генерации нескольких конфигураций узлов, а некоторые конфигурации узлов имеют имя, которое является подмножеством других, проблема в службе компиляции может привести к назначению неправильной конфигурации.  Это происходит только при использовании одного скрипта для генерации конфигураций с данными конфигурации на узла и только при перекрытии имени в начале строки.

Например, если один сценарий конфигурации используется для генерации конфигураций на основе данных узлов, переданных как хэш-таблица с использованием cmdlets, а данные узлов включают сервер под названием "сервер" и "1server".

#### <a name="cause"></a>Причина:

Известная проблема с службой компиляции.

#### <a name="resolution"></a>Решение

Лучшим обходным решением было бы компилировать локально или в конвейере CI/CD и загружать файлы MOF непосредственно в службу.  Если компиляция в службе является требованием, следующим лучшим решением будет разделение заданий компиляции, чтобы не было перекрытия имен.

### <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Сценарий: Ошибка тайм-аута шлюза на загрузке конфигурации DSC

#### <a name="issue"></a>Проблемы

Вы получаете ошибку `GatewayTimeout` при загрузке конфигурации DSC. 

#### <a name="cause"></a>Причина:

Настройки DSC, на компиляцию которого требуется много времени, могут привести к этой ошибке.

#### <a name="resolution"></a>Решение

Конфигурации DSC можно разбирать быстрее, явно `ModuleName` включив `Import-DscResource` параметр для любых вызовов. Для получения дополнительной информации [см.](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1)

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [форумы Azure.](https://azure.microsoft.com/support/forums/)
* Связь [@AzureSupport](https://twitter.com/azuresupport) с официальной учетной записью Microsoft Azure для улучшения обслуживания клиентов путем подключения сообщества Azure к нужным ресурсам: ответам, поддержке и экспертам.
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **«Получите поддержку».**
