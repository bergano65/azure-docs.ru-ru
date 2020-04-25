---
title: Устранение ошибок Runbook службы автоматизации Azure
description: Узнайте, как устранять неполадки и устранять проблемы, которые могут возникнуть при работе с модулями Runbook службы автоматизации Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: a407461e20eefe29dd410ac6ed547b33287a5be8
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82145406"
---
# <a name="troubleshoot-runbook-errors"></a>Устранение ошибок модуля Runbook

 В этой статье описываются различные ошибки Runbook, которые могут возникнуть, и способы их устранения.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке AZ Module в гибридной рабочей роли Runbook см. в статье [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Для учетной записи службы автоматизации можно обновить модули до последней версии, используя [обновление модулей Azure PowerShell в службе автоматизации Azure](../automation-update-azure-modules.md).

## <a name="diagnosing-runbook-issues"></a>Диагностика проблем Runbook

При возникновении ошибок во время выполнения Runbook в службе автоматизации Azure можно выполнить следующие действия, чтобы помочь в диагностике проблем.

1. **Убедитесь, что скрипт Runbook успешно выполнен на локальном компьютере.** 

    Справочные материалы по языку и учебным модулям см. в документации по [документам PowerShell](/powershell/scripting/overview) или документах [Python](https://docs.python.org/3/) . Локальное выполнение скрипта может обнаружить и устранить распространенные ошибки, например:

      * Отсутствующие модули
      * Синтаксические ошибки
      * Логические ошибки

2. **Исследуйте [Потоки ошибок](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output)Runbook.**

    Просмотрите эти потоки для конкретных сообщений и сравните их с ошибками, описанными в этой статье.

3. **Убедитесь, что узлы и Рабочая область службы автоматизации имеют необходимые модули.** 

    Если модуль Runbook импортирует все модули, убедитесь, что они доступны для учетной записи службы автоматизации, выполнив действия, описанные в разделе [Импорт модулей](../shared-resources/modules.md#importing-modules). Обновите модули до последней версии, следуя инструкциям в [статье обновление модулей Azure в службе автоматизации Azure](..//automation-update-azure-modules.md). Дополнительные сведения об устранении неполадок см. в разделе [Устранение неполадок модулей](shared-resources.md#modules).

4. **Если модуль Runbook приостанавливается или неожиданно завершается сбоем.**

    * [Проверка состояния задания](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) определяет состояние Runbook и некоторые возможные причины.
    * [Добавьте дополнительные выходные данные](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) в модуль Runbook, чтобы определить, что происходит перед приостановкой модуля Runbook.
    * [Обрабатывает все исключения](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) , создаваемые заданием.

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Сценарий: для входа выполните команду Login-AzureRMAccount.

### <a name="issue"></a>Проблема

При выполнении модуля Runbook появляется следующая ошибка:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Причина:

Эта ошибка может возникать, если не используется учетная запись запуска от имени или истек срок действия учетной записи запуска от имени. См. раздел [Управление учетными записями запуска от имени службы автоматизации Azure](https://docs.microsoft.com/azure/automation/manage-runas-account).

Эта ошибка возникает в двух основных причинах:

* Существуют разные версии модуля AzureRM или AZ.
* Вы пытаетесь получить доступ к ресурсам в отдельной подписке.

### <a name="resolution"></a>Разрешение

Если эта ошибка возникает после обновления одного модуля AzureRM или AZ, следует обновить все модули до одной версии.

Если вы пытаетесь получить доступ к ресурсам в другой подписке, можно выполнить следующие действия, чтобы настроить разрешения.

1. Перейдите к учетной записи запуска от имени службы автоматизации и скопируйте идентификатор и отпечаток приложения.

    ![Копировать идентификатор и отпечаток](../media/troubleshoot-runbooks/collect-app-id.png)

1. Перейдите к контролю доступа подписки, в котором не размещена учетная запись службы автоматизации, и добавьте новое назначение ролей.

    ![Управление доступом](../media/troubleshoot-runbooks/access-control.png)

1. Добавьте собранный ранее идентификатор приложения. Выберите разрешения участника.

    ![Добавление назначения роли](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Скопируйте имя подписки.

1. Теперь можно использовать следующий код Runbook для проверки разрешений учетной записи службы автоматизации в другой подписке. Замените `"\<CertificateThumbprint\>"` на значение, скопированное на шаге 1. Замените `"\<SubscriptionName\>"` на значение, скопированное на шаге 4.

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

### <a name="issue"></a>Проблема

При работе с командлетом `Select-AzureSubscription`, `Select-AzureRMSubscription`или `Select-AzSubscription` вы получаете следующую ошибку:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Ошибка

Это ошибка может возникать в указанных ниже случаях.

* Недопустимое имя подписки.
* Пользователь Azure Active Directory, который пытается получить сведения о подписке, не настроен в качестве администратора подписки.
* Командлет недоступен.

### <a name="resolution"></a>Разрешение

Выполните следующие действия, чтобы определить, прошли ли вы проверку подлинности в Azure и у вас есть доступ к подписке, которую вы пытаетесь выбрать.

1. Чтобы убедиться, что сценарий работает автономно, протестируйте его за пределами службы автоматизации Azure.
2. Убедитесь, что скрипт запускает командлет [Connect-азаккаунт](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) перед запуском `Select-*` командлета.
3. К началу вашего модуля runbook добавьте `Disable-AzContextAutosave –Scope Process`. Этот вызов командлета гарантирует, что любые учетные данные применяются только к выполнению текущего модуля Runbook.
4. Если вы по-прежнему видите это сообщение об ошибке, измените код `AzContext` , добавив параметр для `Connect-AzAccount`, а затем выполните код.

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Сценарий: не удается выполнить проверку подлинности в Azure из-за включенной многофакторной проверки подлинности

### <a name="issue"></a>Проблема

При аутентификации в Azure с помощью своего имени и пароля вы получаете следующую ошибку:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Причина:

Если в учетной записи Azure активирована многофакторная проверка подлинности, применять учетную запись пользователя Azure Active Directory для аутентификации в Azure нельзя. Вместо этого для проверки подлинности необходимо использовать сертификат или субъект-службу.

### <a name="resolution"></a>Разрешение

Чтобы использовать сертификат с командлетами классической модели развертывания Azure, см. статью [Создание и Добавление сертификата для управления службами Azure](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx). Сведения об использовании субъекта-службы с командлетами Azure Resource Manager см. в разделе [Создание участника-службы с помощью портал Azure](../../active-directory/develop/howto-create-service-principal-portal.md) и [Проверка подлинности субъекта-службы с помощью Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="scenario-you-see-an-error-in-your-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Сценарий: вы видите ошибку в потоках заданий о методе get_SerializationSettings

### <a name="issue"></a>Проблема

В потоках заданий для модуля Runbook появится следующая ошибка:

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

Возможно, эта ошибка вызвана неполной миграцией из AzureRM в команды AZ modules в модуле Runbook. Это может привести к тому, что служба автоматизации Azure запустит задание Runbook, используя только модули AzureRM, а затем запустить другое задание, используя только AZ Modules, что приведет к сбою "песочницы". 

### <a name="resolution"></a>Разрешение

Мы не рекомендуем использовать командлеты AZ и AzureRM в одном модуле Runbook. Дополнительные сведения о правильном использовании этих модулей см. в разделе [Переход на AZ modules](../shared-resources/modules.md#migrating-to-az-modules).

## <a name="scenario-the-runbook-fails-with-the-error-a-task-was-canceled"></a><a name="task-was-cancelled"></a>Сценарий. Выполнение модуля Runbook завершается ошибкой отмены задачи

### <a name="issue"></a>Проблема

В модуле Runbook возникает ошибка, аналогичная приведенной ниже:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Причина:

Это ошибка может быть вызвана использованием устаревших модулей Azure.

### <a name="resolution"></a>Разрешение

Эту ошибку можно устранить, обновив модули Azure до последней версии. 

1. В учетной записи службы автоматизации щелкните **модули**, а затем **Обновите модули Azure**. 
2. Обновление занимает примерно 15 минут. После завершения повторно запустите модуль Runbook, который завершился сбоем.

Дополнительные сведения об обновлении модулей см. [в статье обновление модулей Azure в службе автоматизации Azure](../automation-update-azure-modules.md).

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Сценарий: Сбой модулей Runbook при работе с несколькими подписками

### <a name="issue"></a>Проблема

При выполнении модулей Runbook модуль Runbook не может управлять ресурсами Azure.

### <a name="cause"></a>Причина:

При запуске модуля runbook используется неправильный контекст.

### <a name="resolution"></a>Разрешение

Контекст подписки может быть потерян, если модуль Runbook вызывает несколько модулей Runbook. Чтобы убедиться в том, что контекст подписки передается в модули Runbook, клиент Runbook должен передать контекст в `Start-AzureRmAutomationRunbook` командлет в `AzureRmContext` параметре. Используйте `Disable-AzureRmContextAutosave` командлет с `Scope` параметром, имеющим значение `Process` , чтобы убедиться, что указанные учетные данные используются только для текущего модуля Runbook. Дополнительные сведения см. в разделе [Работа с несколькими подписками](../automation-runbook-execution.md#working-with-multiple-subscriptions).

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

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-script"></a><a name="not-recognized-as-cmdlet"></a>Сценарий: термин не распознан как имя командлета, функции, скрипта

### <a name="issue"></a>Проблема

В модуле Runbook возникает ошибка, аналогичная приведенной ниже:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Причина:

Эта ошибка может возникать по следующим причинам.

* Модуль, содержащий командлет, не импортируется в учетную запись службы автоматизации.
* Модуль, содержащий командлет, импортирован, но устарел.

### <a name="resolution"></a>Разрешение

Выполните одну из следующих задач, чтобы устранить эту ошибку. 

* Сведения о модулях Azure см. в разделе [обновление модулей Azure PowerShell в службе автоматизации Azure](../automation-update-azure-modules.md) , чтобы узнать, как обновить модули в учетной записи службы автоматизации.

* Для модуля, не относящегося к Azure, убедитесь, что модуль импортируется в учетную запись службы автоматизации.

## <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-failed-to-start-each-time"></a><a name="job-attempted-3-times"></a>Сценарий: предпринята попытка запуска задания Runbook три раза, но не удалось запустить каждый раз

### <a name="issue"></a>Проблема

Модуль Runbook завершается со следующей ошибкой:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Причина:

Эта ошибка возникает по одной из следующих причин:

* Предельный объем памяти. Задание может завершиться ошибкой, если в нем используется более 400 МБ памяти. Документированные ограничения на память, выделенную для песочницы, находятся в статье [ограничения службы автоматизации](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits). 

* Сетевые сокеты. В песочницах Azure ограничено 1000 одновременных сетевых сокетов. См. раздел [ограничения службы автоматизации](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* Модуль несовместим. Зависимости модуля могут быть неправильными. В этом случае модуль Runbook обычно возвращает сообщение `Command not found` или. `Cannot bind parameter`

* Без проверки подлинности с Active Directory для "песочницы". Модуль Runbook попытался вызвать исполняемый файл или подпроцесс, который выполняется в песочнице Azure. Настройка модулей Runbook для проверки подлинности в Azure AD с помощью библиотеки проверки подлинности Azure Active Directory (ADAL) не поддерживается.

* Слишком много данных об исключениях. Модуль Runbook попытался записать слишком много данных об исключении в поток вывода.

### <a name="resolution"></a>Разрешение

* Ограничение памяти, сетевые сокеты. Предлагаемые способы работы в рамках ограничения памяти: разделить рабочую нагрузку между несколькими модулями Runbook, обрабатывать меньшие объемы данных в памяти, избежать необходимости записывать ненужные выходные данные из модулей Runbook и определить, сколько контрольных точек записывается в модули Runbook рабочего процесса PowerShell. Используйте метод Clear, например `$myVar.clear`, для удаления переменных и использования `[GC]::Collect` для немедленного выполнения сборки мусора. Эти действия уменьшают объем памяти, занимаемой модулем runbook во время выполнения.

* Модуль несовместим. Обновите модули Azure, выполнив действия, описанные в статье [обновление модулей Azure PowerShell в службе автоматизации Azure](../automation-update-azure-modules.md).

* Без проверки подлинности с Active Directory для "песочницы". При проверке подлинности в Azure AD с помощью модуля Runbook убедитесь, что модуль Azure AD доступен в вашей учетной записи службы автоматизации. Не забудьте предоставить учетной записи запуска от имени необходимые разрешения для выполнения задач, которые автоматизируется модулем Runbook.

  Если модуль Runbook не может вызвать исполняемый файл или подпроцесс, выполняющийся в песочнице Azure, используйте Runbook в [гибридной рабочей роли Runbook](../automation-hrw-run-runbooks.md). Гибридные рабочие роли не ограничиваются ограничениями памяти и сети, которые имеют службы "песочницы Azure".

* Слишком много данных об исключениях. В потоке выходных данных задания имеется ограничение в 1 МБ. Убедитесь, что модуль Runbook закрывает вызовы исполняемого или вложенного процесса `try` с `catch` помощью блоков и. Если операции создают исключение, то код будет записывать сообщение из исключения в переменную автоматизации. Эта методика предотвращает запись сообщения в поток вывода задания.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Сценарий: сбой входа в учетную запись Azure

### <a name="issue"></a>Проблема

При работе с `Connect-AzAccount` командлетом появляется одна из следующих ошибок:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Причина:

Эти ошибки возникают, если имя ресурса учетных данных недопустимо. Они также могут возникать, если имя пользователя и пароль, используемые для настройки ресурса учетных данных службы автоматизации, недействительны.

### <a name="resolution"></a>Разрешение

Чтобы определить источник проблемы, необходимо выполнить следующие действия.

1. Убедитесь, что у вас нет специальных символов. В имени ресурса учетных данных Службы автоматизации Azure, используемом для подключения к Azure, запрещается вводить такие специальные символы, как `\@`.
2. Проверьте, можно ли использовать имя пользователя и пароль, которые хранятся в учетных данных службы автоматизации Azure в локальном редакторе интегрированной среды сценариев PowerShell. Выполните следующие командлеты в интегрированной среде сценариев PowerShell.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

3. Если проверка подлинности не выполняется локально, учетные данные Azure Active Directory не настроены должным образом. Сведения о том, как правильно настроить учетную запись Azure Active Directory, см. в записи блога [Проверка подлинности в Azure с помощью Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) .

4. Если ошибка кажется временной, попробуйте добавить логику повторных попыток в подсистему проверки подлинности, чтобы сделать проверку подлинности более надежной.

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

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>Сценарий: Неверная ссылка на объект при вызове Add-Азаккаунт

### <a name="issue"></a>Проблема

Эта ошибка возникает при работе с `Add-AzAccount`, который является псевдонимом для `Connect-AzAccount` командлета:

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>Причина:

Эта ошибка может возникать, если модуль Runbook не выполняет нужные действия `Add-AzAccount` перед вызовом для добавления учетной записи службы автоматизации. Примером одного из необходимых действий является вход с использованием учетной записи запуска от имени. Сведения о правильных операциях, используемых в модуле Runbook, см. [в разделе Выполнение Runbook в службе автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-runbook-execution).

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Сценарий: ссылка на объект не задает экземпляр объекта

### <a name="issue"></a>Проблема

При вызове дочернего модуля Runbook с `Wait` параметром и выходным потоком, содержащим объект, возникает следующая ошибка:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Причина:

`Start-AzAutomationRunbook`не обрабатывает выходной поток правильно, если поток содержит объекты.

### <a name="resolution"></a>Разрешение

Рекомендуется реализовать логику опроса и получить выходные данные с помощью командлета [Get-азаутоматионжобаутпут](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) . Пример этой логики определен ниже.

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

### <a name="issue"></a>Проблема

Выполнение модуля Runbook завершилось ошибкой.

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Причина:

Когда runbook является рабочим процессом PowerShell, он хранит сложные объекты в десериализированном формате, чтобы сохранить состояние модуля при приостановке рабочего процесса.

### <a name="resolution"></a>Разрешение

Чтобы устранить эту проблему, используйте любое из следующих решений.

* Если вы направляете сложные объекты из одного командлета в другой, заключите эти `InlineScript` командлеты в действие.
* Передайте только требуемое имя или значение сложного объекта, а не весь объект.
* Используйте модуль Runbook PowerShell, а не модуль Runbook рабочего процесса PowerShell.

## <a name="scenario-runbook-job-fails-because-allocated-quota-exceeded"></a><a name="quota-exceeded"></a>Сценарий: сбой задания Runbook из-за превышения выделенной квоты

### <a name="issue"></a>Проблема

Выполнение задания Runbook завершилось ошибкой.

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Причина:

Эта ошибка возникает, когда время выполнения задания превышает квоту для учетной записи в 500 бесплатных минут. Эта квота применяется ко всем типам задач на выполнение заданий. Некоторые из этих задач — тестирование задания, запуск задания с портала, выполнение задания с помощью веб-перехватчиков или планирование выполнения задания с помощью портал Azure или вашего центра обработки данных. Дополнительные сведения о ценах на автоматизацию см. в разделе [цены на автоматизацию](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Разрешение

Если вы хотите использовать более 500 минут обработки в месяц, измените подписку с уровня "бесплатный" на уровень "базовый".

1. Войдите в свою подписку Azure.
2. Выберите учетную запись службы автоматизации для обновления.
3. Щелкните **Параметры**, а затем — **цены**.
4. Щелкните **включить** на странице снизу, чтобы обновить учетную запись до уровня "базовый".

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Сценарий: не удается распознать командлет при выполнении модуля Runbook

### <a name="issue"></a>Проблема

Выполнение задания Runbook завершилось ошибкой.

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Причина:

Эта ошибка возникает, когда модулю PowerShell не удается найти командлет, который используется в runbook. Возможно, модуль, содержащий командлет, отсутствует в учетной записи, существует конфликт имен с именем Runbook, или же командлет существует в другом модуле, и автоматизация не может разрешить это имя.

### <a name="resolution"></a>Разрешение

Чтобы устранить проблему, используйте любое из следующих решений.

* Убедитесь, что имя командлета введено правильно.
* Убедитесь, что командлет существует в учетной записи службы автоматизации и что конфликтов нет. Чтобы проверить наличие командлета, откройте модуль Runbook в режиме редактирования и выполните поиск командлета, который нужно найти в библиотеке, или выполните команду `Get-Command <CommandName>`. После проверки того, что командлет доступен для учетной записи и не существует конфликтов имен с другими командлетами или модулями Runbook, добавьте командлет на холст и убедитесь, что вы используете допустимый набор параметров в модуле Runbook.
* Если у вас есть конфликт имен и командлет доступен в двух разных модулях, устраните проблему, используя полное имя командлета. Например, можно использовать `ModuleName\CmdletName`.
* Если вы выполняете локальный модуль Runbook в группе гибридной рабочей роли, убедитесь, что модуль и командлет установлены на компьютере, на котором размещена Гибридная Рабочая роль.

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Сценарий: сбой длительного выполнения Runbook

### <a name="issue"></a>Проблема

Runbook находится в остановленном состоянии в течение 3 часов после выполнения команды. Кроме того, может появиться следующее сообщение об ошибке:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Такое поведение характерно в изолированных средах Azure из-за [общего](../automation-runbook-execution.md#fair-share) мониторинга процессов в службе автоматизации Azure. Если процесс выполняется дольше трех часов, справедливое предоставление общего доступа автоматически останавливает модуль Runbook. Состояние модуля Runbook, который находится за пределами длительного общего времени, отличается от типа Runbook. Модули runbook PowerShell и Python переходят в состояние Остановлено. Модули runbook рабочих процессов PowerShell переходят в состояние Сбой.

### <a name="cause"></a>Причина:

Модуль Runbook выполнялся в течение 3-часового лимита, разрешенного долево в песочнице Azure.

### <a name="resolution"></a>Разрешение

Одно из рекомендуемых решений — запустить runbook в [гибридной рабочей роли Runbook](../automation-hrw-run-runbooks.md). Гибридные рабочие роли не ограничиваются предельным числом модулей Runbook, допустимым в течение 3 часов. Модули Runbook, выполняемые в гибридных рабочих ролях Runbook, должны быть разработаны для поддержки поведения при перезапуске в случае непредвиденных проблем с локальной инфраструктурой.

Другое решение — оптимизация модуля Runbook путем создания [дочерних модулей Runbook](../automation-child-runbooks.md). Если модуль Runbook просматривает одну и ту же функцию в нескольких ресурсах, например в операции базы данных в нескольких базах данных, функцию можно переместить в дочерний модуль Runbook. Каждый дочерний модуль Runbook выполняется параллельно в отдельном процессе. Это уменьшает количество времени на завершение родительского модуля runbook.

Командлеты PowerShell для реализации дочерних runbook:

* [Start-азаутоматионрунбук](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0). Этот командлет позволяет запустить runbook и передать в него параметры.

* [Get-азаутоматионжоб](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0). При наличии операций, которые необходимо выполнить после завершения дочернего Runbook, этот командлет позволяет проверить состояние задания для каждого дочернего модуля.

## <a name="scenario-status-400-bad-request-when-calling-a-webhook"></a><a name="expired webhook"></a>Сценарий: состояние: 400 недопустимый запрос при вызове веб-перехватчика

### <a name="issue"></a>Проблема

При попытке вызвать веб-перехватчик для модуля Runbook службы автоматизации Azure появляется следующее сообщение об ошибке:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Причина:

Вызываемый веб-перехватчик отключен, или срок его действия истек.

### <a name="resolution"></a>Разрешение

если веб-перехватчик отключен, его можно повторно включить на портале Azure. Если срок действия веб-перехватчика истек, его необходимо удалить, а затем создать заново. Если срок действия еще не истек, можно только [обновить веб-перехватчик](../automation-webhooks.md#renew-webhook).

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Сценарий: 429: частота запросов в настоящее время слишком велика...

### <a name="issue"></a>Проблема

При выполнении командлета `Get-AzAutomationJobOutput` вы получаете следующее сообщение об ошибке:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Причина:

Эта ошибка может возникать при получении выходных данных задания из модуля Runbook, который содержит много [подробных потоков](../automation-runbook-output-and-messages.md#verbose-stream).

### <a name="resolution"></a>Разрешение

Чтобы устранить эту ошибку, выполните одно из следующих действий.

* изменить модуль Runbook и сократить число создаваемых потоков заданий;

* уменьшите количество потоков, полученных при выполнении командлета. Для этого можно задать значение `Stream` параметра командлета [Get-азаутоматионжобаутпут](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) , чтобы получить только выходные потоки. 

## <a name="scenario-powershell-job-fails-with-error-cannot-invoke-method"></a><a name="cannot-invoke-method"></a>Сценарий: сбой задания PowerShell с ошибкой: не удается вызвать метод

### <a name="issue"></a>Проблема

При запуске задания PowerShell в модуле Runbook, запущенном в Azure, появляется следующее сообщение об ошибке:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Причина:

Эта ошибка может означать, что модули Runbook, выполняющиеся в песочнице Azure, не могут работать в [полном языковом режиме](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Разрешение

Устранить эту ошибку можно двумя способами.

* Для запуска модуля Runbook вместо [запуска](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/start-job?view=powershell-7)используйте [Start-азаутоматионрунбук](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) .
* Попробуйте запустить модуль Runbook в гибридной рабочей роли Runbook.

Дополнительные сведения об этом поведении и других возможностях модулей Runbook службы автоматизации Azure см. [в статье выполнение Runbook в службе автоматизации Azure](../automation-runbook-execution.md).

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>Сценарий: Гибридная Рабочая роль Runbook Linux получает запрос на ввод пароля при подписании модуля Runbook

### <a name="issue"></a>Проблема

При выполнении `sudo` команды для гибридной рабочей роли Runbook Linux получается непредвиденный запрос на ввод пароля.

### <a name="cause"></a>Причина:

Учетная запись **нксаутоматионусер** для агента log Analytics для Linux неправильно настроена в файле " **sudo** ". Гибридной рабочей роли Runbook требуется соответствующая настройка разрешений учетной записи и других данных, чтобы она могла подписывать модули Runbook в рабочей роли Runbook для Linux.

### <a name="resolution"></a>Разрешение

* Убедитесь, что Гибридная Рабочая роль Runbook имеет исполняемый файл GnuPG (GPG) на компьютере.

* Проверьте конфигурацию учетной записи **нксаутоматионусер** в файле " **sudo** ". См. раздел [Запуск модулей Runbook в гибридной рабочей роли Runbook](../automation-hrw-run-runbooks.md) .

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>Сценарий: сбой командлета в Runbook PowerShell PnP в службе автоматизации Azure

### <a name="issue"></a>Проблема

Когда модуль Runbook записывает автоматически созданный объект PnP PowerShell в выходные данные службы автоматизации Azure, выходные данные командлета не могут выполнять потоковую передачу в службу автоматизации.

### <a name="cause"></a>Причина:

Чаще всего эта проблема возникает, когда служба автоматизации Azure обрабатывает модули Runbook, которые вызывают командлеты PnP PowerShell `add-pnplistitem`, например, без перехвата возвращаемых объектов.

### <a name="resolution"></a>Разрешение

Измените скрипты, чтобы присвоить значения переменным, чтобы командлеты не предпринимали попытку записи целых объектов в стандартный вывод. Скрипт может перенаправить поток вывода в командлет, как показано ниже.

```azurecli
  $null = add-pnplistitem
```

Если сценарий анализирует выходные данные командлета, сценарий должен сохранить выходные данные в переменной и манипулировать переменной вместо просто потоковой передачи выходных данных.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-invalid-status-code-forbidden-when-using-key-vault-inside-a-runbook"></a>Сценарий: недопустимый код состояния "запрещено" при использовании Key Vault в модуле Runbook

### <a name="issue"></a>Проблема

При попытке получить доступ к Key Vault с помощью модуля Runbook службы автоматизации Azure возникает следующая ошибка:

```error
Operation returned an invalid status code 'Forbidden' 
```

### <a name="cause"></a>Причина:

Возможные причины этой проблемы:

* Не использовать учетную запись запуска от имени.
* Недостаточно разрешений.

### <a name="resolution"></a>Разрешение

#### <a name="not-using-run-as-account"></a>Не используется учетная запись запуска от имени

Выполните [Шаг 5. Добавьте проверку подлинности для управления ресурсами Azure](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell#add-authentication-to-manage-azure-resources) , чтобы обеспечить использование учетной записи запуска от имени для доступа к Key Vault. 

#### <a name="insufficient-permissions"></a>Недостаточные разрешения

[Добавьте разрешения для Key Vault](https://docs.microsoft.com/azure/automation/manage-runas-account#add-permissions-to-key-vault) , чтобы убедиться, что учетная запись запуска от имени имеет достаточные разрешения для доступа к Key Vault. 

## <a name="my-problem-isnt-listed-above"></a><a name="other"></a>Моя проблема не указана в этой статье

В следующих разделах перечислены другие распространенные ошибки и предоставлена вспомогательная документация, помогающая устранить проблему.

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>Гибридная рабочая роль runbook не выполняет задания или не отвечает

Если вы используете задания в гибридной рабочей роли Runbook, а не в службе автоматизации Azure, вам может потребоваться [устранить неполадки в работе гибридной рабочей роли](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker).

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>Сбой Runbook без разрешения или некоторого варианта

Учетные записи запуска от имени могут не иметь тех же разрешений на ресурсы Azure, что и текущая учетная запись. Убедитесь, что учетная запись запуска от имени имеет [разрешения на доступ к любым ресурсам](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) , используемым в скрипте.

### <a name="issues-passing-parameters-into-webhooks"></a>Проблемы при передаче параметров в веб-перехватчики

Дополнительные сведения о передаче параметров в веб-перехватчики см. в статье [Запуск Runbook из веб-перехватчика](../automation-webhooks.md#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="issues-using-az-modules"></a>Проблемы с использованием модулей AZ

Использование неполного переноса модулей Runbook из AzureRM в AZ может привести к сбоям в работе песочницы и сбоям Runbook. См. раздел [Использование модулей в модулях Runbook](../automation-runbook-execution.md#using-modules-in-your-runbooks).

### <a name="inconsistent-behavior-in-runbooks"></a>Несогласованное поведение модулей runbook

Следуйте указаниям в [выполнении модуля Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) , чтобы избежать проблем с параллельными заданиями, ресурсами, создаваемыми несколько раз, или другой логикой с учетом времени в модулях Runbook.

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Модуль Runbook завершается ошибкой без разрешения, запрещен (403) или какой-либо вариант

Учетные записи запуска от имени могут не иметь тех же разрешений на ресурсы Azure, что и текущая учетная запись. Убедитесь, что учетная запись запуска от имени имеет [разрешения на доступ к любым ресурсам](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) , используемым в скрипте.

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Модули runbook работали, но внезапно были остановлены

* Убедитесь, что срок действия учетной записи запуска от имени не истек. См. статью [продление сертификации](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Если вы используете веб- [перехватчик](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook) для запуска модуля Runbook, убедитесь, что срок действия веб-перехватчика не истек.

### <a name="passing-parameters-into-webhooks"></a>Передача параметров в веб-перехватчики

Дополнительные сведения о передаче параметров в веб-перехватчики см. в статье [Запуск Runbook из веб-перехватчика](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="using-self-signed-certificates"></a>Использование самозаверяющих сертификатов

Сведения о том, как использовать самозаверяющие сертификаты, см. в разделе [Создание нового сертификата](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate).

### <a name="access-denied-when-using-azure-sandbox-for-a-runbook"></a>Отказано в доступе при использовании "песочницы Azure" для модуля Runbook

"Песочница Azure" предотвращает доступ ко всем необработанным COM-серверам. Например, изолированное приложение или модуль Runbook не может вызвать инструментарий управления Windows (WMI) (WMI) или в службу установщик Windows (мсисервер. exe). Дополнительные сведения об использовании песочницы см. в разделе [выполнение Runbook в службе автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-runbook-execution).

## <a name="recommended-documents"></a>Рекомендуемые документы

* [Выполнение модуля Runbook в службе автоматизации Azure](../automation-runbook-execution.md)
* [Запуск модуля Runbook в службе автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Выполнение модуля Runbook в службе автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>Дальнейшие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключайтесь с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества обслуживания клиентов путем подключения сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **получить поддержку**.
