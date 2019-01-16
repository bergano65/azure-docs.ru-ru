---
title: Устранение ошибок c помощью модулей Runbook службы автоматизации Azure
description: Подробные сведения об устранении ошибок c помощью модулей Runbook службы автоматизации Azure
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: f5663842a4d861ed6eb76de859b870aa7114cb04
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063647"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Устранение ошибок c помощью модулей Runbook

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Ошибки, связанные с аутентификацией, при работе с модулями Runbook службы автоматизации Azure

### <a name="sign-in-failed"></a>Сценарий: не удается войти в учетную запись Azure

#### <a name="issue"></a>Проблема

При работе с командлетом `Add-AzureAccount` или `Connect-AzureRmAccount` будет получена следующая ошибка.
:

```
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Причина:

Эта ошибка возникает, если указаны недопустимые имя ресурса учетных данных или имя пользователя и пароль, которые использовались для настройки ресурса учетных данных службы автоматизации.

#### <a name="resolution"></a>Способы устранения:

Чтобы определить источник проблемы, необходимо выполнить следующие действия.  

1. Убедитесь, что в имени ресурса учетных данных службы автоматизации, используемом для подключения к Azure, отсутствуют специальные знаки, включая **@**.  
2. Убедитесь, что вы можете использовать имя пользователя и пароль, которые хранятся в учетных данных службы автоматизации Azure в редакторе локальной интегрированной среды сценариев PowerShell. Можно проверить правильность имени пользователя и пароля, выполнив следующие командлеты в интегрированной среде сценариев PowerShell.  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Локальный сбой аутентификации означает, что учетные данные Azure Active Directory были указаны неправильно. Сведения о том, как правильно настроить учетную запись Azure Active Directory, см. в записи блога об [аутентификации в Azure с помощью Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/).  

4. Если это временная ошибка, попробуйте добавить логику повторных попыток в процедуру аутентификации, чтобы сделать ее более надежной.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
   $LogonAttempt++
   # Logging in to Azure...
   $connectionResult = Connect-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $servicePrincipalConnection.TenantId `
      -ApplicationId $servicePrincipalConnection.ApplicationId `
      -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

   Start-Sleep -Seconds 30
   }
   ```

### <a name="unable-to-find-subscription"></a>Сценарий: не удается найти подписку Azure

#### <a name="issue"></a>Проблема

При работе с командлетом `Select-AzureSubscription` или `Select-AzureRmSubscription` произошла следующая ошибка.

```
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Ошибка

Эта ошибка возникает, если указано недействительное имя подписки, а также если пользователь Azure Active Directory, который пытается получить сведения о подписке, не является ее администратором.

#### <a name="resolution"></a>Способы устранения:

Чтобы определить, что аутентификация в Azure пройдена и доступ к требуемой подписке получен, сделайте следующее.  

1. Проверьте скрипт вне службы автоматизации Azure, чтобы убедиться, что он может работать автономно.
2. Убедитесь, что запускается командлет `Add-AzureAccount` перед запуском командлета `Select-AzureSubscription`. 
3. К началу вашего модуля runbook добавьте `Disable-AzureRmContextAutosave –Scope Process`. Это обеспечит применение любых учетных данных только к выполнению текущего модуля runbook.
4. Если это сообщение об ошибке не исчезло, измените код, добавив параметр **-AzureRmContext** после командлета `Add-AzureAccount`, а затем выполните код.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

### <a name="auth-failed-mfa"></a>Сценарий. не удается выполнить проверку подлинности в Azure из-за включенной многофакторной проверки подлинности

#### <a name="issue"></a>Проблема

При аутентификации в Azure с помощью своего имени и пароля вы получаете следующую ошибку:

```
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Причина:

Если в учетной записи Azure активирована многофакторная проверка подлинности, применять учетную запись пользователя Azure Active Directory для аутентификации в Azure нельзя. Вместо этого при проверке подлинности в Azure используйте сертификат или субъект-службу.

#### <a name="resolution"></a>Способы устранения:

Сведения об использовании сертификата с командлетами классической модели развертывания Azure см. в руководстве по [созданию и добавлению сертификатов для управления службами Azure](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Сведения об использовании субъекта-службы с командлетами диспетчера ресурсов Azure см. в статьях о [создании субъекта-службы с помощью портала Azure](../../active-directory/develop/howto-create-service-principal-portal.md) и [аутентификации субъекта-службы в диспетчере ресурсов Azure](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="common-errors-when-working-with-runbooks"></a>Распространенные ошибки при работе с модулями Runbook

### <a name="task-was-cancelled"></a>Сценарий: выполнение модуля Runbook завершилось ошибкой. Задача была отменена

#### <a name="issue"></a>Проблема

В модуле Runbook возникает ошибка, аналогичная приведенной ниже:

```
Exception: A task was canceled.
```

#### <a name="cause"></a>Причина:

Это ошибка может быть вызвана использованием устаревших модулей Azure.

#### <a name="resolution"></a>Способы устранения:

Эту ошибку можно устранить, обновив модули Azure до последней версии.

В учетной записи службы автоматизации щелкните **Модули** и **Обновить модули Azure**. Обновление занимает примерно 15 минут. После его завершения перезапустите проблемный модуль. Дополнительные сведения об обновлении модулей см. в разделе [Обновление модулей Azure в службе автоматизации Azure](../automation-update-azure-modules.md).

### <a name="runbook-auth-failure"></a>Сценарий. Сбой модуля runbook при работе с несколькими подписками

#### <a name="issue"></a>Проблема

При выполнении модуля runbooks с командлетом `Start-AzureRmAutomationRunbook` модуль не может управлять ресурсами Azure.

#### <a name="cause"></a>Причина:

При запуске модуля runbook используется неправильный контекст.

#### <a name="resolution"></a>Способы устранения:

При работе с несколькими подписками контекст подписки может быть потерян при вызове модулей runbook. Чтобы убедиться, что контекст подписки передан модулям runbook, добавьте параметр `AzureRmContext` в командлет и передайте ему контекст. Также рекомендуется использовать командлет `Disable-AzureRmContextAutosave` с областью **Процесс**, чтобы удостовериться, что учетные данные используются только для текущего модуля runbook.

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRmContext $AzureContext `
    –Parameters $params –wait
```

### <a name="not-recognized-as-cmdlet"></a>Сценарий. сбой модуля Runbook из-за отсутствующего командлета

#### <a name="issue"></a>Проблема

В модуле Runbook возникает ошибка, аналогичная приведенной ниже:

```
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>Причина:

Ошибка может быть вызвана следующими причинами.

1. Модуль, содержащий командлет, не импортирован в учетную запись службы автоматизации.
2. Модуль, содержащий командлет, импортирован, но устарел.

#### <a name="resolution"></a>Способы устранения:

Эту ошибку можно устранить, выполнив одну из следующих задач:

Если модуль является модулем Azure, см. раздел [Как обновить модули Azure PowerShell в службе автоматизации Azure](../automation-update-azure-modules.md), чтобы узнать, как обновить модули в учетной записи службы автоматизации.

Если это отдельный модуль, убедитесь, что он импортирован в учетную запись службы автоматизации.

### <a name="job-attempted-3-times"></a>Сценарий: было предпринято три попытки запуска задания Runbook, но все они были неудачными

#### <a name="issue"></a>Проблема

Выполнение модуля Runbook завершилось ошибкой.

```
The job was tried three times but it failed
```

#### <a name="cause"></a>Причина:

Ошибка может быть вызвана следующими причинами.

1. Предельный объем памяти. Ограничения на объем памяти, выделяемый на Sandbox, описаны в разделе [Ограничения автоматизации](../../azure-subscription-service-limits.md#automation-limits). Задание может завершиться ошибкой, если оно использует более 400 МБ памяти.

2. Сетевые сокеты. В песочницах Azure разрешено одновременно использовать не более 1000 сетевых сокетов, как описано в разделе об [ограничениях службы автоматизации](../../azure-subscription-service-limits.md#automation-limits).

3. Модуль несовместим. Такая ситуация может возникнуть, если зависимости модуля заданы неправильно. В этом случае runbook обычно возвращает сообщение об ошибке "Команда не найдена" или "Не удается привязать параметр".

#### <a name="resolution"></a>Способы устранения:

Эту проблему можно устранить одним из следующих способов.

* Чтобы работа выполнялась в заданных пределах объема памяти, рекомендуется разделить рабочую нагрузку между несколькими модулями runbook, не обрабатывать слишком большой объем данных в памяти, не записывать ненужные выходные данные из модулей runbook и учитывать, сколько контрольных точек записывается в модули runbook рабочих процессов PowerShell. Для очистки переменной можно использовать метод clear, например `$myVar.clear()`, а для немедленного сбора мусора — `[GC]::Collect()`. Это позволит снизить объем занимаемой памяти во время выполнения модуля runbook.

* Обновите модули Azure с помощью действий, описанных в статье [How to update Azure PowerShell modules in Azure Automation](../automation-update-azure-modules.md) (Как обновить модули Azure PowerShell в службе автоматизации Azure).  

* Другим решением является запуск модуля Runbook в [гибридной рабочей роли Runbook](../automation-hrw-run-runbooks.md). В гибридных рабочих ролях нет таких ограничений памяти и сетевых ресурсов, как в песочницах Azure.

### <a name="fails-deserialized-object"></a>Сценарий: сбой модуля Runbook из-за десериализованного объекта

#### <a name="issue"></a>Проблема

Выполнение модуля Runbook завершилось ошибкой.

```
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>Причина:

Когда runbook является рабочим процессом PowerShell, он хранит сложные объекты в десериализированном формате, чтобы сохранить состояние модуля при приостановке рабочего процесса.

#### <a name="resolution"></a>Способы устранения:

При использовании одного из трех следующих способов проблема будет устранена.

1. Если сложные объекты передаются от одного командлета к другому через конвейер, включите эти командлеты в InlineScript.
2. Передайте только требуемое имя или значение сложного объекта, а не весь объект.
3. Используйте модуль Runbook PowerShell, а не модуль Runbook рабочего процесса PowerShell.

### <a name="quota-exceeded"></a>Сценарий: не удается выполнить задание модуля Runbook из-за превышения выделенной квоты

#### <a name="issue"></a>Проблема

Выполнение задания Runbook завершилось ошибкой.

```
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Причина:

Эта ошибка возникает, когда время выполнения задания превышает квоту для учетной записи в 500 бесплатных минут. Эта квота применяется ко всем типам задач при выполнении задания, включая тестирование задания, запуск задания на портале, выполнение задания с помощью веб-перехватчиков, а также планирование задания с помощью портала Azure или центра обработки данных. Дополнительные сведения см. на странице [цен на службу автоматизации](https://azure.microsoft.com/pricing/details/automation/).

#### <a name="resolution"></a>Способы устранения:

Если на обработку задач вам требуется более 500 минут в месяц, выберите для подписки уровень "Базовый" вместо уровня "Бесплатный". Это можно сделать так.  

1. Войдите в свою подписку Azure.  
2. Выберите учетную запись службы автоматизации, которую вы хотите обновить.  
3. Щелкните **Параметры** > **Цены**.
4. Нажмите кнопку **Включить** в нижней части страницы, чтобы обновить свою учетную запись до уровня **Базовый**.

### <a name="cmdlet-not-recognized"></a>Сценарий: не удается распознать командлет при выполнении модуля Runbook

#### <a name="issue"></a>Проблема

Выполнение задания Runbook завершилось ошибкой.

```
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Причина:

Эта ошибка возникает, когда модулю PowerShell не удается найти командлет, который используется в runbook. Такое случается, когда в учетной записи отсутствует модуль, содержащий командлет, когда возникает конфликт имен с именем runbook, а также когда командлет существует и в другом модуле, а службе автоматизации не удается разрешить имя.

#### <a name="resolution"></a>Способы устранения:

Эту проблему можно устранить одним из следующих способов.  

* Проверьте правильность ввода имени командлета.  
* Убедитесь, что командлет существует в учетной записи службы автоматизации, а конфликты отсутствуют. Чтобы проверить наличие командлета, откройте модуль Runbook в режиме редактирования и найдите требуемый командлет в библиотеке или выполните команду `Get-Command <CommandName>`. Убедившись в наличии командлета в учетной записи и отсутствии конфликта имен с другими командлетами или модулями runbook, добавьте его на холст. При этом в runbook должен использоваться допустимый набор параметров.  
* Если обнаружен конфликт имен, а сам командлет доступен в двух разных модулях, проблему можно решить использованием полного имени командлета. Например, можно использовать следующий формат: **имя_модуля\имя_командлета**.  
* Если runbook выполняется локально в группе гибридных рабочих ролей, убедитесь, что на компьютере, на котором размещена гибридная рабочая роль, установлен соответствующий модуль или командлет.

### <a name="long-running-runbook"></a>Сценарий: долго работающий модуль Runbook не выполняется

#### <a name="issue"></a>Проблема

Runbook находится в **остановленном** состоянии в течение 3 часов после выполнения команды. Также может появиться сообщение об ошибке.

```
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

Эта реакция на событие в песочницах Azure связана с мониторингом "справедливого распределения" процессов в службе автоматизации Azure, которая автоматически останавливает runbook, который выполняется дольше 3 часов. Состояние runbook, который превышает ограничение времени справедливого распределения, зависит от типа runbook. Модули runbook PowerShell и Python переходят в состояние **Остановлено**. Модули runbook рабочих процессов PowerShell переходят в состояние **Сбой**.

#### <a name="cause"></a>Причина:

Runbook выполнялся дольше трехчасового ограничения, заданного справедливым распределением в песочнице Azure.

#### <a name="resolution"></a>Способы устранения:

Одно из рекомендуемых решений — запустить runbook в [гибридной рабочей роли Runbook](../automation-hrw-run-runbooks.md).

Гибридные рабочие роли не ограничены тремя часами [справедливого распределения](../automation-runbook-execution.md#fair-share), как песочницы Azure. Хотя гибридные рабочие роли Runbook не ограничиваются 3 часами справедливого распределения, модули runbook, выполняемые в гибридных рабочих ролях Runbook, все равно следует разрабатывать с поддержкой перезапуска при возникновении непредвиденных проблем локальной инфраструктуры.

Еще один вариант — оптимизировать runbook с помощью [дочерних модулей runbook](../automation-child-runbooks.md). Если runbook циклически выполняет одну и ту же функцию для ряда ресурсов, например определенную операцию в нескольких базах данных, такую функцию можно переместить в дочерний runbook. Каждый из этих дочерних модулей runbook выполняется в параллельном режиме отдельными процессами. Это уменьшает количество времени на завершение родительского модуля runbook.

Командлеты PowerShell для реализации дочерних runbook:

[Start-AzureRMAutomationRunbook.](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) Этот командлет позволяет запустить runbook и передать в него параметры.

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob). Этот командлет позволяет проверить состояние задания для каждого дочернего элемента, если есть операции, которые нужно выполнить после выполнения дочернего runbook.

### <a name="expired webhook"></a>Сценарий: Состояние. 400 – ошибочный запрос при вызове веб-перехватчика

#### <a name="issue"></a>Проблема

При попытке вызова веб-перехватчика для модуля runbook службы автоматизации Azure возникает следующая ошибка.

```error
400 Bad Request : This webhook has expired or is disabled
```

#### <a name="cause"></a>Причина:

вызываемый веб-перехватчик отключен, или истек срок его действия.

#### <a name="resolution"></a>Способы устранения:

если веб-перехватчик отключен, его можно повторно включить на портале Azure. Когда истек срок действия веб-перехватчика, его необходимо удалить или создать заново. Если срок действия еще не истек, можно только [обновить веб-перехватчик](../automation-webhooks.md#renew-webhook).

### <a name="429"></a>Сценарий: 429: в настоящее время слишком великая частота запросов. Повторите попытку позже

#### <a name="issue"></a>Проблема

При выполнении командлета `Get-AzureRmAutomationJobOutput` вы получаете следующее сообщение об ошибке:

```
429: The request rate is currently too large. Please try again
```

#### <a name="cause"></a>Причина:

эта ошибка может возникать при извлечении выходных данных задания из модуля Runbook, обладающего множеством [подробных потоков](../automation-runbook-output-and-messages.md#verbose-stream).

#### <a name="resolution"></a>Способы устранения:

Эту проблему можно решить двумя способами:

* изменить модуль Runbook и сократить число создаваемых потоков заданий;
* уменьшите количество потоков, полученных при выполнении командлета. Для этого можно указать командлету `Get-AzureRmAutomationJobOutput` параметр `-Stream Output`, чтобы получить только потоки вывода. 

## <a name="common-errors-when-importing-modules"></a>Распространенные ошибки при импорте модулей

### <a name="module-fails-to-import"></a>Сценарий: не удается импортировать модуль, или после импорта не выполняются командлеты

#### <a name="issue"></a>Проблема

Модулю не удается осуществить импорт, либо импорт выполнен успешно, но командлеты не извлекаются.

#### <a name="cause"></a>Причина:

Ниже приведены наиболее распространенные причины, из-за которых модуль не может успешно завершить импорт в службу автоматизации Azure.

* Структура не соответствует той, которая требуется для службы автоматизации.
* Модуль зависит от другого модуля, который не был развернут в учетной записи службы автоматизации.
* В папке отсутствуют зависимости модуля.
* Командлет `New-AzureRmAutomationModule` используется для передачи модуля, при этом полный путь для хранения не указан или модуль не загружен с помощью общедоступного URL-адреса.

#### <a name="resolution"></a>Способы устранения:

Эту проблему можно устранить одним из следующих способов.

* Убедитесь, что модуль имеет следующий формат: ModuleName.Zip **->** имя_модуля или номер версии **->** (имя_модуля.psm1, имя_модуля.psd1).
* Откройте файл PSD1 и проверьте, есть ли у модуля зависимости. Если зависимости есть, отправьте эти модули в учетную запись службы автоматизации.
* Убедитесь, что все указанные библиотеки DLL находятся в папке модуля.

## <a name="next-steps"></a>Дополнительная информация

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.