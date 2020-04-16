---
title: Выполнение модулей runbook в гибридной рабочей роли Runbook в службе автоматизации Azure
description: Эта статья содержит сведения о выполнении модулей runbook на компьютерах в локальном центре обработки данных или поставщике облачных решений с помощью гибридной рабочей роли Runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: b65c72e0c65cf9aa84cb614478fbdf78258f3054
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405829"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Запуск модулей runbook в гибридной рабочей роли Runbook

Runbooks, ориентированные на hybrid Runbook Worker, обычно управляют ресурсами на локальном компьютере или против ресурсов в локальной среде, где работает работник. Модули Runbook в службе автоматизации Azure обычно управляют ресурсами в облаке Azure. Несмотря на то, что они используются по-разному, runbooks, которые работают в Azure Automation, и runbooks, которые работают на гибридном Runbook Worker, идентичны по структуре.

Когда вы автор книги для запуска на гибридный Runbook работника, вы должны отойти и проверить runbook на машине, которая размещает работника. Машина-хоста имеет все модули PowerShell и сетевой доступ, необходимый для управления и доступа к местным ресурсам. После тестирования runbook на гибридной машине Runbook Worker можно загрузить ее в среду автоматизации Azure, где она может быть запущена на сотрудника. 

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

## <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Разрешения Runbook для гибридного сотрудника Runbook

Поскольку они получают доступ к ресурсам, не относящимся к Azure, рунированные книги, работающие на гибридном работнике Runbook, не могут использовать механизм проверки подлинности, обычно используемый рунами для проверки подлинности ресурсов Azure. Запуск либо предоставляет собственную аутентификацию для локальных ресурсов, либо настраивает аутентификацию с помощью [управляемых идентификаторов для ресурсов Azure.](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager) Вы также можете указать учетную запись Run As, чтобы предоставить пользовательский контекст для всех runbooks.

### <a name="runbook-authentication"></a>Проверка подлинности модуля Runbook

По умолчанию runbooks заражают сяопрочения на локальном компьютере. Для Windows они работают в контексте локальной учетной записи **System.** Для Linux они работают в контексте специальной учетной записи пользователя **nxautomation.** В любом случае, runbooks должны обеспечить свою собственную аутентификацию ресурсов, к которым они имеют доступ.

Активы [учетных данных](automation-credentials.md) и [сертификатов](automation-certificates.md) можно использовать в своей службе с помощью cmdlets, которые позволяют указывать учетные данные, чтобы авторбук мог аутентифицироватьподлинное использование различных ресурсов. В следующем примере показана часть модуля Runbook, предназначенная для перезапуска компьютера. Он извлекает учетные данные из ресурса учетных данных и имя компьютера `Restart-Computer` из переменного актива, а затем использует эти значения с cmdlet.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Вы также можете использовать действие [InlineScript.](automation-powershell-workflow.md#inlinescript) `InlineScript`позволяет запускать блоки кода на другом компьютере с учетными данными, указанными [общим параметром PSCredential.](/powershell/module/psworkflow/about/about_workflowcommonparameters)

### <a name="run-as-account"></a>учетная запись запуска от имени

Вместо того, чтобы ваша книга по запуску предоставляла свою собственную аутентификацию местным ресурсам, можно указать учетную запись Run As для гибридной группы Runbook Worker. Для этого необходимо определить [ресурс учетных данных,](automation-credentials.md) который имеет доступ к локальным ресурсам. Эти ресурсы включают в себя сертификаты магазинов и все runbooks работать под этими учетные данные на гибридных Runbook работника в группе.

Имя пользователя для учетных данных должно быть представлено в одном из следующих форматов:

* домен\имя пользователя;
* username@domain
* имя пользователя (для локальных учетных записей на локальном компьютере).

Используйте следующую процедуру, чтобы указать учетную запись Run As для гибридной группы Runbook Worker.

1. Создайте [ресурс учетных данных](automation-credentials.md) с доступом к локальным ресурсам.
2. На портале Azure откройте учетную запись службы автоматизации.
3. Щелкните плитку **Группы гибридных рабочих ролей** и выберите группу.
4. Выберите **все настройки,** за которыми **следуют настройки группы гибридных сотрудников.**
5. Измените значение **Run As** от по **умолчанию** к **custom.**
6. Выберите учетные данные и нажмите кнопку **Сохранить**.

### <a name="managed-identities-for-azure-resources"></a><a name="managed-identities-for-azure-resources"></a>Управляемые идентификаторы для ресурсов Azure

Гибридные runbook Workers на виртуальных машинах Azure могут использовать управляемые идентификаторы для ресурсов Azure для аутентификации ресурсов Azure. Использование управляемых идентификаторов для ресурсов Azure вместо учетных записей Run As дает преимущества, поскольку вам не нужно:

* Экспортировать сертификат Run As, а затем импортировать его в Hybrid Runbook Worker.
* Обновите сертификат, используемый учетной записью Run As.
* Обработка объекта соединения Run As в коде runbook.

Выполните следующие шаги по использованию управляемого итев для ресурсов Azure в гибридном работнике Runbook.

1. Создайте Azure VM.
2. Настройка управляемых идентификаторов для ресурсов Azure на VM. [См. Настройку управляемых идентификаторов для ресурсов Azure на VM с помощью портала Azure.](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)
3. Предоставь VM доступ к группе ресурсов в ресурсном менеджере. Обратитесь к [использованию системы Windows VM, назначенной управляемой имитаю, для доступа к диспетчеру ресурсов.](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)
4. Установите гибридный работник Runbook на VM. Смотрите [Развертывание Windows Гибридный Runbook работника](automation-windows-hrw-install.md).
5. Обновите беговую книгу, чтобы использовать `Identity` cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) с параметром для проверки подлинности ресурсов Azure. Эта конфигурация уменьшает необходимость использования учетной записи Run As и выполнения связанного управления учетной записью.

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity`работает для гибридного Runbook Worker, используя установленную системой идентификацию и одиночное удостоверение, назначенное пользователем. Если вы используете несколько идентификаторов, назначенных пользователем, в Гибридном Работнике Runbook, в службе запуска должна быть `AccountId` указана параметр для `Connect-AzAccount` выбора определенного удостоверения, назначенного пользователем.

### <a name="automation-run-as-account"></a><a name="runas-script"></a>Учетная запись запуска от имени службы автоматизации

В рамках автоматизированного процесса сборки для развертывания ресурсов в Azure может потребоваться доступ к предприимчивым системам для поддержки задачи или набора шагов в последовательности развертывания. Чтобы обеспечить аутентификацию в отношении Azure с помощью учетной записи Run As, необходимо установить сертификат учетной записи Run As.

Следующий runbook PowerShell, называемый **Export-RunAsCertificateToHybridWorker,** экспортирует сертификат Run As из вашей учетной записи Автоматизации Azure. Runbook загружает и импортирует сертификат в местный магазин сертификатов машины на гибридном Runbook Worker, который подключен к той же учетной записи. Как только он завершает этот шаг, в службе запуска проверяется, что сотрудник может успешно аутентифицировать проверку Azure с помощью учетной записи Run As.

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>Для PowerShell runbooks, `Add-AzAccount` и `Add-AzureRMAccount` псевдонимы для `Connect-AzAccount`. При поиске элементов библиотеки, `Connect-AzAccount`если вы `Add-AzAccount`не видите, вы можете использовать, или вы можете обновить свои модули в вашей учетной записи автоматизации.

Чтобы закончить подготовку Run As аккаунта:

1. Сохраните на компьютере runbook **Export-RunAsCertificateToHybridWorker** на вашем компьютере с расширением **.ps1.**
2. Импортируйте его в свой аккаунт автоматизации.
3. Отспособьте runbook, изменив `Password` значение переменной на свой пароль. 
4. Опубликуйте книгу.
5. Запустите runbook, ориентируясь на группу Hybrid Runbook Worker, которая запускает и проверяет подлинность рунсов с помощью учетной записи Run As. 
6. Изучите поток задания, чтобы увидеть, что он сообщает о попытке импортировать сертификат в местный магазин машин, и следует с несколькими строками. Такое поведение зависит от количества учетных записей Automation, которые вы определяете в подписке, и степени успешности проверки подлинности.

## <a name="job-behavior-on-hybrid-runbook-workers"></a>Поведение рабочих мест на гибридных работниках Runbook

Azure Automation обрабатывает задания на гибридных Runbook Workers несколько отличаются от заданий, работающих в песочницах Azure. Одним из ключевых отличий является то, что нет никаких ограничений на продолжительность работы на runbook работников. Runbooks, запускаемые в песочницах Azure, ограничены тремя часами из-за [справедливой доли.](automation-runbook-execution.md#fair-share)

Для длительной книги запуска необходимо убедиться, что она устойчива к возможной перезагрузке, например, если машина, вмещаещем работу рабочего, перезагружается. Если гибридная машина-хост Runbook Worker перезагружается, любая работа запуска runbook перезапускается с самого начала или с последнего контрольно-пропускного пункта для runbooks Workflow PowerShell. После того, как задание runbook перезапускается более трех раз, оно приостанавливается.

Помните, что задания для hybrid Runbook Workers работают под локальной учетной записью System на Windows или учетной записью **nxautomation** на Linux. Для Linux необходимо убедиться, что учетная запись **nxautomation** имеет доступ к месту хранения модулей runbook. При использовании [cmdlet Install-Module](/powershell/module/powershellget/install-module) обязательно укажите всех `Scope` пользователей для параметра, чтобы обеспечить доступ к учетной записи **nxautomation.** Для получения дополнительной информации о PowerShell на Linux, [см.](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms)

## <a name="starting-a-runbook-on-a-hybrid-runbook-worker"></a>Запуск книги на гибридных Runbook работника

[Запуск книги в Azure Automation](automation-starting-a-runbook.md) описывает различные методы запуска runbook. Стартап для runbook на гибридном Runbook Worker использует опцию **Run on,** которая позволяет указать название группы Hybrid Runbook Worker. Когда группа указана, один из работников в этой группе получает и запускает runbook. Если в runbook нет этой опции, Azure Automation запускает runbook в обычном режиме.

При запуске книги на портале Azure вам будет представлен авоты **Run на** опцию, для которой можно выбрать **Azure** или **Hybrid Worker.** Если вы выберете **Hybrid Worker,** вы можете выбрать группу Hybrid Runbook Worker из группы выпадающих.

Используйте `RunOn` параметр с cmdlet [Start-AzAutomationRunbook.](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) В следующем примере Windows PowerShell используется для запуска книги по тестированию под названием **Test-Runbook** в группе Hybrid Runbook Worker под названием MyHybridGroup.

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> Вы должны [скачать последнюю версию PowerShell,](https://azure.microsoft.com/downloads/) если у вас есть ранее один установлен. Установите эту версию только на рабочей станции, где вы начинаете runbook от PowerShell. Вам не нужно устанавливать его на гибридный компьютер Runbook Worker, если вы не собираетесь начать runbooks с этого компьютера.

## <a name="working-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Работа с подписанными runbooks на Windows Гибридный Runbook работника

Можно настроить Windows Hybrid Runbook Worker для запуска только подписанных рун.

> [!IMPORTANT]
> Настроенные гибридные рабочие роли Runbook запускаются, только когда модули Runbook подписаны, а модули Runbook, которые не были подписаны, не будут выполнены в рабочей роли.

### <a name="create-signing-certificate"></a>Создание сертификата для подписи

В следующем примере создается самозаверяющийся сертификат, который можно использовать для подписывания модулей runbook. Этот код создает сертификат и экспортирует его так, что гибридный работник Runbook может импортировать его позже. Отпечаток пальца также возвращается для последующего использования при ссылках на сертификат.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Импортный сертификат и настройка работников для проверки подписи

Копируйте сертификат, созданный для каждого гибридного сотрудника Runbook в группе. Выполнить следующий скрипт для импорта сертификата и настроить работников для использования проверки подписи на runbooks.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Подпишите свои книги с помощью сертификата

С гибридными работниками Runbook настроены использовать только подписанные runbooks, вы должны подписать runbooks, которые должны быть использованы на гибридных Runbook работника. Используйте следующий образец кода PowerShell, чтобы подписать эти runbooks.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

При подписании книги, вы должны импортировать его в свою учетную запись Автоматизация и опубликовать его с подписью блока. Чтобы узнать, как импортировать модули runbook, ознакомьтесь с разделом [Creating or importing a runbook in Azure Automation](manage-runbooks.md#importing-a-runbook) (Создание или импорт модуля Runbook в службу автоматизации Azure).

## <a name="working-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Работа с подписанными рунбуками на Linux Hybrid Runbook Worker

Чтобы иметь возможность работать с подписанными runbooks, Linux Гибридный Runbook Работник должен иметь [GPG](https://gnupg.org/index.html) исполняемых на локальной машине.

> [!IMPORTANT]
> Настроенные гибридные рабочие роли Runbook запускаются, только когда модули Runbook подписаны, а модули Runbook, которые не были подписаны, не будут выполнены в рабочей роли.

### <a name="create-a-gpg-keyring-and-keypair"></a>Создание набора и пары ключей GPG

Для создания клавиатуры GPG и клавиатуры используйте учетную запись Hybrid Runbook Worker **nxautomation.**

1. Используйте приложение sudo для входной регистрации в качестве учетной записи **nxautomation.**

    ```bash
    sudo su – nxautomation
    ```

2. После того как вы используете **nxautomation,** создать GPG keypair. GPG проведет вас через шаги. Вы должны укажите имя, адрес электронной почты, время истечения срока годности и пароль. Затем вы ждете, пока на машине будет достаточно энтропии для получения ключа.

    ```bash
    sudo gpg --generate-key
    ```

3. Поскольку каталог GPG был создан с помощью sudo, необходимо сменить владельца на **nxautomation** с помощью следующей команды.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Сделайте клавиатуру доступной для гибридного сотрудника Runbook

После создания ключа сделайте его доступным для гибридного runbook Worker. Измените файл настроек **/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf,** чтобы включить `[worker-optional]`следующий пример кода в раздел файла.

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Проверить, что проверка подписи на

Если проверка подписи была отключена на машине, необходимо включить ее, запустив следующую команду sudo. Замените идентификатор `<LogAnalyticsworkspaceId>` рабочего пространства.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Подпись модуля runbook

После настройки проверки подписи используйте следующую команду GPG, чтобы подписать книгу запуска.

```bash
gpg –-clear-sign <runbook name>
```

Подписанный runbook называется ** <runbook name>.asc**.

Теперь вы можете загрузить подписанный runbook в Azure Automation и выполнить его как обычный runbook.

## <a name="next-steps"></a>Дальнейшие действия

* Подробнее о методах запуска runbook можно узнать в журнале [Startingbook.](automation-starting-a-runbook.md)
* Чтобы понять, как использовать текстовый редактор для работы с запусками PowerShell в Azure Automation, смотрите [Редактирование Runbook в Azure Automation.](automation-edit-textual-runbook.md)
* Если ваши runbooks не завершают успешно, просмотрите руководство по устранению неполадок для [сбоев выполнения runbook.](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)
* Для получения дополнительной информации о PowerShell, включая языковые справочные и учебные модули, обратитесь к [документам PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
* Для справки PowerShell cmdlet [см.](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)
