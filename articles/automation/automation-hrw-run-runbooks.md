---
title: Выполнение модулей runbook в гибридной рабочей роли Runbook в службе автоматизации Azure
description: Эта статья содержит сведения о выполнении модулей runbook на компьютерах в локальном центре обработки данных или поставщике облачных решений с помощью гибридной рабочей роли Runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: c67fff32770446cac3adef8af50c9e5733077bc7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372401"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Запуск модулей runbook в гибридной рабочей роли Runbook

Модули Runbook, предназначенные для гибридной рабочей роли Runbook, обычно управляют ресурсами на локальном компьютере или с ресурсами в локальной среде, в которой развернута рабочая роль. Модули Runbook в службе автоматизации Azure обычно управляют ресурсами в облаке Azure. Несмотря на то, что они используются по-разному, модули Runbook, работающие в службе автоматизации Azure и модули Runbook, которые выполняются в гибридной рабочей роли Runbook, идентичны в структуре.

При создании модуля Runbook для запуска в гибридной рабочей роли Runbook необходимо изменить и протестировать модуль Runbook на компьютере, на котором размещена рабочая роль. На главном компьютере есть все модули PowerShell и сетевой доступ, необходимые для управления локальными ресурсами и доступа к ним. После тестирования модуля Runbook на компьютере гибридной рабочей роли Runbook его можно передать в среду службы автоматизации Azure, где ее можно будет запустить в рабочей роли. 

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке AZ Module в гибридной рабочей роли Runbook см. в статье [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Для учетной записи службы автоматизации можно обновить модули до последней версии, используя [обновление модулей Azure PowerShell в службе автоматизации Azure](automation-update-azure-modules.md).

## <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Разрешения Runbook для гибридной рабочей роли Runbook

Так как они обращаются к ресурсам, не относящимся к Azure, модули Runbook, работающие в гибридной рабочей роли Runbook, не могут использовать механизм проверки подлинности, обычно используемый модулями Runbook для проверки подлинности ресурсов Azure Модуль Runbook либо предоставляет собственную проверку подлинности для локальных ресурсов, либо настраивает проверку подлинности с помощью [управляемых удостоверений для ресурсов Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Можно также указать учетную запись запуска от имени, чтобы предоставить контекст пользователя для всех модулей Runbook.

### <a name="runbook-authentication"></a>Проверка подлинности модуля Runbook

По умолчанию модули Runbook выполняются на локальном компьютере. Для Windows они выполняются в контексте локальной системной учетной записи. Для Linux они выполняются в контексте специальной учетной записи пользователя **нксаутоматион**. В любом сценарии модули Runbook должны предоставлять собственную проверку подлинности для ресурсов, к которым они обращаются.

Вы можете использовать [учетные](automation-credentials.md) данные и ресурсы [сертификатов](automation-certificates.md) в модуле Runbook с командлетами, которые позволяют указать учетные данные, чтобы модуль Runbook мог проходить проверку подлинности в разных ресурсах. В следующем примере показана часть модуля Runbook, предназначенная для перезапуска компьютера. Он извлекает учетные данные из ресурса учетных данных и имя компьютера из переменного ресурса, а затем использует эти значения с командлетом **Restart-Computer** .

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Можно также использовать действие [InlineScript](automation-powershell-workflow.md#inlinescript) . InlineScript позволяет запускать блоки кода на другом компьютере с учетными данными, указанными в [общем параметре PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="run-as-account"></a>учетная запись запуска от имени

Вместо того чтобы модуль Runbook предоставил собственную проверку подлинности для локальных ресурсов, можно указать учетную запись запуска от имени для группы гибридных рабочих ролей Runbook. Для этого необходимо определить [ресурс учетных данных](automation-credentials.md) , который имеет доступ к локальным ресурсам. Эти ресурсы включают хранилища сертификатов, и все модули Runbook выполняются под этими учетными данными в гибридной рабочей роли Runbook в группе.

Имя пользователя для учетных данных должно быть представлено в одном из следующих форматов:

* домен или имя пользователя
* username@domain
* имя пользователя (для локальных учетных записей на локальном компьютере).

Используйте следующую процедуру, чтобы указать учетную запись запуска от имени для группы гибридных рабочих ролей Runbook.

1. Создайте [ресурс учетных данных](automation-credentials.md) с доступом к локальным ресурсам.
2. На портале Azure откройте учетную запись службы автоматизации.
3. Щелкните плитку **Группы гибридных рабочих ролей** и выберите группу.
4. Выберите **все параметры**, а затем — **Параметры группы гибридных рабочих ролей**.
5. Измените значение параметра **Запуск от имени** с **Default** на **Custom**.
6. Выберите учетные данные и нажмите кнопку **Сохранить**.

### <a name="managed-identities-for-azure-resources"></a>Управляемые удостоверения для ресурсов Azure

Гибридные рабочие роли Runbook на виртуальных машинах Azure могут использовать управляемые удостоверения для ресурсов Azure для проверки подлинности в ресурсах Azure. Использование управляемых удостоверений для ресурсов Azure вместо учетных записей запуска от имени предоставляет преимущества, поскольку вам не нужно:

* Экспортируйте сертификат запуска от имени, а затем импортируйте его в гибридную рабочую роль Runbook
* Продлите сертификат, используемый учетной записью запуска от имени
* Обработку объекта подключения запуска от имени в коде модуля Runbook

Выполните следующие действия, чтобы использовать управляемое удостоверение для ресурсов Azure в гибридной рабочей роли Runbook.

1. Создайте виртуальную машину Azure.
2. Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине. См. раздел [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине с помощью портал Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
3. Предоставьте виртуальной машине доступ к группе ресурсов в диспетчер ресурсов. Чтобы [получить доступ к Диспетчер ресурсов, обратитесь к статье Использование управляемого удостоверения, назначенного системой виртуальной машины Windows](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
4. Установите гибридную рабочую роль Runbook на виртуальной машине. См. раздел [развертывание гибридной рабочей роли Runbook Windows](automation-windows-hrw-install.md).
5. Обновите модуль Runbook, чтобы использовать командлет [Connect-азаккаунт](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) с параметром *Identity* для проверки подлинности в ресурсах Azure. Такая конфигурация сокращает потребность в использовании учетной записи запуска от имени и управление соответствующей учетной записью.

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity` работает для гибридной рабочей роли Runbook, используя назначенное системой удостоверение и одно назначенное пользователем удостоверение. Если для гибридной рабочей роли Runbook используется несколько пользовательских удостоверений, модуль Runbook должен указать параметр *accountID* для **Connect-азаккаунт** , чтобы выбрать конкретное назначенное пользователем удостоверение.

### <a name="runas-script"></a>Учетная запись запуска от имени службы автоматизации

В рамках автоматизированного процесса сборки для развертывания ресурсов в Azure может потребоваться доступ к локальным системам для поддержки задачи или набора действий в последовательности развертывания. Чтобы обеспечить проверку подлинности в Azure с помощью учетной записи запуска от имени, необходимо установить сертификат учетной записи запуска от имени.

Следующий модуль Runbook PowerShell, именуемый **Export-RunAsCertificateToHybridWorker**, экспортирует сертификат запуска от имени из учетной записи службы автоматизации Azure. Модуль Runbook скачивает и импортирует сертификат в хранилище сертификатов локального компьютера в гибридной рабочей роли Runbook, подключенной к той же учетной записи. После завершения этого шага модуль Runbook проверяет, может ли рабочий процесс пройти проверку подлинности в Azure с помощью учетной записи запуска от имени.

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
>Для модулей Runbook PowerShell **Add-азаккаунт** и **Add-AzureRMAccount** являются псевдонимами для **Connect-азаккаунт**. Если при поиске элементов библиотеки вы не видите пункт **Connect-азаккаунт**, можно использовать **Add-азаккаунт**или обновить модули в учетной записи службы автоматизации.

Чтобы завершить подготовку учетной записи запуска от имени, сделайте следующее:

1. Сохраните модуль Runbook **Export-RunAsCertificateToHybridWorker** на компьютере с расширением **PS1** .
2. Импортируйте его в учетную запись службы автоматизации.
3. Измените модуль Runbook, изменив значение переменной *пароля* o собственным паролем. 
4. Опубликуйте модуль Runbook.
5. Запустите модуль Runbook, нацеливание на группу гибридных рабочих ролей Runbook, которая выполняет и выполняет проверку подлинности модулей Runbook с помощью учетной записи запуска от имени. 
6. Изучите поток задания, чтобы убедиться, что он сообщает о попытке импортировать сертификат в хранилище локального компьютера, а затем использует несколько строк. Это поведение зависит от количества учетных записей службы автоматизации, определяемых в подписке, и степени успеха проверки подлинности.

## <a name="job-behavior-on-hybrid-runbook-workers"></a>Поведение задания в гибридных рабочих ролях Runbook

Служба автоматизации Azure обрабатывает задания в гибридных рабочих ролях Runbook несколько иначе из заданий, выполняемых в изолированных средах Azure. Одно из ключевых различий заключается в том, что для рабочих процессов Runbook не существует ограничения на длительность задания. Модули Runbook, выполняемые в изолированных средах Azure, ограничены тремя часами из-за [справедливого предоставления общего доступа](automation-runbook-execution.md#fair-share).

Для долго выполняющегося модуля Runbook необходимо обеспечить устойчивость к возможному перезапуску, например, если компьютер, на котором размещается Рабочая роль, перезагружается. Если Гибридный компьютер узла Runbook Worker перезапускается, любое запущенное задание Runbook перезапускается с самого начала или из последней контрольной точки для модулей Runbook рабочего процесса PowerShell. После перезапуска задания Runbook более трех раз оно приостанавливается.

Помните, что задания гибридных рабочих ролей Runbook выполняются под локальной системной учетной записью Windows или учетной записью **нксаутоматион** в Linux. Для Linux необходимо убедиться, что учетная запись **нксаутоматион** имеет доступ к расположению, где хранятся модули Runbook. При использовании командлета [Install-Module](/powershell/module/powershellget/install-module) обязательно укажите **ALLUSERS** для параметра *области* , чтобы обеспечить доступ к учетной записи **нксаутоматион** . Дополнительные сведения о PowerShell в Linux см. в статье [Известные проблемы для PowerShell на платформах, отличных от Windows](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="starting-a-runbook-on-a-hybrid-runbook-worker"></a>Запуск Runbook в гибридной рабочей роли Runbook

[Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md) описывает различные методы запуска модуля Runbook. При запуске модуля Runbook в гибридной рабочей роли Runbook используется параметр **выполнить в** , который позволяет указать имя группы гибридных рабочих ролей Runbook. Если указана группа, то один из сотрудников этой группы получает и выполняет модуль Runbook. Если в модуле Runbook не указан этот параметр, служба автоматизации Azure запускает модуль Runbook обычным образом.

При запуске модуля Runbook в портал Azure вы увидите параметр **выполнить в** , для которого можно выбрать **Azure** или **гибридную рабочую роль**. Если выбран вариант **Гибридная Рабочая роль**, можно выбрать группу гибридных рабочих ролей Runbook из раскрывающегося списка.

Используйте параметр *RunOn* с командлетом **Start-AzureAutomationRunbook** . В следующем примере Windows PowerShell используется для запуска модуля Runbook с именем **Test-Runbook** в группе гибридной рабочей роли Runbook с именем михибридграуп.

```azurepowershell-interactive
Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> Параметр *RunOn* был добавлен в командлет **Start-AzureAutomationRunbook** в версии 0.9.1 Microsoft Azure PowerShell. Если у вас установлена более ранняя версия, следует [загрузить последнюю версию](https://azure.microsoft.com/downloads/) . Установите эту версию только на рабочей станции, где запускается модуль Runbook из PowerShell. Не нужно устанавливать его на компьютере гибридной рабочей роли Runbook, если вы не планируете запускать модули Runbook с этого компьютера.

## <a name="working-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Работа с подписанными модулями Runbook в гибридной рабочей роли Runbook Windows

Вы можете настроить гибридную рабочую роль Runbook Windows для запуска только подписанных модулей Runbook.

> [!IMPORTANT]
> После настройки гибридной рабочей роли Runbook для запуска только подписанных модулей Runbook рабочие модули Runbook, которые не были подписаны, не будут выполняться в рабочей роли.

### <a name="create-signing-certificate"></a>Создание сертификата для подписи

В следующем примере создается самозаверяющийся сертификат, который можно использовать для подписывания модулей runbook. Этот код создает сертификат и экспортирует его, чтобы Гибридная Рабочая роль Runbook могла импортировать его позже. Отпечаток также возвращается для последующего использования в ссылке на сертификат.

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

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Импорт сертификата и Настройка рабочих ролей для проверки подписи

Скопируйте созданный вами сертификат в каждую гибридную рабочую роль Runbook в группе. Выполните следующий скрипт, чтобы импортировать сертификат и настроить его на использование проверки подписи в модулях Runbook.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Подписывание модулей Runbook с помощью сертификата

Если гибридные рабочие роли Runbook настроены для использования только подписанных модулей Runbook, необходимо подписывать модули Runbook, которые будут использоваться в гибридной рабочей роли Runbook. Чтобы подписать эти модули Runbook, используйте следующий пример кода PowerShell.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Если модуль Runbook был подписан, его необходимо импортировать в учетную запись службы автоматизации и опубликовать с помощью блока Signature. Чтобы узнать, как импортировать модули runbook, ознакомьтесь с разделом [Creating or importing a runbook in Azure Automation](manage-runbooks.md#import-a-runbook) (Создание или импорт модуля Runbook в службу автоматизации Azure).

## <a name="working-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Работа с подписанными модулями Runbook в гибридной рабочей роли Runbook Linux

Чтобы иметь возможность работать с подписанными модулями Runbook, в гибридной рабочей роли Runbook Linux должен быть исполняемый файл [GPG](https://gnupg.org/index.html) на локальном компьютере.

> [!IMPORTANT]
> После настройки гибридной рабочей роли Runbook для запуска только подписанных модулей Runbook рабочие модули Runbook, которые не были подписаны, не будут выполняться в рабочей роли.

### <a name="create-a-gpg-keyring-and-keypair"></a>Создание набора и пары ключей GPG

Чтобы создать набор ключей и пару ключей GPG, используйте учетную запись гибридной рабочей роли Runbook Worker **нксаутоматион** .

1. Используйте приложение sudo для входа в качестве учетной записи **нксаутоматион** .

    ```bash
    sudo su – nxautomation
    ```

2. После использования **нксаутоматион**Создайте пару ключей GPG. GPG поможет выполнить шаги. Необходимо указать имя, адрес электронной почты, срок действия и парольную фразу. Затем подождите, пока на компьютере будет достаточно энтропии, чтобы создать ключ.

    ```bash
    sudo gpg --generate-key
    ```

3. Так как каталог GPG был создан с помощью sudo, необходимо изменить его владельца на **нксаутоматион** с помощью следующей команды.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Обеспечение доступности набора ключей для гибридной рабочей роли Runbook

После создания набора ключей сделайте его доступным для гибридной рабочей роли Runbook. Измените файл параметров `/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf` чтобы включить следующий пример кода в раздел File **[Worker — необязательно]** .

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Убедитесь, что проверка подписи включена

Если проверка подписи была отключена на компьютере, необходимо включить ее, выполнив следующую команду sudo. Замените `<LogAnalyticsworkspaceId>` ИДЕНТИФИКАТОРом рабочей области.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Подпись модуля runbook

После настройки проверки подписи используйте следующую команду GPG, чтобы подписать модуль Runbook.

```bash
gpg –-clear-sign <runbook name>
```

Подписанный Runbook называется `<runbook name>.asc`.

Теперь вы можете передать подписанный модуль Runbook в службу автоматизации Azure и выполнить его как обычный модуль Runbook.

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о методах запуска модуля Runbook см. в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).
* Чтобы понять, как использовать текстовый редактор для работы с модулями Runbook PowerShell в службе автоматизации Azure, см. статью [изменение модуля Runbook в службе автоматизации Azure](automation-edit-textual-runbook.md).
* Если модули Runbook не завершаются успешно, ознакомьтесь с руководством по устранению неполадок при [сбоях выполнения Runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Дополнительные сведения о PowerShell, включая Справочник по языку и обучающие модули, см. в документации по [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
