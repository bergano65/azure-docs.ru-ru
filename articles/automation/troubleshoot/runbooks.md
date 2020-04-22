---
title: Ошибки, устранение ошибок в запуске системы автоматизации Azure
description: Узнайте, как устранить неполадки и устранить проблемы, с которыми можно столкнуться в runbooks Azure Automation.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 5ed25821f606b98bacf2acf3c2c389a8437406fa
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770904"
---
# <a name="troubleshoot-runbook-errors"></a>Ошибки выполнения ошибок

 В этой статье описаны различные ошибки runbook, которые могут возникнуть и как их устранить.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](../automation-update-azure-modules.md)

## <a name="diagnosing-runbook-issues"></a>Диагностика вопросов, связанных с книгой,

При получении ошибок во время выполнения runbook в Azure Automation можно использовать следующие действия для диагностики проблем.

1. **Убедитесь, что ваш скрипт runbook успешно выполняется на локальной машине.** 

    Обратитесь к [документам PowerShell](/powershell/scripting/overview) или [Python Docs](https://docs.python.org/3/) для языковых справочных и обучающих модулей. Выполнение сценария локально может обнаружить и устранить распространенные ошибки, такие как:

      * Отсутствующие модули
      * Синтаксические ошибки
      * Логические ошибки

2. **Исследуйте [потоки ошибок](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output)runbook.**

    Посмотрите на эти потоки для конкретных сообщений и сравните их с ошибками, задокументированными в этой статье.

3. **Убедитесь, что ваши узлы и рабочее пространство автоматизации имеют необходимые модули.** 

    Если в runbook импортируются какие-либо модули, убедитесь, что они доступны для вашей учетной записи Автоматизации, используя шаги, перечисленные в [модулях Импорта.](../shared-resources/modules.md#importing-modules) Обновите свои модули до последней версии, следуя инструкциям [в модулях Update Azure в Azure Automation.](..//automation-update-azure-modules.md) Для получения дополнительной [информации](shared-resources.md#modules)об устранении неполадок см.

4. **Делайте, если ваша книга приостановлена или неожиданно выходит из строя.**

    * [Проверка статусов вакансий](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) определяет статусы runbook и некоторые возможные причины.
    * [Добавьте дополнительный выход](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) в книгу, чтобы определить, что происходит до приостановки выполнения.
    * [Обработка любых исключений,](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) которые брошены вашей работы.

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Сценарий: Выполнить Login-AzureRMAccount для входа в систему

### <a name="issue"></a>Проблемы

Вы получаете следующую ошибку при выполнении runbook:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Причина:

Эта ошибка может возникнуть, когда вы не используете учетную запись Run As или срок действия учетной записи Run As. Смотрите [Управление Azure Автоматизация Run Как счета](https://docs.microsoft.com/azure/automation/manage-runas-account).

Эта ошибка имеет две основные причины:

* Существуют различные версии модуля AzureRM или Az.
* Вы пытаетесь получить доступ к ресурсам в отдельной подписке.

### <a name="resolution"></a>Решение

Если вы получили эту ошибку после обновления одного модуля AzureRM или Az, необходимо обновить все модули до одной и той же версии.

Если вы пытаетесь получить доступ к ресурсам в другой подписке, вы можете выполнить ниже приведенные ниже действия для настройки разрешений.

1. Перейдите на счет Automation Run As и скопируйте идентификатор приложения и отпечаток пальца.

    ![Копирование идентификатора и отпечатка пальца](../media/troubleshoot-runbooks/collect-app-id.png)

1. Перейдите в управление доступом подписки, где не размещается учетная запись Автоматизации, и добавьте новое назначение ролей.

    ![Управление доступом](../media/troubleshoot-runbooks/access-control.png)

1. Добавьте идентификатор приложения, собранный ранее. Выберите разрешения участников.

    ![Добавление назначения роли](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Копируйте название подписки.

1. Теперь вы можете использовать следующий код runbook для проверки разрешений с учетной записи Automation на другую подписку. Заменить `"\<CertificateThumbprint\>"` значение, скопированное в шаге 1. Заменить `"\<SubscriptionName\>"` значение, скопированное в шаге 4.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Сценарий: не удается найти подписку Azure

### <a name="issue"></a>Проблемы

Вы получаете следующую ошибку `Select-AzureRMSubscription`при `Select-AzSubscription` работе `Select-AzureSubscription`с, или cmdlet:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Ошибка

Это ошибка может возникать в указанных ниже случаях.

* Имя подписки неявляется.
* Пользователь Active Directory Azure, который пытается получить данные о подписке, не настроен как администратор подписки.
* Cmdlet недоступен.

### <a name="resolution"></a>Решение

Выполните ниже шаги, чтобы определить, прошли ли вы аутентифицированные действия в Azure и получили доступ к выбранной подписке.

1. Чтобы убедиться, что ваш скрипт работает автономно, протестируйте его за пределами Azure Automation.
2. Убедитесь, что скрипт запускает cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) перед запуском `Select-*` cmdlet.
3. К началу вашего модуля runbook добавьте `Disable-AzContextAutosave –Scope Process`. Этот вызов cmdlet гарантирует, что все учетные данные применяются только к выполнению текущей службы runbook.
4. Если вы все еще видите это сообщение `AzContext` об `Connect-AzAccount`ошибке, измените код, добавив параметр для, а затем выполните код.

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Сценарий: не удается выполнить проверку подлинности в Azure из-за включенной многофакторной проверки подлинности

### <a name="issue"></a>Проблемы

При аутентификации в Azure с помощью своего имени и пароля вы получаете следующую ошибку:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Причина:

Если в учетной записи Azure активирована многофакторная проверка подлинности, применять учетную запись пользователя Azure Active Directory для аутентификации в Azure нельзя. Вместо этого для проверки подлинности необходимо использовать сертификат или принцип службы.

### <a name="resolution"></a>Решение

Чтобы использовать сертификат с cmdlets классической модели развертывания Azure, обратитесь к [созданию и добавлению сертификата для управления службами Azure.](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Чтобы использовать основной сервис с cmdlets Ресурсов Azure, см. [Создание основной службы с помощью портала Azure](../../active-directory/develop/howto-create-service-principal-portal.md) и [проверка подлинности основного сервиса с помощью менеджера ресурсов Azure.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="scenario-you-see-an-error-in-your-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Сценарий: В потоках работы видно, что в потоках работы прослушаешься get_SerializationSettings

### <a name="issue"></a>Проблемы

В потоках вакансий в потоке вакансий можно увидеть следующую ошибку:

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AZAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>Причина:

Эта ошибка вызвана использованием как модуля AzureRM, так и az-модулей cmdlets в ступеньке. Это происходит при импорте модуля Az перед импортом модуля AzureRM.

### <a name="resolution"></a>Решение

Смдлеты Az и AzureRM не могут быть импортированы и использованы в одной и той же книге. Подробнее о Az cmdlets в Azure Automation можно узнать в [автоматизации Azure,](../shared-resources/modules.md)см.

## <a name="scenario-the-runbook-fails-with-the-error-a-task-was-canceled"></a><a name="task-was-cancelled"></a>Сценарий. Выполнение модуля Runbook завершается ошибкой отмены задачи

### <a name="issue"></a>Проблемы

В модуле Runbook возникает ошибка, аналогичная приведенной ниже:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Причина:

Это ошибка может быть вызвана использованием устаревших модулей Azure.

### <a name="resolution"></a>Решение

Эту ошибку можно устранить, обновив модули Azure до последней версии. 

1. В вашей учетной записи Автоматизация щелкните **модули,** а затем **обновите модули Azure.** 
2. Обновление занимает около 15 минут. Как только он будет завершен, перезапустите запущенную книгу, которая не удалась.

Чтобы узнать больше об обновлении модулей, смотрите [в обновлении модулей Azure в Azure Automation.](../automation-update-azure-modules.md)

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Сценарий: Runbooks терпят неудачу при работе с несколькими подписками

### <a name="issue"></a>Проблемы

При запуске руны в runbook работает не удается управлять ресурсами Azure.

### <a name="cause"></a>Причина:

При запуске модуля runbook используется неправильный контекст.

### <a name="resolution"></a>Решение

Контекст подписки может быть потерян, когда в runbook вывешнетне несколько runbooks. Чтобы обеспечить передачу контекста подписки в runbooks, пусть `Start-AzureRmAutomationRunbook` клиент runbook `AzureRmContext` передаст контекст cmdlet в параметре. Используйте `Disable-AzureRmContextAutosave` cmdlet `Scope` с набором параметров, чтобы `Process` гарантировать, что указанные учетные данные используются только для текущей книги. Для получения дополнительной [информации см.](../automation-runbook-execution.md#working-with-multiple-subscriptions)

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –wait
```

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-script"></a><a name="not-recognized-as-cmdlet"></a>Сценарий: Срок, не признанный как название cmdlet, функция, сценарий

### <a name="issue"></a>Проблемы

В модуле Runbook возникает ошибка, аналогичная приведенной ниже:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Причина:

Эта ошибка может произойти по следующим причинам:

* Модуль, содержащий cmdlet, не импортируется в учетную запись Автоматизации.
* Модуль, содержащий cmdlet импортируется, но устарел.

### <a name="resolution"></a>Решение

Выполняй одну из следующих задач для устранения этой ошибки. 

* Для модуля Azure смотрите, [как обновить модули Azure PowerShell в Azure Automation,](../automation-update-azure-modules.md) чтобы узнать, как обновить свои модули в учетной записи автоматизации.

* Для модуля, не относящемся к Azure, убедитесь, что модуль, импортируемый в вашу учетную запись Автоматизации.

## <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-failed-to-start-each-time"></a><a name="job-attempted-3-times"></a>Сценарий: Запуск работы runbook был предпринят три раза, но не запускался каждый раз

### <a name="issue"></a>Проблемы

Ваша книга не удается со следующей ошибкой:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Причина:

Эта ошибка возникает из-за одной из следующих проблем:

* Предельный объем памяти. Задание может выполнить неудачу, если она использует более 400 МБ памяти. Задокументированные ограничения памяти, отведенные в песочнице, находятся в [пределах службавтоматизации.](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) 

* Сетевые сокеты. Панзурные песочницы ограничены 1000 одновременными сетевыми розетками. См [Ограничения служб автоматизации](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* Модуль несовместим. Зависимость от модуля может быть неправильной. В этом случае ваша книга `Command not found` обычно `Cannot bind parameter` возвращает или сообщение.

* Нет аутентификации с Active Каталог для песочницы. В runbook была предпринята попытка вызвать исполняемый или подпроцесс, который работает в песочнице Azure. Настройка рун для аутентификации с помощью Azure AD с помощью Библиотеки подлинности активных каталогов Azure (ADAL) не поддерживается.

* Слишком много данных об исключениях. В runbook была предпринята попытка написать слишком много данных об исключениях в поток вывода.

### <a name="resolution"></a>Решение

* Предел памяти, сетевые розетки. Предлагаемые способы работы в пределах памяти — разделить рабочую нагрузку между несколькими runbooks, обработать меньше данных в памяти, избежать написания ненужных выходных данных из ваших runbooks и рассмотреть, сколько контрольных личек записано в ваших рукописных учебниках powerShell. Используйте четкий метод, `$myVar.clear`например, чтобы `[GC]::Collect` очистить переменные и использовать для запуска сбора мусора немедленно. Эти действия уменьшают объем памяти, занимаемой модулем runbook во время выполнения.

* Модуль несовместим. Обновите модули Azure, выяснив следующие шаги в [мочеиспускании модулей Azure PowerShell в Azure Automation.](../automation-update-azure-modules.md)

* Нет аутентификации с active каталог для песочницы. При проверке подлинности в Azure AD с помощью runbook убедитесь, что модуль Azure AD доступен в вашей учетной записи Автоматизации. Не забудьте предоставить учетной записи Run As необходимые разрешения для выполнения задач, которые автоматизирует runbook.

  Если в runbook не может вызвать исполняемый или подпроцесс, работающий в песочнице Azure, используйте книгу запуска на [гибридном Runbook Worker.](../automation-hrw-run-runbooks.md) Гибридные работники не ограничены ограничениями памяти и сети, которыми являются песочницы Azure.

* Слишком много данных об исключениях. Существует ограничение в 1 МБ в потоке вывода задания. Убедитесь, что в runbook контакты с выполнением `try` выполнения `catch` и блоков. Если операции бросают исключение, попросайте код сообщение из исключения в переменную Автоматизации. Этот метод предотвращает вписания сообщения в поток вывода задания.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Сценарий: Запись на учетную запись Azure не удалась

### <a name="issue"></a>Проблемы

Вы получаете одну из следующих `Connect-AzAccount` ошибок при работе с cmdlet:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Причина:

Эти ошибки возникают, если имя актива учетных данных не является действительным. Они также могут возникнуть, если имя пользователя и пароль, которые вы использовали для настройки актива учетных данных Automation, недействительны.

### <a name="resolution"></a>Решение

Чтобы определить источник проблемы, необходимо выполнить следующие действия.

1. Убедитесь, что у вас нет специальных символов. В имени ресурса учетных данных Службы автоматизации Azure, используемом для подключения к Azure, запрещается вводить такие специальные символы, как `\@`.
2. Проверьте, можно ли использовать имя пользователя и пароль, которые хранятся в учетных данных Azure Automation в локальном редакторе PowerShell ISE. Выполнить следующие cmdlets в PowerShell ISE.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

3. Если проверка подлинности не сработала локально, вы не настроили учетные данные Active Directory Azure. Обратитесь к [Authenticating to Azure с помощью блога Azure Active Directory,](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) чтобы настроить учетную запись Active Directory Azure.

4. Если ошибка кажется преходящей, попробуйте добавить логику повторной работы в рутину проверки подлинности, чтобы сделать проверку подлинности более надежной.

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
       $connectionResult = Connect-AzAccount `
                              -ServicePrincipal `
                              -Tenant $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Сценарий: Ссылка на объект не установлена на экземпляр объекта

### <a name="issue"></a>Проблемы

Вы получаете следующую ошибку при входе `Wait` в детскую запущенную книгу с параметром, и поток вывода содержит объект:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Причина:

`Start-AzAutomationRunbook`не обрабатывает поток вывода правильно, если поток содержит объекты.

### <a name="resolution"></a>Решение

Рекомендуется реализовать логику опроса и использовать [cmdlet Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) для получения вывода. Ниже приведен пример этой логики.

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$jobResults | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Сценарий: сбой модуля Runbook из-за десериализованного объекта

### <a name="issue"></a>Проблемы

Выполнение модуля Runbook завершилось ошибкой.

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Причина:

Когда runbook является рабочим процессом PowerShell, он хранит сложные объекты в десериализированном формате, чтобы сохранить состояние модуля при приостановке рабочего процесса.

### <a name="resolution"></a>Решение

Используйте любое из следующих решений, чтобы исправить эту проблему.

* Если вы трубопроводов сложных объектов от одного cmdlet к `InlineScript` другому, оберните эти cmdlets в деятельности.
* Передайте только требуемое имя или значение сложного объекта, а не весь объект.
* Используйте модуль Runbook PowerShell, а не модуль Runbook рабочего процесса PowerShell.

## <a name="scenario-runbook-job-fails-because-allocated-quota-exceeded"></a><a name="quota-exceeded"></a>Сценарий: Задание Runbook не удается из-за превышения выделенной квоты

### <a name="issue"></a>Проблемы

Выполнение задания Runbook завершилось ошибкой.

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Причина:

Эта ошибка возникает, когда время выполнения задания превышает квоту для учетной записи в 500 бесплатных минут. Эта квота применяется ко всем типам задач на выполнение заданий. Некоторые из этих задач — тестирование задания, запуск задания с портала, выполнение задания с помощью веб-крючков или планирование выполнения задания с помощью портала Azure или центра обработки данных. Чтобы узнать больше о ценах на автоматизацию, [см.](https://azure.microsoft.com/pricing/details/automation/)

### <a name="resolution"></a>Решение

Если вы хотите использовать более 500 минут обработки в месяц, измените подписку с бесплатного уровня на базовый.

1. Войдите в свою подписку Azure.
2. Выберите учетную запись Автоматизации для обновления.
3. Нажмите **Настройки,** затем **цены**.
4. Нажмите **Включить на** нижней части страницы, чтобы обновить учетную запись до базового уровня.

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Сценарий: не удается распознать командлет при выполнении модуля Runbook

### <a name="issue"></a>Проблемы

Выполнение задания Runbook завершилось ошибкой.

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Причина:

Эта ошибка возникает, когда модулю PowerShell не удается найти командлет, который используется в runbook. Возможно, что модуль, содержащий cmdlet отсутствует в учетной записи, есть конфликт имени с именем runbook, или cmdlet также существует в другом модуле и автоматизация не может решить имя.

### <a name="resolution"></a>Решение

Используйте любое из следующих решений для устранения проблемы.

* Убедитесь, что вы правильно ввели имя cmdlet.
* Убедитесь, что cmdlet существует в вашей учетной записи Automation и что нет никаких конфликтов. Чтобы проверить наличие cmdlet, откройте книгу в режиме отображаеми и ищите `Get-Command <CommandName>`cmdlet, который вы хотите найти в библиотеке, или запустите. После того как вы подтвердили, что cmdlet доступен для учетной записи, и что нет никаких конфликтов имени с другими cmdlets или runbooks, добавьте cmdlet к холсту и убедитесь, что вы используете действительный параметр, установленный в вашей книге.
* Если у вас есть конфликт имени и cmdlet доступен в двух различных модулях, решить проблему, используя полностью квалифицированное название для cmdlet. Например, можно использовать `ModuleName\CmdletName`.
* Если вы проводите запуск в гибридной рабочей группе, убедитесь, что модуль и cmdlet установлены на машине, в мещающейся в гибридной рабочей группе.

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Сценарий: Долгосрочный runbook не завершается

### <a name="issue"></a>Проблемы

Runbook находится в остановленном состоянии в течение 3 часов после выполнения команды. Вы также можете получить эту ошибку:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Такое поведение происходит по своей конструкции в песочницах Azure из-за справедливого мониторинга [процессов](../automation-runbook-execution.md#fair-share) в Azure Automation. Если процесс выполняется более трех часов, справедливая доля автоматически останавливает запуск книги. Статус runbook, который проходит мимо справедливого ограничения времени доли отличается по типу runbook. Модули runbook PowerShell и Python переходят в состояние Остановлено. Модули runbook рабочих процессов PowerShell переходят в состояние Сбой.

### <a name="cause"></a>Причина:

Запуск побежал за 3-часовой лимит, разрешенный справедливой долей в песочнице Azure.

### <a name="resolution"></a>Решение

Одно из рекомендуемых решений — запустить runbook в [гибридной рабочей роли Runbook](../automation-hrw-run-runbooks.md). Гибридные рабочие не ограничены 3-часовым лимитом на справедливую долю, который есть у песочниц Azure. Runbooks, запущенные на гибридных работниках Runbook, должны быть разработаны для поддержки поведения перезапуска при возникновении неожиданных проблем с локальной инфраструктурой.

Другим решением является оптимизация runbook путем создания [детских runbooks.](../automation-child-runbooks.md) Если в книге runbook работает одинаковая функция на нескольких ресурсах, например, в операции базы данных на нескольких базах данных, можно переместить функцию в детскую книгу. Каждая детская книга выполняется параллельно в отдельном процессе. Это уменьшает количество времени на завершение родительского модуля runbook.

Командлеты PowerShell для реализации дочерних runbook:

* [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0). Этот командлет позволяет запустить runbook и передать в него параметры.

* [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0). Если есть операции, которые должны быть выполнены после завершения детской книги, это cmdlet позволяет проверить состояние работы для каждого ребенка.

## <a name="scenario-status-400-bad-request-when-calling-a-webhook"></a><a name="expired webhook"></a>Сценарий: Статус: 400 Bad Request при вызове веб-крючка

### <a name="issue"></a>Проблемы

При попытке вызвать веб-крюк для запуска Azure Automation получает следующую ошибку:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Причина:

Вызываемый веб-перехватчик отключен, или срок его действия истек.

### <a name="resolution"></a>Решение

если веб-перехватчик отключен, его можно повторно включить на портале Azure. Если у веб-крючка истек срок действия, необходимо удалить, а затем воссоздать его. Если срок действия еще не истек, можно только [обновить веб-перехватчик](../automation-webhooks.md#renew-webhook).

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Сценарий: 429: Коэффициент запроса в настоящее время слишком велик...

### <a name="issue"></a>Проблемы

При выполнении командлета `Get-AzAutomationJobOutput` вы получаете следующее сообщение об ошибке:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Причина:

Эта ошибка может возникнуть при извлечении вывода задания из runbook, который имеет много [потоков Verbose.](../automation-runbook-output-and-messages.md#verbose-stream)

### <a name="resolution"></a>Решение

Сделайте одно из следующих, чтобы устранить эту ошибку.

* изменить модуль Runbook и сократить число создаваемых потоков заданий;

* уменьшите количество потоков, полученных при выполнении командлета. Для этого можно установить значение `Stream` параметра для cmdlet [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) для получения только выходных потоков. 

## <a name="scenario-powershell-job-fails-with-error-cannot-invoke-method"></a><a name="cannot-invoke-method"></a>Сценарий: Работа PowerShell не справляется с ошибкой: не может вызвать метод

### <a name="issue"></a>Проблемы

Вы получаете следующее сообщение об ошибке при запуске задания PowerShell в запущенной в Azure книге:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Причина:

Эта ошибка может указывать на то, что запущенные книги, исполняемые в песочнице Azure, не могут работать в [режиме полного языка.](/powershell/module/microsoft.powershell.core/about/about_language_modes)

### <a name="resolution"></a>Решение

Существует два способа устранения этой ошибки.

* Вместо того, чтобы использовать [Start-Job,](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/start-job?view=powershell-7)используйте [Start-AzAutomationRunbook,](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) чтобы запустить книгу.
* Попробуйте запустить книгу на гибридный Runbook работника.

Чтобы узнать больше об этом поведении и других [Runbook behavior](../automation-runbook-execution.md#runbook-behavior)поведении runbooks Azure Automation, см.

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>Сценарий: Linux Hybrid Runbook Worker получает подсказку для пароля при подписании runbook

### <a name="issue"></a>Проблемы

Запуск `sudo` команды для Linux Hybrid Runbook Worker получает неожиданный запрос для пароля.

### <a name="cause"></a>Причина:

Учетная запись **nxautomationuser** для агента Log Analytics для Linux неправильно настроена в файле **sudoers.** Гибридный Runbook Worker нуждается в соответствующей конфигурации разрешений на учетную запись и других данных, чтобы он мог подписать runbooks на Linux Runbook Worker.

### <a name="resolution"></a>Решение

* Убедитесь, что Hybrid Runbook Worker имеет GnuPG (GPG) исполняемый на машине.

* Проверьте конфигурацию учетной записи **nxautomationuser** в файле **sudoers.** Смотрите [Запуск runbooks на гибридных Runbook работника](../automation-hrw-run-runbooks.md)

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>Сценарий: Cmdlet не удается в PnP PowerShell runbook на Azure автоматизации

### <a name="issue"></a>Проблемы

Когда запуск записывает созданный PnP PowerShell объект на выход Azure Automation напрямую, выход cmdlet не может передаваться обратно в Автоматизацию.

### <a name="cause"></a>Причина:

Эта проблема чаще всего возникает, когда Azure Automation обрабатывает рунированные книги, `add-pnplistitem`которые вызывают PnP PowerShell cmdlets, например, без ловли объектов возврата.

### <a name="resolution"></a>Решение

Отспособьте скрипты, чтобы присвоить переменным любые значения возврата, чтобы cmdlets не пытались написать целые объекты в стандартный вывод. Скрипт может перенаправить выходной поток на cmdlet, как показано ниже.

```azurecli
  $null = add-pnplistitem
```

Если скрипт разбирает выход cmdlet, скрипт должен хранить выход в переменной и манипулировать переменной, а не просто потоковой передачей.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-invalid-status-code-forbidden-when-using-key-vault-inside-a-runbook"></a>Сценарий: Недействительный код статуса "Запрещено" при использовании Key Vault в справочной книге

### <a name="issue"></a>Проблемы

При попытке получить доступ к Key Vault через runbook Автоматизации Azure вы получаете следующую ошибку:

```error
Operation returned an invalid status code 'Forbidden' 
```

### <a name="cause"></a>Причина:

Возможные причины этой проблемы:

* Не использовать учетную запись Run As.
* Недостаточно разрешений.

### <a name="resolution"></a>Решение

#### <a name="not-using-run-as-account"></a>Не использование учетной записи Run As

Выполните последующие шаги в [шаге 5 - Добавьте аутентификацию для управления ресурсами Azure,](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell#add-authentication-to-manage-azure-resources) чтобы убедиться, что вы используете учетную запись Run As для доступа к Key Vault. 

#### <a name="insufficient-permissions"></a>Недостаточные разрешения

Выполните последующие [действия в Добавлении разрешений в Key Vault,](https://docs.microsoft.com/azure/automation/manage-runas-account#add-permissions-to-key-vault) чтобы убедиться, что ваша учетная запись Run As имеет достаточное количество разрешений для доступа к Key Vault. 

## <a name="my-problem-isnt-listed-above"></a><a name="other"></a>Моя проблема не указана в этой статье

В приведенных ниже разделах перечисляют другие распространенные ошибки и предоставляются вспомогательная документация, которая поможет вам решить проблему.

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>Гибридная рабочая роль runbook не выполняет задания или не отвечает

Если вы работаете на гибридном Runbook Worker, а не в Azure Automation, возможно, потребуется [устранить неполадки самого гибридного работника.](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker)

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>Runbook не удается без разрешения или некоторые изменения

Выполнить Как-то может не иметь тех же разрешений для ресурсов Azure, что и текущая учетная запись. Убедитесь, что ваша учетная запись Run As имеет [разрешения на доступ к любым ресурсам,](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) используемым в скрипте.

### <a name="issues-passing-parameters-into-webhooks"></a>Вопросы передачи параметров в webhooks

Для справки с прохождением параметров в веб-крючки, [см.](../automation-webhooks.md#parameters-used-when-the-webhook-starts-a-runbook)

### <a name="issues-using-az-modules"></a>Проблемы с использованием модулей Az

Использование модулей Az и модулей AzureRM в одной учетной записи Автоматизации не поддерживается. Для получения более подробной информации можно узнать [о модулях Az в runbooks.](https://docs.microsoft.com/azure/automation/az-modules)

### <a name="inconsistent-behavior-in-runbooks"></a>Несогласованное поведение модулей runbook

Следуйте инструкциям в [исполнении Runbook,](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) чтобы избежать проблем с одновременными заданиями, ресурсами, создаваемыми несколько раз, или другой логикой, чувствительной к времени, в runbooks.

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Runbook не справляется с ошибкой Нет разрешения, Запрещено (403), или некоторые изменения

Выполнить Как-то может не иметь тех же разрешений для ресурсов Azure, что и текущая учетная запись. Убедитесь, что ваша учетная запись Run As имеет [разрешения на доступ к любым ресурсам,](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) используемым в скрипте.

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Модули runbook работали, но внезапно были остановлены

* Убедитесь, что срок действия учетной записи Run As не истек. Смотрите [продление сертификации](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Если вы используете [веб-крюк](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook) для запуска runbook, убедитесь, что веб-крюк не истек.

### <a name="passing-parameters-into-webhooks"></a>Передача параметров в веб-перехватчики

Для справки с прохождением параметров в веб-крючки, [см.](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters-used-when-the-webhook-starts-a-runbook)

### <a name="using-az-modules"></a>Использование модулей Az

Использование модулей Az и модулей AzureRM в одной учетной записи Автоматизации не поддерживается. Смотрите [модули Az в runbooks](https://docs.microsoft.com/azure/automation/az-modules).

### <a name="using-self-signed-certificates"></a>Использование самоподписанных сертификатов

Для использования самоподписанных сертификатов [см.](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate)

### <a name="access-denied-when-using-azure-sandbox-for-a-runbook"></a>Доступ отказано при использовании песочницы Azure для runbook

Песочница Azure препятствует доступу ко всем неумуренным серверам COM. Например, приложение с песочницей или runbook не могут зайти в службу управления Windows (WMI) или в службу установки Windows (msiserver.exe). Подробную информацию об использовании песочницы можно узнать [в журнале Azure Automation.](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="recommended-documents"></a>Рекомендуемые документы

* [Запуск модуля Runbook в службе автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Выполнение модуля Runbook в службе автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [форумы Azure.](https://azure.microsoft.com/support/forums/)
* Связь [@AzureSupport](https://twitter.com/azuresupport) с официальной учетной записью Microsoft Azure для улучшения обслуживания клиентов путем подключения сообщества Azure к нужным ресурсам: ответам, поддержке и экспертам.
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **«Получите поддержку».**
