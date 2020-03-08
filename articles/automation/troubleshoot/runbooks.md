---
title: Устранение ошибок c помощью модулей Runbook службы автоматизации Azure
description: Узнайте, как устранять неполадки и устранять проблемы, которые могут возникнуть при работе с модулями Runbook службы автоматизации Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: b7d876c7f865b8368451ea1b6cc96ade89a59aa8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373429"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Устранение ошибок c помощью модулей Runbook

При возникновении ошибок при выполнении модулей Runbook в службе автоматизации Azure можно выполнить следующие действия, чтобы помочь в диагностике проблем.

1. **Убедитесь, что скрипт Runbook успешно выполнен на локальном компьютере:**  Справочные материалы по языку и учебным модулям см. в документации по [документам PowerShell](/powershell/scripting/overview) или документах [Python](https://docs.python.org/3/) .

   Локальное выполнение скрипта может обнаружить и устранить распространенные ошибки, например:

   - **Отсутствующие модули**
   - **Синтаксические ошибки**
   - **Логические ошибки**

2. **Изучите** [Потоки ошибок](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output) модуля Runbook для конкретных сообщений и сравните их с приведенными ниже ошибками.

3. **Убедитесь, что узлы и Рабочая область автоматизации содержат необходимые модули:** Если модуль Runbook импортирует все модули, убедитесь, что они доступны для учетной записи службы автоматизации, выполнив действия, описанные в разделе [Импорт модулей](../shared-resources/modules.md#import-modules). Обновите модули до последней версии, следуя инструкциям в [статье обновление модулей Azure в службе автоматизации Azure](..//automation-update-azure-modules.md). Дополнительные сведения об устранении неполадок см. в разделе [Устранение неполадок модулей](shared-resources.md#modules).

Если работа модуля Runbook приостановлена или произошла непредвиденная ошибка:

* [Проверка состояния задания](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) определяет состояние Runbook и некоторые возможные причины.
* [Добавьте дополнительные выходные данные](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) в модуль Runbook, чтобы определить, что происходит перед приостановкой модуля Runbook.
* [Обрабатывает все исключения](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) , создаваемые заданием.

## <a name="login-azurerm"></a>Сценарий: для входа выполните команду Login-AzureRMAccount.

### <a name="issue"></a>Проблема

При выполнении модуля Runbook появляется следующая ошибка:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Причина

Эта ошибка может возникать, если не используется учетная запись запуска от имени или истек срок действия учетной записи запуска от имени. См. раздел [Управление учетными записями запуска от имени службы автоматизации Azure](https://docs.microsoft.com/azure/automation/manage-runas-account).

Эта ошибка возникает в двух основных причинах:

* Различные версии модулей AzureRM.
* Вы пытаетесь получить доступ к ресурсам в отдельной подписке.

### <a name="resolution"></a>Решение

Если эта ошибка возникает после обновления одного модуля AzureRM, следует обновить все модули AzureRM до одной версии.

Если вы пытаетесь получить доступ к ресурсам в другой подписке, можно выполнить следующие действия, чтобы настроить разрешения.

1. Перейдите к учетной записи запуска от имени службы автоматизации и скопируйте идентификатор и отпечаток приложения.
  ![скопировать идентификатор и отпечаток приложения](../media/troubleshoot-runbooks/collect-app-id.png)
1. Перейдите к контролю доступа подписки, в котором не размещена учетная запись службы автоматизации, и добавьте новое назначение ролей.
  ![Управление доступом](../media/troubleshoot-runbooks/access-control.png)
1. Добавьте идентификатор приложения, полученный на предыдущем шаге. Выберите разрешения участника.
   ![добавить назначение ролей](../media/troubleshoot-runbooks/add-role-assignment.png)
1. Скопируйте имя подписки для следующего шага.
1. Теперь можно использовать следующий код Runbook для проверки разрешений учетной записи службы автоматизации в другой подписке.

    Замените «\<CertificateThumbprint\>» значением, скопированным на шаге #1, и значением «\<SubscriptionName\>», скопированным на шаге #4.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzureRmSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzureRmSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzureRmContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="unable-to-find-subscription"></a>Сценарий: не удается найти подписку Azure

### <a name="issue"></a>Проблема

При работе с командлетом `Select-AzureSubscription` или `Select-AzureRmSubscription` произошла следующая ошибка.

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Ошибка

Эта ошибка возникает, если:

* имя подписки указано неправильно;

* пользователь Azure Active Directory, который пытается получить информацию о подписке, не имеет прав администратора для этой подписки.

### <a name="resolution"></a>Решение

Чтобы определить, что аутентификация в Azure пройдена и доступ к требуемой подписке получен, сделайте следующее.

1. Проверьте скрипт вне службы автоматизации Azure, чтобы убедиться, что он может работать автономно.
2. Убедитесь, что запускается командлет `Add-AzureAccount` перед запуском командлета `Select-AzureSubscription`.
3. К началу вашего модуля runbook добавьте `Disable-AzureRmContextAutosave –Scope Process`. Этот командлет обеспечит применение любых учетных данных только к выполнению текущего модуля runbook.
4. Если это сообщение об ошибке не исчезло, измените код, добавив параметр **-AzureRmContext** после командлета `Add-AzureAccount`, а затем выполните код.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

## <a name="auth-failed-mfa"></a>Сценарий: не удается выполнить проверку подлинности в Azure из-за включенной многофакторной проверки подлинности

### <a name="issue"></a>Проблема

При аутентификации в Azure с помощью своего имени и пароля вы получаете следующую ошибку:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Причина

Если в учетной записи Azure активирована многофакторная проверка подлинности, применять учетную запись пользователя Azure Active Directory для аутентификации в Azure нельзя. Вместо этого при проверке подлинности в Azure используйте сертификат или субъект-службу.

### <a name="resolution"></a>Решение

Чтобы использовать сертификат с командлетами классической модели развертывания Azure, см. статью [Создание и Добавление сертификата для управления службами Azure](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx). Чтобы использовать субъект-службу с командлетами Azure Resource Manager, ознакомьтесь с [разпортал Azure](../../active-directory/develop/howto-create-service-principal-portal.md) и выполните [проверку подлинности субъекта-службы с помощью Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="get-serializationsettings"></a>Сценарий: вы видите ошибку в потоках заданий о методе get_SerializationSettings

### <a name="issue"></a>Проблема

Вы видите ошибку в потоках задания модуля runbook со следующим сообщением.

```error
Connect-AzureRMAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzureRmAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

### <a name="cause"></a>Причина

Эта ошибка возникла вследствие одновременного использования командлетов AzureRM и Az в модуле runbook. Она возникает при импортировании `Az` перед импортированием `AzureRM`.

### <a name="resolution"></a>Решение

Командлеты AZ и AzureRM нельзя импортировать и использовать в одном модуле Runbook. Дополнительные сведения о командлетах az в службе автоматизации Azure см. в разделе [Поддержка модуля az в службе автоматизации Azure](../az-modules.md).

## <a name="task-was-cancelled"></a>Сценарий. Выполнение модуля Runbook завершается ошибкой отмены задачи

### <a name="issue"></a>Проблема

В модуле Runbook возникает ошибка, аналогичная приведенной ниже:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Причина

Это ошибка может быть вызвана использованием устаревших модулей Azure.

### <a name="resolution"></a>Решение

Эту ошибку можно устранить, обновив модули Azure до последней версии.

В учетной записи службы автоматизации щелкните **модули**и щелкните **обновить модули Azure**. Обновление занимает примерно 15 минут. После его завершения перезапустите проблемный модуль. Дополнительные сведения об обновлении модулей см. в разделе [Обновление модулей Azure в службе автоматизации Azure](../automation-update-azure-modules.md).

## <a name="runbook-auth-failure"></a>Сценарий: Сбой модулей Runbook при работе с несколькими подписками

### <a name="issue"></a>Проблема

При выполнении модулей Runbook модуль Runbook не может управлять ресурсами Azure.

### <a name="cause"></a>Причина

При запуске модуля runbook используется неправильный контекст.

### <a name="resolution"></a>Решение

При работе с несколькими подписками контекст подписки может быть потерян при вызове модулей runbook. Чтобы убедиться, что контекст подписки передан модулям runbook, добавьте параметр `AzureRmContext` в командлет и передайте ему контекст. Также рекомендуется использовать командлет `Disable-AzureRmContextAutosave` с областью **Процесс**, чтобы удостовериться, что учетные данные используются только для текущего модуля runbook.

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with Run As account
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

Дополнительные сведения см. в разделе [Работа с несколькими подписками](../automation-runbook-execution.md#working-with-multiple-subscriptions).

## <a name="not-recognized-as-cmdlet"></a>Сценарий: термин не распознан как имя командлета, функции, скрипта

### <a name="issue"></a>Проблема

В модуле Runbook возникает ошибка, аналогичная приведенной ниже:

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Причина

Эта ошибка может возникать по следующим причинам.

* Модуль, содержащий командлет, не импортируется в учетную запись службы автоматизации.
* Модуль, содержащий командлет, импортирован, но устарел.

### <a name="resolution"></a>Решение

Эту ошибку можно устранить, выполнив одну из следующих задач:

Если модуль является модулем Azure, см. статью [обновление модулей Azure PowerShell в службе автоматизации Azure](../automation-update-azure-modules.md) , чтобы узнать, как обновить модули в учетной записи службы автоматизации.

Если это отдельный модуль, убедитесь, что модуль импортируется в учетную запись службы автоматизации.

## <a name="job-attempted-3-times"></a>Сценарий: предпринята попытка запуска задания Runbook три раза, но не удалось запустить каждый раз

### <a name="issue"></a>Проблема

Модуль Runbook завершается со следующей ошибкой.

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Причина

Эта ошибка возникает из-за одной из следующих проблем.

* Предельный объем памяти. Задание может завершиться ошибкой, если в нем используется более 400 МБ памяти. Документированные ограничения на память, выделенную для песочницы, находятся в статье [ограничения службы автоматизации](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits). 

* Сетевые сокеты. В песочницах Azure ограничено 1000 одновременных сетевых сокетов. См. раздел [ограничения службы автоматизации](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* Модуль несовместим. Зависимости модуля могут быть неправильными. В этом случае модуль Runbook обычно возвращает сообщение "команда не найдена" или "не удается привязать параметр".

* Без проверки подлинности с Active Directory для "песочницы". Модуль Runbook попытался вызвать исполняемый файл или подпроцесс, который выполняется в песочнице Azure. Настройка модулей Runbook для проверки подлинности в Azure AD с помощью библиотеки проверки подлинности Azure Active Directory (ADAL) не поддерживается.

* Слишком много данных об исключениях. Модуль Runbook попытался записать слишком много данных об исключении в поток вывода.

### <a name="resolution"></a>Решение

* Ограничение памяти, сетевые сокеты. Предлагаемые способы работы в рамках ограничения памяти: разделение рабочей нагрузки между несколькими модулями Runbook, обработка меньшего объема данных в памяти, предотвращение записи ненужных результатов из модулей Runbook и определение того, сколько контрольных точек записывается в рабочий процесс PowerShell модулей Runbook. Используйте метод Clear, например `$myVar.clear`, чтобы очистить переменные и использовать `[GC]::Collect` для немедленного выполнения сборки мусора. Эти действия уменьшают объем памяти, занимаемой модулем runbook во время выполнения.

* Модуль несовместим. Обновите модули Azure, выполнив действия, описанные в статье [обновление модулей Azure PowerShell в службе автоматизации Azure](../automation-update-azure-modules.md).

* Без проверки подлинности с помощью ADAL для песочницы. При проверке подлинности в Azure AD с помощью модуля Runbook убедитесь, что модуль Azure AD доступен в вашей учетной записи службы автоматизации. Не забудьте предоставить учетной записи запуска от имени необходимые разрешения для выполнения задач, которые автоматизируется модулем Runbook.

  Если модуль Runbook не может вызвать исполняемый файл или подпроцесс, выполняющийся в песочнице Azure, используйте Runbook в [гибридной рабочей роли Runbook](../automation-hrw-run-runbooks.md). Гибридные рабочие роли не ограничиваются ограничениями памяти и сети, которые имеют службы "песочницы Azure".

* Слишком много данных об исключениях. В потоке выходных данных задания имеется ограничение в 1 МБ. Убедитесь, что модуль Runbook содержит вызовы исполняемого или вложенного процесса в блоке try/catch. Если операции создают исключение, то код будет записывать сообщение из исключения в переменную автоматизации. Эта методика предотвращает запись сообщения в поток вывода задания.

## <a name="sign-in-failed"></a>Сценарий: сбой входа в учетную запись Azure

### <a name="issue"></a>Проблема

При работе с командлетами `Add-AzureAccount` или `Connect-AzureRmAccount` появляется одна из следующих ошибок:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Причина

Эта ошибка возникает, если указано недопустимое имя ресурса учетных данных. Ошибка также может возникнуть, если имя пользователя и пароль, которые вы ввели для настройки ресурса учетных данных службы автоматизации, указаны неправильно.

### <a name="resolution"></a>Решение

Чтобы определить источник проблемы, необходимо выполнить следующие действия.

1. Убедитесь, что вы не использовали специальные символы. В имени ресурса учетных данных Службы автоматизации Azure, используемом для подключения к Azure, запрещается вводить такие специальные символы, как **\@** .
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
       #Logging in to Azure...
       $connectionResult = Connect-AzureRmAccount `
                              -ServicePrincipal `
                              -TenantId $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="child-runbook-object"></a>Сценарий: ссылка на объект не задает экземпляр объекта

### <a name="issue"></a>Проблема

При вызове дочернего модуля Runbook с параметром `-Wait` появляется следующее сообщение об ошибке, а выходной поток содержит объект:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Причина

Существует известная ошибка, при которой Start-AzureRmAutomationRunbook неправильно обрабатывает выходной поток, если он содержит объекты.

### <a name="resolution"></a>Решение

Чтобы устранить эту проблему, рекомендуется реализовать логику опроса и получить выходные данные с помощью командлета [Get-азурермаутоматионжобаутпут](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) . Эта логика показана в следующем примере.

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzureRmAutomationJob
}

$jobResults | Get-AzureRmAutomationJobOutput | Get-AzureRmAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="fails-deserialized-object"></a>Сценарий: сбой модуля Runbook из-за десериализованного объекта

### <a name="issue"></a>Проблема

Выполнение модуля Runbook завершилось ошибкой.

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Причина

Когда runbook является рабочим процессом PowerShell, он хранит сложные объекты в десериализированном формате, чтобы сохранить состояние модуля при приостановке рабочего процесса.

### <a name="resolution"></a>Решение

При использовании одного из трех следующих способов проблема будет устранена.

* Если сложные объекты передаются от одного командлета к другому через конвейер, включите эти командлеты в InlineScript.
* Передайте только требуемое имя или значение сложного объекта, а не весь объект.
* Используйте модуль Runbook PowerShell, а не модуль Runbook рабочего процесса PowerShell.

## <a name="quota-exceeded"></a>Сценарий: сбой задания Runbook из-за превышения выделенной квоты

### <a name="issue"></a>Проблема

Выполнение задания Runbook завершилось ошибкой.

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Причина

Эта ошибка возникает, когда время выполнения задания превышает квоту для учетной записи в 500 бесплатных минут. Эта квота применяется ко всем типам задач на выполнение заданий. К этим типам задач может относиться тестирование задания, запуск задания на портале Azure, выполнение задания с помощью веб-перехватчиков, а также планирование задания с помощью портала Azure или центра обработки данных. Дополнительные сведения см. на странице [цен на службу автоматизации](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Решение

Если на обработку задач вам требуется более 500 минут в месяц, выберите для подписки уровень "Базовый" вместо уровня "Бесплатный". Это можно сделать так.

1. Войдите в свою подписку Azure.
2. Выберите учетную запись службы автоматизации для обновления.
3. Щелкните **Параметры**, а затем — **цены**.
4. Нажмите кнопку **Включить** в нижней части страницы, чтобы обновить свою учетную запись до уровня **Базовый**.

## <a name="cmdlet-not-recognized"></a>Сценарий: не удается распознать командлет при выполнении модуля Runbook

### <a name="issue"></a>Проблема

Выполнение задания Runbook завершилось ошибкой.

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Причина

Эта ошибка возникает, когда модулю PowerShell не удается найти командлет, который используется в runbook. Такая ошибка возникает, когда в учетной записи отсутствует модуль, содержащий командлет, когда возникает конфликт имен с именем модуля runbook, а также когда командлет существует и в другом модуле, а службе автоматизации Azure не удается разрешить имя.

### <a name="resolution"></a>Решение

Эту проблему можно устранить одним из следующих способов.

* Проверьте правильность ввода имени командлета.
* Убедитесь, что командлет существует в учетной записи службы автоматизации, а конфликты отсутствуют. Чтобы проверить наличие командлета, откройте модуль Runbook в режиме редактирования и найдите требуемый командлет в библиотеке или выполните команду `Get-Command <CommandName>`. Убедившись в наличии командлета в учетной записи и отсутствии конфликта имен с другими командлетами или модулями runbook, добавьте его на холст. При этом в runbook должен использоваться допустимый набор параметров.
* Если обнаружен конфликт имен, а сам командлет доступен в двух разных модулях, эту проблему можно решить использованием полного имени командлета. Например, можно использовать следующий формат: **имя_модуля\имя_командлета**.
* Если runbook выполняется локально в группе гибридных рабочих ролей, убедитесь, что на компьютере, на котором размещена гибридная рабочая роль, установлен соответствующий модуль или командлет.

## <a name="long-running-runbook"></a>Сценарий: сбой длительного выполнения Runbook

### <a name="issue"></a>Проблема

Runbook находится в **остановленном** состоянии в течение 3 часов после выполнения команды. Также может появиться сообщение об ошибке.

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

Такое поведение характерно в изолированных средах Azure из-за [общего](../automation-runbook-execution.md#fair-share) мониторинга процессов в службе автоматизации Azure. Если он выполняется дольше трех часов, справедливое предоставление общего доступа автоматически останавливает модуль Runbook. Состояние модуля Runbook, который находится за пределами длительного общего времени, отличается от типа Runbook. Модули runbook PowerShell и Python переходят в состояние **Остановлено**. Модули runbook рабочих процессов PowerShell переходят в состояние **Сбой**.

### <a name="cause"></a>Причина

Модуль Runbook выполнялся в течение 3-часового лимита, разрешенного долево в песочнице Azure.

### <a name="resolution"></a>Решение

Одно из рекомендуемых решений — запустить runbook в [гибридной рабочей роли Runbook](../automation-hrw-run-runbooks.md).

Гибридные рабочие роли не ограничиваются предельным числом модулей Runbook, допустимым в течение 3 часов. Модули Runbook, выполняемые в гибридных рабочих ролях Runbook, должны быть разработаны для поддержки поведения при перезапуске в случае непредвиденных проблем с локальной инфраструктурой.

Еще один вариант — оптимизировать runbook с помощью [дочерних модулей runbook](../automation-child-runbooks.md). Если модуль runbook циклически выполняет одну и ту же функцию для ряда ресурсов, например определенную операцию в нескольких базах данных, такую функцию можно переместить в дочерний модуль runbook. Каждый из этих дочерних модулей runbook выполняется в параллельном режиме отдельными процессами. Это уменьшает количество времени на завершение родительского модуля runbook.

Командлеты PowerShell для реализации дочерних runbook:

[Start-AzureRMAutomationRunbook.](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) Этот командлет позволяет запустить runbook и передать в него параметры.

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob). Если есть операции, которые нужно выполнить после выполнения дочернего модуля runbook, этот командлет позволяет проверить состояние задания для каждого дочернего элемента.

## <a name="expired webhook"></a>Сценарий: состояние: 400 недопустимый запрос при вызове веб-перехватчика

### <a name="issue"></a>Проблема

При попытке вызова веб-перехватчика для модуля runbook службы автоматизации Azure возникает следующая ошибка.

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Причина

Вызываемый веб-перехватчик отключен, или срок его действия истек.

### <a name="resolution"></a>Решение

если веб-перехватчик отключен, его можно повторно включить на портале Azure. Когда истек срок действия веб-перехватчика, его необходимо удалить или создать заново. Если срок действия еще не истек, можно только [обновить веб-перехватчик](../automation-webhooks.md#renew-webhook).

## <a name="429"></a>Сценарий: 429: частота запросов в настоящее время слишком велика...

### <a name="issue"></a>Проблема

При выполнении командлета `Get-AzureRmAutomationJobOutput` вы получаете следующее сообщение об ошибке:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Причина

эта ошибка может возникать при извлечении выходных данных задания из модуля Runbook, обладающего множеством [подробных потоков](../automation-runbook-output-and-messages.md#verbose-stream).

### <a name="resolution"></a>Решение

Эту проблему можно решить двумя способами:

* изменить модуль Runbook и сократить число создаваемых потоков заданий;
* уменьшите количество потоков, полученных при выполнении командлета. Для этого можно указать командлету `-Stream Output` параметр `Get-AzureRmAutomationJobOutput`, чтобы получить только потоки вывода. 

## <a name="cannot-invoke-method"></a>Сценарий: сбой задания PowerShell с ошибкой: не удается вызвать метод

### <a name="issue"></a>Проблема

При запуске задания PowerShell в модуле Runbook, запущенном в Azure, появляется следующее сообщение об ошибке:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Причина

Эта ошибка может возникать при запуске задания PowerShell в модуле Runbook, работающем в Azure. Это может произойти из-за того, что модули Runbook, запущенные в песочнице Azure, могут не работать в [полном языковом режиме](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Решение

Эту проблему можно решить двумя способами:

* Вместо использования `Start-Job`используйте `Start-AzureRmAutomationRunbook` для запуска модуля Runbook
* Если в модуле Runbook есть это сообщение об ошибке, запустите его в гибридной рабочей роли Runbook.

Дополнительные сведения об этом поведении и других возможностях модулей Runbook службы автоматизации Azure см. в разделе [поведение модуля Runbook](../automation-runbook-execution.md#runbook-behavior).

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>Сценарий: Гибридная Рабочая роль Runbook Linux получает запрос на ввод пароля при подписании модуля Runbook

### <a name="issue"></a>Проблема

При выполнении команды **sudo** для гибридной рабочей роли Runbook Linux получается непредвиденный запрос на ввод пароля.

### <a name="cause"></a>Причина

Учетная запись нксаутоматионусер для агента Log Analytics для Linux неправильно настроена в файле "Sudo". Гибридной рабочей роли Runbook требуется соответствующая настройка разрешений учетной записи и других данных, чтобы она могла подписывать модули Runbook в рабочей роли Runbook для Linux.

### <a name="resolution"></a>Решение

* Убедитесь, что Гибридная Рабочая роль Runbook имеет исполняемый файл GnuPG (GPG) на компьютере.

* Проверьте конфигурацию учетной записи нксаутоматионусер в файле "Sudo". См. раздел [Запуск модулей Runbook в гибридной рабочей роли Runbook](../automation-hrw-run-runbooks.md) .

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>Сценарий: сбой командлета в Runbook PowerShell PnP в службе автоматизации Azure

### <a name="issue"></a>Проблема

Когда модуль Runbook записывает автоматически созданный объект PnP PowerShell в выходные данные службы автоматизации Azure, выходные данные командлета не могут выполнять потоковую передачу в службу автоматизации.

### <a name="cause"></a>Причина

Чаще всего эта проблема возникает, когда служба автоматизации Azure обрабатывает модули Runbook, которые вызывают командлеты PnP PowerShell, например **Add-пнплиститем**, без перехвата возвращаемых объектов.

### <a name="resolution"></a>Решение

Измените скрипты, чтобы присвоить значения переменным, чтобы командлеты не предпринимали попытку записи целых объектов в стандартный вывод. Скрипт может перенаправить поток вывода в командлет, как показано ниже.

```azurecli
  $null = add-pnplistitem
```
Если сценарий анализирует выходные данные командлета, сценарий должен сохранить выходные данные в переменной и манипулировать переменной вместо просто потоковой передачи выходных данных.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="other"></a>Моя проблема не указана выше

В следующих разделах перечислены другие распространенные ошибки, а также сопутствующая документация, помогающая устранить проблему.

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>Гибридная рабочая роль runbook не выполняет задания или не отвечает

Если вы используете задания с помощью гибридной рабочей роли, а не в службе автоматизации Azure, вам может потребоваться [устранить неполадки в работе гибридной рабочей роли](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker).

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>Модуль runbook прекратил работу с ошибкой "Нет разрешения" или аналогичной

Учетные записи запуска от имени могут не иметь тех же разрешений на ресурсы Azure, что и текущая учетная запись. Убедитесь, что учетная запись запуска от имени имеет [разрешения на доступ к любым ресурсам](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) , используемым в скрипте.

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Модули runbook работали, но внезапно были остановлены

* Если модули Runbook ранее выполнялись, но остановлены, убедитесь, что срок действия [учетной записи запуска от имени](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal) не истек.
* Если вы используете веб-перехватчики для запуска модулей Runbook, убедитесь, что срок действия [веб-перехватчика](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook) не истек.

### <a name="issues-passing-parameters-into-webhooks"></a>Проблемы при передаче параметров в веб-перехватчики

Дополнительные сведения о передаче параметров в веб-перехватчики см. в статье [Запуск Runbook из веб-перехватчика](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters).

### <a name="issues-using-az-modules"></a>Проблемы с использованием модулей AZ

Использование модулей AZ modules и AzureRM modules в одной учетной записи службы автоматизации не поддерживается. Дополнительные сведения см. в статье о [модулях AZ modules в модулях Runbook](https://docs.microsoft.com/azure/automation/az-modules) .

### <a name="inconsistent-behavior-in-runbooks"></a>Несогласованное поведение модулей runbook

Следуйте указаниям в [выполнении модуля Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) , чтобы избежать проблем с параллельными заданиями, ресурсами, созданными несколько раз, или другой логикой с учетом времени в модулях Runbook.

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Модуль Runbook завершается ошибкой без разрешения, запрещен (403) или какой-либо вариант

Учетные записи запуска от имени могут не иметь тех же разрешений на ресурсы Azure, что и текущая учетная запись. Убедитесь, что учетная запись запуска от имени имеет [разрешения на доступ к любым ресурсам](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) , используемым в скрипте.

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Модули runbook работали, но внезапно были остановлены

* Если модули Runbook ранее выполнялись, но остановлены, убедитесь, что срок действия учетной записи запуска от имени не истек. См. статью [продление сертификации](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Если вы используете веб-перехватчики для запуска модулей Runbook, убедитесь, что срок действия веб-перехватчика [не истек](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook).

### <a name="passing-parameters-into-webhooks"></a>Передача параметров в веб-перехватчики

Дополнительные сведения о передаче параметров в веб-перехватчики см. в статье [Запуск Runbook из веб-перехватчика](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters).

### <a name="using-az-modules"></a>Использование модулей Az

Использование модулей AZ modules и AzureRM modules в одной учетной записи службы автоматизации не поддерживается. См. раздел [AZ modules in runbooks](https://docs.microsoft.com/azure/automation/az-modules).

### <a name="using-self-signed-certificates"></a>Использование самозаверяющих сертификатов

Сведения о том, как использовать самозаверяющие сертификаты, см. в разделе [Создание нового сертификата](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate).

## <a name="recommended-documents"></a>Рекомендуемые документы

* [Запуск модуля Runbook в службе автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Выполнение Runbook в службе автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.
