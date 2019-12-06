---
title: Устранение ошибок c помощью модулей Runbook службы автоматизации Azure
description: Подробные сведения об устранении ошибок c помощью модулей Runbook службы автоматизации Azure
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 84a17cb4468f60abf2463e6aa3ca331466aad247
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850148"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Устранение ошибок c помощью модулей Runbook

При возникновении ошибок при выполнении модулей Runbook в службе автоматизации Azure можно выполнить следующие действия, чтобы помочь в диагностике проблемы.

1. **Убедитесь, что скрипт Runbook успешно выполнен на локальном компьютере:**  Справочные материалы по языку и учебным модулям см. в документации по [документам PowerShell](/powershell/scripting/overview) или документах [Python](https://docs.python.org/3/) .

   Локальное выполнение скрипта может обнаружить и устранить распространенные ошибки, например:

   - **Отсутствующие модули**
   - **Синтаксические ошибки**
   - **Логические ошибки**

2. **Изучите** [Потоки ошибок](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output) модуля Runbook для конкретных сообщений и сравните их с приведенными ниже ошибками.

3. **Убедитесь, что узлы и Рабочая область автоматизации содержат необходимые модули:** Если модуль Runbook импортирует все модули, убедитесь, что они доступны в учетной записи службы автоматизации, выполнив действия, описанные в разделе [Импорт модулей](../shared-resources/modules.md#import-modules). Обновите модули до последней версии, следуя инструкциям в разделе [обновление модулей Azure в службе автоматизации Azure](..//automation-update-azure-modules.md). Дополнительные сведения об устранении неполадок см. в разделе [Устранение неполадок модулей](shared-resources.md#modules).

Если работа модуля Runbook приостановлена или произошла непредвиденная ошибка:

* [Проверка состояния задания](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) определяет состояние Runbook и некоторые возможные причины.
* [Добавьте дополнительные выходные данные](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) в модуль Runbook, чтобы определить, что происходит перед приостановкой модуля Runbook.
* [Обрабатывает все исключения](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) , создаваемые заданием.

## <a name="login-azurerm"></a>Сценарий: запуск login-AzureRMAccount для входа

### <a name="issue"></a>Проблема

При выполнении модуля Runbook появляется следующая ошибка:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Причина:

Эта ошибка может возникнуть, если вы не используете учетную запись запуска от имени или срок ее действия истек. См. раздел [Управление учетными записями запуска от имени Azure Automation](https://docs.microsoft.com/azure/automation/manage-runas-account).

Эта ошибка возникает в двух основных причинах:

* Различные версии модулей AzureRM.
* Вы пытаетесь получить доступ к ресурсам в отдельной подписке.

### <a name="resolution"></a>Разрешение

Если эта ошибка возникает после обновления одного модуля AzureRM, следует обновить все модули AzureRM до одной версии.

Если вы пытаетесь получить доступ к ресурсам в другой подписке, можно выполнить следующие действия, чтобы настроить разрешения.

1. Перейдите к учетной записи запуска от имени учетной записи службы автоматизации и скопируйте идентификатор и отпечаток приложения.
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

### <a name="resolution"></a>Разрешение

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

### <a name="cause"></a>Причина:

Если в учетной записи Azure активирована многофакторная проверка подлинности, применять учетную запись пользователя Azure Active Directory для аутентификации в Azure нельзя. Вместо этого при проверке подлинности в Azure используйте сертификат или субъект-службу.

### <a name="resolution"></a>Разрешение

Сведения об использовании сертификата с командлетами классической модели развертывания Azure см. в руководстве по [созданию и добавлению сертификатов для управления службами Azure](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Сведения об использовании субъекта-службы с командлетами диспетчера ресурсов Azure см. в статьях о [создании субъекта-службы с помощью портала Azure](../../active-directory/develop/howto-create-service-principal-portal.md) и [аутентификации субъекта-службы в диспетчере ресурсов Azure](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

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

### <a name="cause"></a>Причина:

Эта ошибка возникла вследствие одновременного использования командлетов AzureRM и Az в модуле runbook. Она возникает при импортировании `Az` перед импортированием `AzureRM`.

### <a name="resolution"></a>Разрешение

Командлеты Az и AzureRM невозможно импортировать и использовать одновременно в одном и том же модуле runbook. Больше сведений о поддержке Az в службе автоматизации Azure см. в статье [Поддержка модуля Az в службе автоматизации Azure](../az-modules.md).

## <a name="task-was-cancelled"></a>Сценарий. Выполнение модуля Runbook завершается ошибкой отмены задачи

### <a name="issue"></a>Проблема

В модуле Runbook возникает ошибка, аналогичная приведенной ниже:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Причина:

Это ошибка может быть вызвана использованием устаревших модулей Azure.

### <a name="resolution"></a>Разрешение

Эту ошибку можно устранить, обновив модули Azure до последней версии.

В учетной записи службы автоматизации щелкните **Модули** и **Обновить модули Azure**. Обновление занимает примерно 15 минут. После его завершения перезапустите проблемный модуль. Дополнительные сведения об обновлении модулей см. в разделе [Обновление модулей Azure в службе автоматизации Azure](../automation-update-azure-modules.md).

## <a name="runbook-auth-failure"></a>Сценарий: Сбой модулей Runbook при работе с несколькими подписками

### <a name="issue"></a>Проблема

При выполнении модулей Runbook модуль Runbook не может управлять ресурсами Azure.

### <a name="cause"></a>Причина:

При запуске модуля runbook используется неправильный контекст.

### <a name="resolution"></a>Разрешение

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

Дополнительные сведения см. в разделе [Работа с несколькими подписками](../automation-runbook-execution.md#working-with-multiple-subscriptions).

## <a name="not-recognized-as-cmdlet"></a>Сценарий: термин не распознан как имя командлета, функции, скрипта

### <a name="issue"></a>Проблема

В модуле Runbook возникает ошибка, аналогичная приведенной ниже:

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Причина:

Эта ошибка может возникнуть по одной из следующих причин:

* Модуль, содержащий командлет, не импортирован в учетную запись службы автоматизации.
* Модуль, содержащий командлет, импортирован, но устарел.

### <a name="resolution"></a>Разрешение

Эту ошибку можно устранить, выполнив одну из следующих задач:

Если модуль является модулем Azure, см. раздел [Как обновить модули Azure PowerShell в службе автоматизации Azure](../automation-update-azure-modules.md), чтобы узнать, как обновить модули в учетной записи службы автоматизации.

Если это отдельный модуль, убедитесь, что он импортирован в учетную запись службы автоматизации.

## <a name="job-attempted-3-times"></a>Сценарий: было предпринято три попытки запуска задания Runbook, но все они были неудачными

### <a name="issue"></a>Проблема

Выполнение модуля Runbook завершилось ошибкой.

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Причина:

Эта ошибка возникает из-за одной из следующих проблем:

* Предельный объем памяти. Ограничения на объем памяти, выделяемый на Sandbox, описаны в разделе [Ограничения автоматизации](../../azure-subscription-service-limits.md#automation-limits). Задание может завершиться ошибкой, если оно использует более 400 МБ памяти.

* Сетевые сокеты. В песочницах Azure разрешено одновременно использовать не более 1000 сетевых сокетов, как описано в разделе об [ограничениях службы автоматизации](../../azure-subscription-service-limits.md#automation-limits).

* Модуль несовместим. Такая ситуация может возникнуть, если зависимости модуля заданы неправильно. В этом случае runbook обычно возвращает сообщение об ошибке "Команда не найдена" или "Не удается привязать параметр".

* Модуль Runbook попытался вызвать исполняемый файл или подпроцесс в модуле Runbook, который выполняется в песочнице Azure. В песочницах Azure этот сценарий не поддерживается.

* Модуль Runbook попытался записать слишком много данных об исключении в поток вывода.

### <a name="resolution"></a>Разрешение

Эту проблему можно устранить одним из следующих способов.

* Чтобы работа выполнялась в заданных пределах объема памяти, рекомендуется разделить рабочую нагрузку между несколькими модулями runbook, не обрабатывать слишком большой объем данных в памяти, не записывать ненужные выходные данные из модулей runbook и учитывать, сколько контрольных точек записывается в модули runbook рабочих процессов PowerShell. Чтобы очистить переменную и немедленно запустить сборку мусора, вы можете использовать методы `$myVar.clear()` и `[GC]::Collect()` соответственно. Эти действия уменьшают объем памяти, занимаемой модулем runbook во время выполнения.

* Обновите модули Azure с помощью действий, описанных в статье [How to update Azure PowerShell modules in Azure Automation](../automation-update-azure-modules.md) (Как обновить модули Azure PowerShell в службе автоматизации Azure).

* Другим решением является запуск модуля Runbook в [гибридной рабочей роли Runbook](../automation-hrw-run-runbooks.md). В гибридных рабочих ролях нет таких ограничений памяти и сетевых ресурсов, как в песочницах Azure.

* Если необходимо вызвать процесс (например, .exe или subprocess.call) в модуле runbook, нужно запустить runbook с помощью [гибридной рабочей роли runbook](../automation-hrw-run-runbooks.md).

* В потоке выходных данных задания имеется ограничение в 1 МБ. Убедитесь, что вызовы исполняемого или вложенного процесса заключаются в блок try/catch. Если они вызывают исключение, напишите сообщение из этого исключения в переменную автоматизации. Это предотвратит запись данных в поток вывода задания.

## <a name="sign-in-failed"></a>Сценарий: не удается войти в учетную запись Azure

### <a name="issue"></a>Проблема

При работе с командлетами `Add-AzureAccount` или `Connect-AzureRmAccount` появляется одна из следующих ошибок:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Причина:

Эта ошибка возникает, если указано недопустимое имя ресурса учетных данных. Ошибка также может возникнуть, если имя пользователя и пароль, которые вы ввели для настройки ресурса учетных данных службы автоматизации, указаны неправильно.

### <a name="resolution"></a>Разрешение

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

## <a name="child-runbook-object"></a>Ссылка на объект не задана экземпляром объекта

### <a name="issue"></a>Проблема

При вызове дочернего модуля Runbook с параметром `-Wait` появляется следующее сообщение об ошибке, а выходной поток содержит объект:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Причина:

Известна проблема, при которой командлет [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) некорректно обрабатывает поток выходных данных, если в нем содержатся объекты.

### <a name="resolution"></a>Разрешение

Чтобы устранить проблему, рекомендуется вместо этого реализовать логику опроса и использовать для получения выходных данных командлет [Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput). Эта логика показана в следующем примере.

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

### <a name="cause"></a>Причина:

Когда runbook является рабочим процессом PowerShell, он хранит сложные объекты в десериализированном формате, чтобы сохранить состояние модуля при приостановке рабочего процесса.

### <a name="resolution"></a>Разрешение

При использовании одного из трех следующих способов проблема будет устранена.

* Если сложные объекты передаются от одного командлета к другому через конвейер, включите эти командлеты в InlineScript.
* Передайте только требуемое имя или значение сложного объекта, а не весь объект.
* Используйте модуль Runbook PowerShell, а не модуль Runbook рабочего процесса PowerShell.

## <a name="quota-exceeded"></a>Сценарий: не удается выполнить задание модуля Runbook из-за превышения выделенной квоты

### <a name="issue"></a>Проблема

Выполнение задания Runbook завершилось ошибкой.

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Причина:

Эта ошибка возникает, когда время выполнения задания превышает квоту для учетной записи в 500 бесплатных минут. Эта квота применяется ко всем типам задач на выполнение заданий. К этим типам задач может относиться тестирование задания, запуск задания на портале Azure, выполнение задания с помощью веб-перехватчиков, а также планирование задания с помощью портала Azure или центра обработки данных. Дополнительные сведения см. на странице [цен на службу автоматизации](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Разрешение

Если на обработку задач вам требуется более 500 минут в месяц, выберите для подписки уровень "Базовый" вместо уровня "Бесплатный". Это можно сделать так.

1. Войдите в подписку Azure.
2. Выберите учетную запись службы автоматизации, которую нужно обновить.
3. Щелкните **Параметры** > **Цены**.
4. Нажмите кнопку **Включить** в нижней части страницы, чтобы обновить свою учетную запись до уровня **Базовый**.

## <a name="cmdlet-not-recognized"></a>Сценарий: не удается распознать командлет при выполнении модуля Runbook

### <a name="issue"></a>Проблема

Выполнение задания Runbook завершилось ошибкой.

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Причина:

Эта ошибка возникает, когда модулю PowerShell не удается найти командлет, который используется в runbook. Такая ошибка возникает, когда в учетной записи отсутствует модуль, содержащий командлет, когда возникает конфликт имен с именем модуля runbook, а также когда командлет существует и в другом модуле, а службе автоматизации Azure не удается разрешить имя.

### <a name="resolution"></a>Разрешение

Эту проблему можно устранить одним из следующих способов.

* Проверьте правильность ввода имени командлета.
* Убедитесь, что командлет существует в учетной записи службы автоматизации, а конфликты отсутствуют. Чтобы проверить наличие командлета, откройте модуль Runbook в режиме редактирования и найдите требуемый командлет в библиотеке или выполните команду `Get-Command <CommandName>`. Убедившись в наличии командлета в учетной записи и отсутствии конфликта имен с другими командлетами или модулями runbook, добавьте его на холст. При этом в runbook должен использоваться допустимый набор параметров.
* Если обнаружен конфликт имен, а сам командлет доступен в двух разных модулях, эту проблему можно решить использованием полного имени командлета. Например, можно использовать следующий формат: **имя_модуля\имя_командлета**.
* Если runbook выполняется локально в группе гибридных рабочих ролей, убедитесь, что на компьютере, на котором размещена гибридная рабочая роль, установлен соответствующий модуль или командлет.

## <a name="long-running-runbook"></a>Сценарий. Длительный модуль Runbook не выполняется

### <a name="issue"></a>Проблема

Runbook находится в **остановленном** состоянии в течение 3 часов после выполнения команды. Также может появиться сообщение об ошибке.

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

Это предусмотренная реакция на событие в песочницах Azure, которая связана с мониторингом "справедливого распределения" процессов в службе автоматизации Azure. Если модуль runbook выполняется дольше трех часов, служба справедливого распределения останавливает его. Состояние runbook, который превышает ограничение времени справедливого распределения, зависит от типа runbook. Модули runbook PowerShell и Python переходят в состояние **Остановлено**. Модули runbook рабочих процессов PowerShell переходят в состояние **Сбой**.

### <a name="cause"></a>Причина:

Runbook выполнялся дольше трехчасового ограничения, заданного справедливым распределением в песочнице Azure.

### <a name="resolution"></a>Разрешение

Одно из рекомендуемых решений — запустить runbook в [гибридной рабочей роли Runbook](../automation-hrw-run-runbooks.md).

Гибридные рабочие роли не ограничены тремя часами [справедливого распределения](../automation-runbook-execution.md#fair-share), как песочницы Azure. Модули runbook, выполняемые в гибридных рабочих ролях runbook, следует разрабатывать с поддержкой перезапуска при возникновении непредвиденных проблем локальной инфраструктуры.

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

### <a name="cause"></a>Причина:

Вызываемый веб-перехватчик отключен, или срок его действия истек.

### <a name="resolution"></a>Разрешение

если веб-перехватчик отключен, его можно повторно включить на портале Azure. Когда истек срок действия веб-перехватчика, его необходимо удалить или создать заново. Если срок действия еще не истек, можно только [обновить веб-перехватчик](../automation-webhooks.md#renew-webhook).

## <a name="429"></a>Сценарий: 429: частота запросов в настоящее время слишком велика. Повторите попытку позже

### <a name="issue"></a>Проблема

При выполнении командлета `Get-AzureRmAutomationJobOutput` вы получаете следующее сообщение об ошибке:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Причина:

эта ошибка может возникать при извлечении выходных данных задания из модуля Runbook, обладающего множеством [подробных потоков](../automation-runbook-output-and-messages.md#verbose-stream).

### <a name="resolution"></a>Разрешение

Эту проблему можно решить двумя способами:

* изменить модуль Runbook и сократить число создаваемых потоков заданий;
* уменьшите количество потоков, полученных при выполнении командлета. Для этого можно указать командлету `Get-AzureRmAutomationJobOutput` параметр `-Stream Output`, чтобы получить только потоки вывода. 

## <a name="cannot-invoke-method"></a>Сценарий: сбой задания PowerShell с ошибкой: не удается вызвать метод

### <a name="issue"></a>Проблема

При запуске задания PowerShell в модуле Runbook, запущенном в Azure, появляется следующее сообщение об ошибке:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Причина:

Эта ошибка может возникать при запуске задания PowerShell в модуле Runbook, запущенном в Azure. Это может произойти из-за того, что модули Runbook, запущенные в песочнице Azure, могут не работать в [режиме полного языка](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Разрешение

Эту проблему можно решить двумя способами:

* Вместо использования `Start-Job`используйте `Start-AzureRmAutomationRunbook` для запуска модуля Runbook
* Если в модуле Runbook есть это сообщение об ошибке, запустите его в гибридной рабочей роли Runbook.

Дополнительные сведения об этом поведении и других возможностях модулей Runbook службы автоматизации Azure см. в разделе [поведение модуля Runbook](../automation-runbook-execution.md#runbook-behavior).

## <a name="other"></a>Моя проблема не указана выше

В следующих разделах перечислены другие распространенные ошибки, а также сопутствующая документация, помогающая устранить проблему.

## <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>Гибридная рабочая роль runbook не выполняет задания или не отвечает

Если вы используете задания с помощью гибридной рабочей роли, а не в службе автоматизации Azure, вам может потребоваться [устранить неполадки в работе гибридной рабочей роли](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker).

## <a name="runbook-fails-with-no-permission-or-some-variation"></a>Модуль runbook прекратил работу с ошибкой "Нет разрешения" или аналогичной

Учетные записи запуска от имени могут не иметь таких же разрешений для ресурсов Azure, как ваша текущая учетная запись. Убедитесь, что у вашей учетной записи запуска от имени [есть разрешения на доступ к любым ресурсам](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), используемым в скрипте.

## <a name="runbooks-were-working-but-suddenly-stopped"></a>Модули runbook работали, но внезапно были остановлены

* Если модули Runbook ранее выполнялись, но остановлены, [Убедитесь, что срок действия учетной записи запуска от имени не истек](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Если для запуска модулей runbook вы используете веб-перехватчики, [убедитесь, что срок действия веб-перехватчика не истек](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook).

## <a name="issues-passing-parameters-into-webhooks"></a>Проблемы при передаче параметров в веб-перехватчики

Сведения о передаче параметров в веб-перехватчики см. в разделе [Запуск модуля Runbook из веб-перехватчика](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters).

## <a name="issues-using-az-modules"></a>Проблемы с использованием модулей AZ

Использование модулей Az и AzureRM в одной учетной записи службы автоматизации не поддерживается. Дополнительные сведения см. в разделе о [модулях az в модулях Runbook](https://docs.microsoft.com/azure/automation/az-modules) .

## <a name="inconsistent-behavior-in-runbooks"></a>Несогласованное поведение модулей runbook

Следуйте указаниям по [выполнению модулей runbook](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior), чтобы избежать проблем с одновременно выполняемыми заданиями, многократным созданием ресурсов и другой логикой, зависящей от времени, в модулях runbook.

## <a name="runbook-fails-with-the-errors-no-permission-forbidden-403-or-some-variation"></a>Сбой Runbook с ошибками: нет разрешения, запрещено, 403 или неопределенный вариант

Учетные записи запуска от имени могут не иметь таких же разрешений для ресурсов Azure, как ваша текущая учетная запись. Убедитесь, что учетная запись запуска от имени имеет [разрешения на доступ к любым ресурсам](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) , используемым в скрипте.

## <a name="runbooks-were-working-but-suddenly-stopped"></a>Модули runbook работали, но внезапно были остановлены

* Если модули Runbook ранее выполнялись, но остановлены, убедитесь, что срок действия учетной записи запуска от имени [не истек](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Если вы используете веб-перехватчики для запуска модулей Runbook, убедитесь, что срок действия веб-перехватчика [не истек](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook).

## <a name="passing-parameters-into-webhooks"></a>Передача параметров в веб-перехватчики

Сведения о передаче параметров в веб-перехватчики см. в разделе [Запуск модуля Runbook из веб-перехватчика](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters).

## <a name="using-az-modules"></a>Использование модулей Az

Использование модулей Az и AzureRM в одной учетной записи службы автоматизации не поддерживается. Дополнительные сведения см. в разделе о [модулях az в модулях Runbook](https://docs.microsoft.com/azure/automation/az-modules) .

## <a name="using-self-signed-certificates"></a>Использование самозаверяющих сертификатов

Чтобы использовать самозаверяющие сертификаты, необходимо следовать руководству по [созданию нового сертификата](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate).

## <a name="recommended-documents"></a>Рекомендуемые документы

* [Запуск модуля Runbook в службе автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Выполнение модуля Runbook в службе автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.
