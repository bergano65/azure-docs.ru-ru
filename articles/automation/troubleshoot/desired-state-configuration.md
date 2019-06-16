---
title: Устранение неполадок с платформой Desired State Configuration (DSC) в службе автоматизации Azure
description: В этой статье приводятся сведения об устранении неполадок с платформой Desired State Configuration (DSC).
services: automation
ms.service: automation
ms.subservice: ''
author: georgewallace
ms.author: gwallace
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7cb0d77a266dbe8afd331782965e7e9a44663671
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514468"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Устранение неполадок с платформой Desired State Configuration (DSC)

В этой статье приводятся сведения об устранении неполадок с платформой Desired State Configuration (DSC).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Распространенные ошибки при настройке требуемого состояния (DSC)

### <a name="unsupported-characters"></a>Сценарий. Не удалось удалить конфигурацию со специальными символами на портале

#### <a name="issue"></a>Проблема

При попытке удалить конфигурацию DSC на портале, вы видите следующую ошибку:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Причина:

Эта ошибка является временная проблема, которая планируется разрешить.

#### <a name="resolution"></a>Способы устранения:

* Az командлет «Remove-AzAutomationDscConfiguration» предназначена для удаления конфигурации.
* Документация по этому командлету еще не обновлены.  До тех пор обратитесь к документации для модуля AzureRM.
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>Сценарий. Не удалось зарегистрировать агент Dsc

#### <a name="issue"></a>Проблема

При попытке запуска `Set-DscLocalConfigurationManager` или другим командлетом DSC, сообщение об ошибке:

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

Эта ошибка обычно вызвана брандмауэром, компьютера, к которому за прокси-сервер или другие сетевые ошибки.

#### <a name="resolution"></a>Способы устранения:

Убедитесь, что компьютер имеет доступ в соответствующие контрольные точки для DSC службы автоматизации Azure и повторите попытку. Список портов и адресов, необходимых, см. в разделе [Планирование сетей](../automation-dsc-overview.md#network-planning)

### <a name="failed-not-found"></a>Сценарий. Узел находится в состоянии сбоя с ошибкой "Не найдено"

#### <a name="issue"></a>Проблема

В отчете об узле указано состояние **сбоя** и содержится сообщение об ошибке:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Причина:

Эта ошибка обычно возникает из-за того, что узлу назначается имя конфигурации (например, ABC) вместо имени конфигурации узла (например, ABC.WebServer).

#### <a name="resolution"></a>Способы устранения:

* Убедитесь, что назначаете узлу «имя конфигурации узла» и не «конфигурация».
* Конфигурацию узла можно назначить узлу с помощью портала Azure или с помощью командлета PowerShell.

  * Чтобы назначить узлу конфигурацию узла с помощью портала Azure, откройте **узлов DSC** странице, а затем выберите узел и щелкните **назначить конфигурацию узла** кнопки.  
  * Чтобы назначить узлу конфигурацию узла с помощью командлета PowerShell, используйте **Set-AzureRmAutomationDscNode** командлета

### <a name="no-mof-files"></a>Сценарий. При компиляции конфигурации не были созданы файлы конфигурации узла (MOF-файлы)

#### <a name="issue"></a>Проблема

Задание компиляции DSC приостановлено с ошибкой:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Причина:

Если выражение, указанное в конфигурации DSC рядом с ключевым словом **Node**, возвращает результат `$null`, конфигурация узла не создается.

#### <a name="resolution"></a>Способы устранения:

Эту проблему можно устранить одним из следующих способов.

* Убедитесь, что выражение рядом **узел** ключевое слово в определении конфигурации не оценивает $null.
* Если при компиляции конфигурации вы передаете ConfigurationData, то убедитесь, что передаются ожидаемые значения из [ConfigurationData](../automation-dsc-compile.md#configurationdata), которые необходимы конфигурации.

### <a name="dsc-in-progress"></a>Сценарий. Отчет узла DSC зависает в состоянии "Выполняется"

#### <a name="issue"></a>Проблема

Агент DSC выводит такое сообщение:

```error
No instance found with given property values
```

#### <a name="cause"></a>Причина:

Вы обновили версию WMF, что привело к повреждению WMI.

#### <a name="resolution"></a>Способы устранения:

Чтобы устранить эту проблему, следуйте инструкциям в [известные проблемы и ограничения DSC](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) статьи.

### <a name="issue-using-credential"></a>Сценарий. Не удается использовать учетные данные в конфигурации DSC

#### <a name="issue"></a>Проблема

Задание компиляции DSC приостановлено со следующей ошибкой:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Причина:

Вы использовали учетные данные в конфигурации, но не передали соответствующие **ConfigurationData** присвоить **PSDscAllowPlainTextPassword** значение true для конфигурации каждого узла.

#### <a name="resolution"></a>Способы устранения:

* Убедитесь, что передайте правильные **ConfigurationData** присвоить **PSDscAllowPlainTextPassword** значение true для конфигурации каждого узла, указанное в конфигурации. См. дополнительные сведения о [ресурсах в службе Automation DSC Azure](../automation-dsc-compile.md#assets).

### <a name="failure-processing-extension"></a>Сценарий. Помощь с внедрением от расширения dsc, ошибка «Сбой модуля обработки»

#### <a name="issue"></a>Проблема

При возникновении подключение с помощью расширения DSC, сбой с ошибкой:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Причина:

Эта ошибка обычно возникает, когда узел назначается имя конфигурации узла, который не существует в службе.

#### <a name="resolution"></a>Способы устранения:

* Убедитесь, что назначаете узлу имя конфигурации узла, который точно соответствует имени в службе.
* Вы можете не включать имя конфигурации узла, что приведет к адаптации узел, но не Назначение конфигурации узлу

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.
