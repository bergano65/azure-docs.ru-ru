---
title: Устранение неполадок настройки требуемого состояния службы автоматизации Azure (DSC)
description: В этой статье приводятся сведения об устранении неполадок с платформой Desired State Configuration (DSC).
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dcd0371d275c3a46fe9bf07c96516a2d0820abb7
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430539"
---
# <a name="troubleshoot-issues-with-azure-automation-desired-state-configuration-dsc"></a>Устранение неполадок с конфигурацией требуемого состояния службы автоматизации Azure (DSC)

В этой статье приводятся сведения об устранении неполадок с платформой Desired State Configuration (DSC).

## <a name="diagnosing-an-issue"></a>Диагностика проблемы

Если при компиляции или развертывании конфигураций в конфигурации состояния Azure возникли ошибки, выполните следующие действия, чтобы помочь в диагностике проблемы.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Убедитесь, что конфигурация успешно компилируется на локальном компьютере.

Настройка состояния Azure основана на PowerShell DSC. Документацию по языку DSC и синтаксису можно найти в документации по [DSC PowerShell](https://docs.microsoft.com/powershell/scripting/overview).

Компиляция конфигурации DSC на локальном компьютере позволяет обнаружить и устранить распространенные ошибки, например:

   - Отсутствующие модули
   - Синтаксические ошибки
   - Логические ошибки

### <a name="2-view-dsc-logs-on-your-node"></a>2. Просмотр журналов DSC на узле

Если конфигурация успешно компилируется, но при применении к узлу происходит сбой, можно найти подробные сведения в журналах DSC. Сведения о том, где найти эти журналы, см. [в разделе где находятся журналы событий DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

Модуль [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) может помочь при анализе подробных сведений из журналов DSC. При обращении в службу поддержки эти журналы необходимы для диагностики проблемы.

Модуль xDscDiagnostics можно установить на локальном компьютере с помощью инструкций, приведенных в разделе [Установка стабильной версии модуля](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Чтобы установить модуль xDscDiagnostics на компьютере Azure, используйте [командлет Invoke-азвмрункомманд](/powershell/module/azurerm.compute/invoke-azurermvmruncommand). Можно также использовать параметр **выполнить команду** на портале, выполнив действия, описанные в разделе [Запуск сценариев PowerShell в виртуальной машине Windows с помощью команды Run](../../virtual-machines/windows/run-command.md).

Сведения об использовании xDscDiagnostics см. [в разделе Использование xDscDiagnostics для анализа журналов DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). См. также [командлеты xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Убедитесь, что узлы и Рабочая область автоматизации имеют необходимые модули

DSC зависит от модулей, установленных на узле. При использовании конфигурации состояния службы автоматизации Azure импортируйте необходимые модули в учетную запись службы автоматизации, выполнив действия, описанные в разделе [Импорт модулей](../shared-resources/modules.md#import-modules). Конфигурации могут также иметь зависимость от конкретных версий модулей. Дополнительные сведения см. в разделе [Устранение неполадок модулей](shared-resources.md#modules).

## <a name="common-errors-when-working-with-dsc"></a>Распространенные ошибки при работе с DSC

### <a name="unsupported-characters"></a>Сценарий: невозможно удалить конфигурацию с специальными символами с портала

#### <a name="issue"></a>Проблема

При попытке удалить конфигурацию DSC с портала появляется следующее сообщение об ошибке:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Причина

Эта ошибка является временной проблемой, которая планируется разрешить.

#### <a name="resolution"></a>Решение

* Чтобы удалить конфигурацию, используйте командлет AZ "Remove-Азаутоматиондскконфигуратион".
* Документация для этого командлета еще не обновлена.  До этого момента обратитесь к документации по модулю AzureRM.
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>Сценарий: не удалось зарегистрировать агент DSC

#### <a name="issue"></a>Проблема

При попытке запуска `Set-DscLocalConfigurationManager` или другого командлета DSC появляется сообщение об ошибке:

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

#### <a name="cause"></a>Причина

Эта ошибка обычно вызвана брандмауэром, компьютером, который находится за прокси-сервером, или другими сетевыми ошибками.

#### <a name="resolution"></a>Решение

Убедитесь, что компьютер имеет доступ к соответствующим конечным точкам Azure Automation DSC и повторите попытку. Список требуемых портов и адресов см. в разделе [планирование сети](../automation-dsc-overview.md#network-planning) .

### <a name="a-nameunauthorizedascenario-status-reports-return-response-code-unauthorized"></a>Сценарий <a/><a name="unauthorized">. отчеты о состоянии возвращают код ответа "несанкционированный"

#### <a name="issue"></a>Проблема

При регистрации узла с помощью конфигурации состояния (DSC) вы получаете одно из следующих сообщений об ошибке:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Причина

Эта проблема вызвана неверным или просроченным сертификатом.  Дополнительные сведения см. в статье [истечение срока действия сертификата и](../automation-dsc-onboarding.md#certificate-expiration-and-re-registration)повторная регистрация.

### <a name="resolution"></a>Решение

Выполните приведенные ниже действия, чтобы повторно зарегистрировать неисправный узел DSC.

Сначала отмените регистрацию узла, выполнив следующие действия.

1. В портал Azure в разделе **учетные записи автоматизации** **главной** -> — > {Ваша учетная запись службы автоматизации} — **Настройка состояния > (DSC)** .
2. Щелкните "узлы" и выберите узел с проблемами.
3. Нажмите кнопку "отменить регистрацию", чтобы отменить регистрацию узла.

Во вторых, удалите расширение DSC с узла.

1. В портал Azure в разделе **домашняя** -> **виртуальная машина** > {сбой узла} — **расширения** >
2. Щелкните "Microsoft. PowerShell. DSC".
3. Нажмите кнопку "Удалить", чтобы удалить расширение PowerShell DSC.

В третьих, удалите из узла все неправильные или просроченные сертификаты.

На узле, на котором произошел сбой, в командной строке PowerShell с повышенными привилегиями выполните следующую команду:

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

Наконец, повторно зарегистрируйте неисправный узел, выполнив следующие действия.

1. В портал Azure в разделе **учетные записи автоматизации** **главной** -> — > {Ваша учетная запись службы автоматизации} — **Настройка состояния > (DSC)** .
2. Щелкните "узлы".
3. Нажмите кнопку "Добавить".
4. Выберите узел, на котором произошел сбой.
5. Щелкните "подключить" и выберите нужные параметры.

### <a name="failed-not-found"></a>Сценарий: узел находится в состоянии сбоя с ошибкой "Не найдено"

#### <a name="issue"></a>Проблема

В отчете об узле указано состояние **сбоя** и содержится сообщение об ошибке:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Причина

Эта ошибка обычно возникает из-за того, что узлу назначается имя конфигурации (например, ABC) вместо имени конфигурации узла (например, ABC.WebServer).

#### <a name="resolution"></a>Решение

* Убедитесь, что вы назначаете узлу имя конфигурации узла, а не "имя конфигурации".
* Конфигурацию узла можно назначить узлу с помощью портала Azure или с помощью командлета PowerShell.

  * Чтобы назначить конфигурацию узла узлу с помощью портал Azure, откройте страницу **узлы DSC** , выберите узел и нажмите кнопку **назначить конфигурацию узла** .
  * Чтобы назначить конфигурацию узла узлу с помощью командлета PowerShell, используйте командлет **Set-AzureRmAutomationDscNode** .

### <a name="no-mof-files"></a>Сценарий: при компиляции конфигурации не были созданы файлы конфигурации узла (MOF-файлы)

#### <a name="issue"></a>Проблема

Задание компиляции DSC приостановлено с ошибкой:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Причина

Если выражение, указанное в конфигурации DSC рядом с ключевым словом **Node**, возвращает результат `$null`, конфигурация узла не создается.

#### <a name="resolution"></a>Решение

Эту проблему можно устранить одним из следующих способов.

* Убедитесь, что выражение рядом с ключевым словом **node** в определении конфигурации не оценивается как $null.
* Если при компиляции конфигурации вы передаете ConfigurationData, то убедитесь, что передаются ожидаемые значения из [ConfigurationData](../automation-dsc-compile.md), которые необходимы конфигурации.

### <a name="dsc-in-progress"></a>Сценарий: отчет узла DSC зависает в состоянии "Выполняется"

#### <a name="issue"></a>Проблема

Агент DSC выводит такое сообщение:

```error
No instance found with given property values
```

#### <a name="cause"></a>Причина

Вы обновили версию WMF, что привело к повреждению WMI.

#### <a name="resolution"></a>Решение

Чтобы устранить эту проблему, следуйте инструкциям в статье об [известных проблемах и ограничениях DSC](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc) .

### <a name="issue-using-credential"></a>Сценарий: не удается использовать учетные данные в конфигурации DSC

#### <a name="issue"></a>Проблема

Задание компиляции DSC приостановлено со следующей ошибкой:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Причина

Вы использовали учетные данные в конфигурации, но не предоставили правильно **ConfigurationData** , чтобы установить **PSDscAllowPlainTextPassword** в значение true для каждой конфигурации узла.

#### <a name="resolution"></a>Решение

* Обязательно передавайте подходящую **ConfigurationData** , чтобы установить **PSDscAllowPlainTextPassword** в значение true для каждой конфигурации узла, указанной в конфигурации. Дополнительные сведения см. [в разделе Компиляция конфигураций DSC в конфигурации состояния службы автоматизации Azure](../automation-dsc-compile.md).

### <a name="failure-processing-extension"></a>Сценарий: подключение из расширения DSC, ошибка при обработке расширения

#### <a name="issue"></a>Проблема

При подключении с помощью расширения DSC происходит сбой, содержащий ошибку:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Причина

Эта ошибка обычно возникает, когда узлу назначено имя конфигурации узла, которое не существует в службе.

#### <a name="resolution"></a>Решение

* Убедитесь, что вы назначаете узлу имя конфигурации узла, которое точно соответствует имени в службе.
* Можно выбрать не включать имя конфигурации узла, что приведет к адаптации узла, но не назначению конфигурации узла.

### <a name="cross-subscription"></a>Сценарий. Регистрация узла с помощью PowerShell возвращает ошибку "произошла одна или несколько ошибок"

#### <a name="issue"></a>Проблема

При регистрации узла с помощью `Register-AzAutomationDSCNode` или `Register-AzureRMAutomationDSCNode`появляется следующее сообщение об ошибке.

```error
One or more errors occurred.
```

#### <a name="cause"></a>Причина

Эта ошибка возникает при попытке зарегистрировать узел, который находится в отдельной подписке, отличной от учетной записи службы автоматизации.

#### <a name="resolution"></a>Решение

Рассматривайте узел между подписками так, как будто он находится в отдельном облаке или локально.

Выполните следующие действия, чтобы зарегистрировать узел.

* Физические или [виртуальные машины Windows в локальной среде или в облаке, отличном от Azure/AWS](../automation-dsc-onboarding.md#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances).
* [Виртуальные машины Linux — физические или виртуальных машин Linux — локально или в облаке, отличном от Azure](../automation-dsc-onboarding.md#physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure).

### <a name="agent-has-a-problem"></a>Сценарий: сообщение об ошибке-"сбой подготовки"

#### <a name="issue"></a>Проблема

При регистрации узла отображается сообщение об ошибке:

```error
Provisioning has failed
```

#### <a name="cause"></a>Причина

Это сообщение появляется при наличии проблем с подключением между узлом и Azure.

#### <a name="resolution"></a>Решение

Определите, находится ли узел в частной виртуальной сети или возникли другие проблемы с подключением к Azure.

Дополнительные сведения см. в разделе [Устранение ошибок при адаптации решений](onboarding.md).

### <a name="failure-linux-temp-noexec"></a>Сценарий: применение конфигурации в Linux, сбой при возникновении общей ошибки

#### <a name="issue"></a>Проблема

При применении конфигурации в Linux происходит сбой, содержащий ошибку:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Причина

Клиенты обнаружили, что если `/tmp` расположение имеет значение `noexec`, текущая версия DSC не сможет применить конфигурации.

#### <a name="resolution"></a>Решение

* Удалите параметр `noexec` из расположения `/tmp`.

### <a name="compilation-node-name-overlap"></a>Сценарий: имена конфигураций узлов, которые перекрываются, могут привести к повреждению выпуска

#### <a name="issue"></a>Проблема

Если для создания конфигурации с несколькими узлами используется один скрипт конфигурации, а некоторые конфигурации узла имеют имя, которое является подмножеством других, то проблема в службе компиляции может привести к назначению неверной конфигурации.  Это происходит только при использовании одного сценария для создания конфигураций с данными конфигурации на узле и только в том случае, если имя перекрывается в начале строки.

Например, если один скрипт конфигурации используется для создания конфигураций на основе данных узла, передаваемых в виде хэш-таблицы с помощью командлетов, а данные узла включают сервер с именем "Server" и "1server".

#### <a name="cause"></a>Причина

Известная ошибка службы компиляции.

#### <a name="resolution"></a>Решение

Лучшим решением является компиляция локально или в конвейере CI/CD и передача файлов MOF непосредственно в службу.  Если компиляция в службе является требованием, то в следующем лучшем случае следует разделить задания компиляции так, чтобы имена не перекрывались.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.
