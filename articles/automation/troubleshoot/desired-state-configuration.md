---
title: Устранение неполадок с платформой Desired State Configuration (DSC) в службе автоматизации Azure
description: В этой статье приводятся сведения об устранении неполадок с платформой Desired State Configuration (DSC).
services: automation
ms.service: automation
ms.subservice: ''
author: bobbytreed
ms.author: robreed
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b9d2dda589cc59be24b73ce16dcdcbbe79b31aef
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259177"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Устранение неполадок с платформой Desired State Configuration (DSC)

В этой статье приводятся сведения об устранении неполадок с платформой Desired State Configuration (DSC).

## <a name="steps-to-troubleshoot-desired-state-configuration-dsc"></a>Действия по устранению неполадок настройки требуемого состояния (DSC)

Если при компиляции или развертывании конфигураций в конфигурации состояния Azure возникли ошибки, выполните следующие действия, чтобы помочь в диагностике проблемы.

1. **Убедитесь, что конфигурация успешно компилируется на локальном компьютере:**  Настройка состояния Azure основана на PowerShell DSC. Документацию по языку DSC и синтаксису можно найти в документации по [DSC PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/overview).

   Путем компиляции конфигурации DSC на локальном компьютере можно обнаружить и устранить распространенные ошибки, например:

   - **Отсутствующие модули**
   - **Синтаксические ошибки**
   - **Логические ошибки**

2. **Просмотр журналов DSC на узле:** Если конфигурация успешно компилируется, но при применении к узлу происходит сбой, можно найти подробные сведения в журналах. Сведения о том, где найти журналы DSC, см. в разделе [где находятся журналы событий DSC](/powershell/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

   Более того, [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) может помочь при анализе подробных сведений из журналов DSC. При обращении в службу поддержки эти журналы попонадобятся вам для диагностики проблемы.

   Вы можете установить **xDscDiagnostics** на локальном компьютере, используя инструкции, приведенные в разделе [Установка стабильной версии модуля](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

   Чтобы установить **xDscDiagnostics** на компьютере Azure, можно использовать [команду AZ VM Run-Command](/cli/azure/vm/run-command) или [Invoke-азвмрункомманд](/powershell/module/azurerm.compute/invoke-azurermvmruncommand). Можно также использовать параметр **выполнить команду** на портале, выполнив действия, описанные в разделе [Запуск сценариев PowerShell в виртуальной машине Windows с помощью команды Run](../../virtual-machines/windows/run-command.md).

   Сведения об использовании **xDscDiagnostics**см. в разделе [Использование xDscDiagnostics для анализа журналов DSC](/powershell/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs), а также [командлетов xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics#cmdlets).
3. **Убедитесь, что узлы и Рабочая область автоматизации содержат необходимые модули:** Настройка требуемого состояния зависит от модулей, установленных на узле.  При использовании конфигурации состояния службы автоматизации Azure импортируйте необходимые модули в учетную запись службы автоматизации, выполнив действия, описанные в разделе [Импорт модулей](../shared-resources/modules.md#import-modules). Конфигурации могут также иметь зависимость от конкретных версий модулей.  Дополнительные сведения см. в разделе [Устранение неполадок модулей](shared-resources.md#modules).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Распространенные ошибки при настройке требуемого состояния (DSC)

### <a name="unsupported-characters"></a>Сценарий. Конфигурацию с специальными символами нельзя удалить с портала

#### <a name="issue"></a>Проблемы

При попытке удалить конфигурацию DSC с портала появляется следующее сообщение об ошибке:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Причина:

Эта ошибка является временной проблемой, которая планируется разрешить.

#### <a name="resolution"></a>Разрешение

* Чтобы удалить конфигурацию, используйте командлет AZ "Remove-Азаутоматиондскконфигуратион".
* Документация для этого командлета еще не обновлена.  До этого момента обратитесь к документации по модулю AzureRM.
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>Сценарий. Не удалось зарегистрировать агент DSC

#### <a name="issue"></a>Проблемы

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

#### <a name="cause"></a>Причина:

Эта ошибка обычно вызвана брандмауэром, компьютером, который находится за прокси-сервером, или другими сетевыми ошибками.

#### <a name="resolution"></a>Разрешение

Убедитесь, что компьютер имеет доступ к соответствующим конечным точкам Azure Automation DSC и повторите попытку. Список требуемых портов и адресов см. в разделе [планирование сети](../automation-dsc-overview.md#network-planning) .

### <a name="failed-not-found"></a>Сценарий. Узел находится в состоянии сбоя с ошибкой "Не найдено"

#### <a name="issue"></a>Проблемы

В отчете об узле указано состояние **сбоя** и содержится сообщение об ошибке:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Причина:

Эта ошибка обычно возникает из-за того, что узлу назначается имя конфигурации (например, ABC) вместо имени конфигурации узла (например, ABC.WebServer).

#### <a name="resolution"></a>Разрешение

* Убедитесь, что вы назначаете узлу имя конфигурации узла, а не "имя конфигурации".
* Конфигурацию узла можно назначить узлу с помощью портала Azure или с помощью командлета PowerShell.

  * Чтобы назначить конфигурацию узла узлу с помощью портал Azure, откройте страницу **узлы DSC** , выберите узел и нажмите кнопку **назначить конфигурацию узла** .
  * Чтобы назначить конфигурацию узла узлу с помощью командлета PowerShell, используйте командлет **Set-AzureRmAutomationDscNode** .

### <a name="no-mof-files"></a>Сценарий. При компиляции конфигурации не были созданы файлы конфигурации узла (MOF-файлы)

#### <a name="issue"></a>Проблемы

Задание компиляции DSC приостановлено с ошибкой:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Причина:

Если выражение, указанное в конфигурации DSC рядом с ключевым словом **Node**, возвращает результат `$null`, конфигурация узла не создается.

#### <a name="resolution"></a>Разрешение

Эту проблему можно устранить одним из следующих способов.

* Убедитесь, что выражение рядом с ключевым словом **node** в определении конфигурации не оценивается как $null.
* Если при компиляции конфигурации вы передаете ConfigurationData, то убедитесь, что передаются ожидаемые значения из [ConfigurationData](../automation-dsc-compile.md), которые необходимы конфигурации.

### <a name="dsc-in-progress"></a>Сценарий. Отчет узла DSC зависает в состоянии "Выполняется"

#### <a name="issue"></a>Проблемы

Агент DSC выводит такое сообщение:

```error
No instance found with given property values
```

#### <a name="cause"></a>Причина:

Вы обновили версию WMF, что привело к повреждению WMI.

#### <a name="resolution"></a>Разрешение

Чтобы устранить эту проблему, следуйте инструкциям в статье об [известных проблемах и ограничениях DSC](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc) .

### <a name="issue-using-credential"></a>Сценарий. Не удается использовать учетные данные в конфигурации DSC

#### <a name="issue"></a>Проблемы

Задание компиляции DSC приостановлено со следующей ошибкой:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Причина:

Вы использовали учетные данные в конфигурации, но не предоставили правильно **ConfigurationData** , чтобы установить **PSDscAllowPlainTextPassword** в значение true для каждой конфигурации узла.

#### <a name="resolution"></a>Разрешение

* Обязательно передавайте подходящую **ConfigurationData** , чтобы установить **PSDscAllowPlainTextPassword** в значение true для каждой конфигурации узла, указанной в конфигурации. См. дополнительные сведения о [ресурсах в службе Automation DSC Azure](../automation-dsc-compile.md#working-with-assets-in-azure-automation-during-compilation).

### <a name="failure-processing-extension"></a>Сценарий. Подключение из расширения DSC, ошибка при обработке расширения

#### <a name="issue"></a>Проблемы

При подключении с помощью расширения DSC происходит сбой, содержащий ошибку:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Причина:

Эта ошибка обычно возникает, когда узлу назначено имя конфигурации узла, которое не существует в службе.

#### <a name="resolution"></a>Разрешение

* Убедитесь, что вы назначаете узлу имя конфигурации узла, которое точно соответствует имени в службе.
* Можно выбрать не включать имя конфигурации узла, что приведет к адаптации узла, но не назначению конфигурации узла.

### <a name="failure-linux-temp-noexec"></a>Сценарий. При применении конфигурации в Linux происходит сбой с общей ошибкой

#### <a name="issue"></a>Проблемы

При применении конфигурации в Linux происходит сбой, содержащий ошибку:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Причина:

Клиенты обнаружили, что если `/tmp` расположение имеет `noexec`значение, текущая версия DSC не сможет применить конфигурации.

#### <a name="resolution"></a>Разрешение

* `noexec` Удалите параметр`/tmp` из расположения.

### <a name="compilation-node-name-overlap"></a>Сценарий. Имена конфигураций узлов, которые перекрываются, могут привести к плохому выпуску

#### <a name="issue"></a>Проблемы

Если для создания конфигурации с несколькими узлами используется один скрипт конфигурации, а некоторые конфигурации узла имеют имя, которое является подмножеством других, то проблема в службе компиляции может привести к назначению неверной конфигурации.  Это происходит только при использовании одного сценария для создания конфигураций с данными конфигурации на узле и только в том случае, если имя перекрывается в начале строки.

Например, если один скрипт конфигурации используется для создания конфигураций на основе данных узла, передаваемых в виде хэш-таблицы с помощью командлетов, а данные узла включают сервер с именем "Server" и "1server".

#### <a name="cause"></a>Причина:

Известная ошибка службы компиляции.

#### <a name="resolution"></a>Разрешение

Лучшим решением является компиляция локально или в конвейере CI/CD и передача файлов MOF непосредственно в службу.  Если компиляция в службе является требованием, то в следующем лучшем случае следует разделить задания компиляции так, чтобы имена не перекрывались.

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.
