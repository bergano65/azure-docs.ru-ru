---
title: Управление учетной записью запуска от имени службы автоматизации Azure
description: В этой статье объясняется, как управлять учетной записью запуска от имени с помощью PowerShell или портала Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: d85eac7af71f03be384b8fa9d9603525c0807dac
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770794"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Управление учетной записью запуска от имени службы автоматизации Azure

Учетные записи запуска от имени в службе автоматизации Azure позволяют выполнять аутентификацию для управления ресурсами в Azure с помощью командлетов Azure. При создании учетной записи запуска от имени в Azure Active Directory (AD) создается пользователь субъекта-службы и ему назначается роль участника на уровне подписки.

## <a name="types-of-run-as-accounts"></a>Типы учетных записей запуска от имени

Служба автоматизации Azure использует два типа учетных записей запуска от имени:

* учетная запись запуска от имени Azure;
* классическая учетная запись запуска от имени Azure.

>[!NOTE]
>Подписки Поставщика облачных речений Azure (CSP) поддерживают только модель Azure Resource Manager. Службы, не являющиеся частью Azure Resource Manager, недоступны в программе. При использовании подписки CSP создается не классическая учетная запись запуска от имени Azure, а учетная запись запуска от имени Azure. Дополнительные сведения о подписках CSP см. в разделе [Доступные службы в подписках CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).

У субъекта-службы для учетной записи запуска от имени по умолчанию нет разрешений на чтение данных из Azure AD. Если вы хотите добавить разрешения для чтения данных из Azure AD или управления ими, необходимо предоставить субъекту-службе разрешения в разделе **Разрешения API**. Дополнительные сведения см. в разделе [Добавление разрешений для доступа к веб-API](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

### <a name="run-as-account"></a>учетная запись запуска от имени

Учетная запись запуска от имени используется для управления ресурсами в [модели развертывания Resource Manager](../azure-resource-manager/management/deployment-models.md). Она выполняет следующее.

* Создает приложение Azure AD с самозаверяющим сертификатом, учетную запись субъекта-службы для этого приложения в Azure AD, а также назначает роль участника для учетной записи в текущей подписке. Вместо этого параметра сертификата можно использовать роль владельца или любую другую роль. Дополнительные сведения см. в статье [Управление доступом на основе ролей в службе автоматизации Azure](automation-role-based-access-control.md).
  
* Создает ресурс сертификата службы автоматизации `AzureRunAsCertificate` в указанной учетной записи службы автоматизации. Этот ресурс содержит закрытый ключ сертификата, используемый в приложении Azure AD.
  
* Создает ресурс подключения службы автоматизации `AzureRunAsConnection` в указанной учетной записи службы автоматизации. Этот ресурс содержит идентификаторы приложения, клиента и подписки, а также отпечаток сертификата.

### <a name="azure-classic-run-as-account"></a>Классическая учетная запись запуска от имени Azure

Классическая учетная запись запуска от имени Azure используется для управления ресурсами в [классической модели развертывания Azure](../azure-resource-manager/management/deployment-models.md). Для создания или обновления учетной записи этого типа необходимо быть соадминистратором подписки.

Классическая учетная запись запуска от имени Azure выполняет следующее.

  * Создает сертификат управления в подписке.

  * Создает ресурс сертификата службы автоматизации `AzureClassicRunAsCertificate` в указанной учетной записи службы автоматизации. Этот ресурс содержит закрытый ключ сертификата, используемый в сертификате управления.

  * Создает ресурс подключения службы автоматизации `AzureClassicRunAsConnection` в указанной учетной записи службы автоматизации. Этот ресурс содержит имя подписки, идентификатор подписки и имя ресурса сертификата.

>[!NOTE]
>Классическая учетная запись запуска от имени Azure не создается по умолчанию при создании учетной записи службы автоматизации Azure. Эту учетную запись можно создать отдельно, выполнив действий, описанные далее в этой статье.

## <a name="obtain-run-as-account-permissions"></a><a name="permissions"></a>Получение разрешений учетной записи запуска от имени

В этом разделе определены разрешения для обычной и классической учетных записей запуска от имени.

### <a name="get-permissions-to-configure-run-as-accounts"></a>Получение разрешений для настройки учетных записей запуска от имени

Чтобы создать или обновить учетную запись запуска от имени, необходимо иметь привилегии и разрешения. Администратор приложений в Azure Active Directory и владелец в подписке могут выполнить все эти задачи. В следующей таблице показаны списки задач, эквивалентный командлет и необходимые разрешения для ситуаций, когда применяется разделение обязанностей.

|Задача|Командлет  |Минимальные разрешения  |Где необходимо установить разрешения|
|---|---------|---------|---|
|Создание приложения Azure AD|[New-AzADApplication](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication)     | Роль разработчика приложения<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>"Главная" > "Azure AD" > "Регистрация приложений" |
|Добавление учетных данных приложения|[New-AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential)     | Администратор приложений или глобальный администратор<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>"Главная" > "Azure AD" > "Регистрация приложений"|
|Создание или получение субъекта-службы Azure AD|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal)     | Администратор приложений или глобальный администратор<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>"Главная" > "Azure AD" > "Регистрация приложений"|
|Назначение или получение роли RBAC для указанного субъекта|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Администратор доступа пользователей, владелец или пользователь со следующими разрешениями:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Подписка](../role-based-access-control/role-assignments-portal.md)</br>"Начальная страница > Подписки > \<имя подписки\> — Управление доступом (IAM)"|
|Создание или удаление сертификата службы автоматизации|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate)     | Участник группы ресурсов         |Группа ресурсов учетной записи службы автоматизации|
|Создание или удаление подключения службы автоматизации|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection)|Участник группы ресурсов |Группа ресурсов учетной записи службы автоматизации|

<sup>1</sup> Пользователи без прав администратора в арендаторе Azure AD могут [регистрировать приложения AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions), если в этом арендаторе для параметра **Пользователи могут регистрировать приложения** на странице параметров пользователя установлено значение **Да**. Если для этого параметра задано значение **Нет**, то пользователь, выполняющий данное действие, должен соответствовать требованиям в этой таблице.

Если вам назначается роль глобального администратора или соадминистратора подписки, но вы не являетесь участником экземпляра Active Directory подписки, то вы будете добавлены в качестве гостя. В этом случае отобразится предупреждение `You do not have permissions to create…` на странице добавления учетной записи службы автоматизации. 

Если вы являетесь участником экземпляра Active Directory подписки, когда вам назначается роль глобального администратора, на странице добавления учетной записи службы автоматизации также может отобразиться предупреждение `You do not have permissions to create…`. В этом случае можно запросить удаление своего пользователя из экземпляра Active Directory подписки, а затем запросить его повторное добавление, чтобы стать полным пользователем в Active Directory.

Вот как можно убедиться, что ситуация, создающая сообщение об ошибке, исправлена.

1. На портале Azure в области Azure Active Directory выберите **Пользователи и группы**. 
2. Выберите **Все пользователи**.
3. Выберите свое имя, а затем выберите **Профиль**. 
4. Убедитесь, что для атрибута **Тип пользователя** в профиле пользователя не задано значение **Гость**.

### <a name="get-permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Получение разрешений для настройки классических учетных записей запуска от имени

Чтобы настроить или обновить классические учетные записи запуска от имени, необходима роль соадминистратора на уровне подписки. Чтобы узнать больше о разрешениях классической подписки, ознакомьтесь с разделом [Классические администраторы подписок Azure](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-azure-portal"></a>Создание учетной записи запуска от имени на портале Azure

Выполните действия, с помощью которых на портале Azure можно обновить учетную запись службы автоматизации Azure. Учетные записи запуска от имени и классические учетные записи запуска от имени создаются отдельно. Если вам не нужно управлять классическими ресурсами, достаточно создать учетную запись запуска от имени Azure.

1. Войдите на портал Azure с помощью учетной записи, которая является участником роли администраторов подписки и соадминистратором подписки.
2. Найдите и выберите раздел **Учетные записи службы автоматизации**.
3. На странице учетных записей службы автоматизации выберите в списке нужную учетную запись.
4. В области слева выберите **Учетные записи запуска от имени** в разделе параметров учетной записи.
5. В зависимости от того, какую учетную запись необходимо создать, выберите **Учетная запись запуска от имени Azure** или **Классическая учетная запись запуска от имени Azure**. 
6. В зависимости от того, какая учетная запись вас интересует, выберите область **Add Azure Run As** (Добавление учетной записи запуска от имени Azure) или **Add Azure Classic Run As Account** (Добавление классической учетной записи запуска от имени Azure). Просмотрев общие сведения, щелкните **Создать**.
6. Ход создания учетной записи запуска от имени в Azure можно отслеживать в разделе **Уведомления** в меню. Кроме того, отображается баннер с уведомлением о создании учетной записи. Процесс создания может занять несколько минут.

## <a name="create-a-run-as-account-using-powershell"></a>Создание учетной записи запуска от имени с помощью PowerShell

В следующем списке приведены требования для создания учетной записи запуска от имени в PowerShell. Эти требования применяются к обоим типам учетных записей запуска от имени.

* Windows 10 или Windows Server 2016 с модулями Azure Resource Manager версии 3.4.1 и выше. Сценарий PowerShell не поддерживает более ранние версии Windows.
* Azure PowerShell 1.0 или более поздней версии. Сведения о выпуске PowerShell 1.0 см. в статье [Приступая к работе с командлетами Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Учетная запись службы автоматизации, указанная в качестве значений параметров `AutomationAccountName` и `ApplicationDisplayName`.
* Разрешения, аналогичные списку в разделе [разрешений для настройки учетной записи запуска от имени](#permissions).

Чтобы получить значения параметров `SubscriptionId`, `ResourceGroupName` и AutomationAccountName, которые являются обязательными для сценария PowerShell, выполните следующие действия.

1. На портале Azure выберите **Учетные записи службы автоматизации**.
1. На странице учетных записей службы автоматизации выберите нужную учетную запись.
1. В разделе параметров учетной записи выберите **Свойства**.
1. Запишите значения параметров **Имя**, **Идентификатор подписки** и **Группа ресурсов** на странице "Свойства". Эти значения соответствуют значениям параметров `AutomationAccountName`, `SubscriptionId` и `ResourceGroupName` в сценарии PowerShell соответственно.

   ![Страница свойств учетной записи службы автоматизации](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Сценарий PowerShell для создания учетной записи запуска от имени

В этом разделе приведен сценарий PowerShell для создания учетной записи запуска от имени. Этот сценарий поддерживает нескольких конфигураций.

* создание учетной записи запуска от имени с использованием самозаверяющего сертификата;
* создание учетной записи запуска от имени и классической учетной записи запуска от имени с использованием самозаверяющего сертификата;
* Создайте учетную запись запуска от имени и классическую учетную запись запуска от имени, используя сертификат, выданный центром сертификации предприятия.
* создание учетной записи запуска от имени и классической учетной записи запуска от имени с использованием самозаверяющего сертификата в облаке Azure для государственных организаций.

Для создания ресурсов сценарий использует несколько командлетов Azure Resource Manager. Сведения о командлетах и требуемых для них разрешениях см. в разделе [Получение разрешений для настройки учетных записей запуска от имени](#get-permissions-to-configure-run-as-accounts).

Сохраните сценарий на компьютере, указав имя файла **New-RunAsAccount.ps1**.

```powershell
#Requires -RunAsAdministrator
Param (
    [Parameter(Mandatory = $true)]
    [String] $ResourceGroup,

    [Parameter(Mandatory = $true)]
    [String] $AutomationAccountName,

    [Parameter(Mandatory = $true)]
    [String] $ApplicationDisplayName,

    [Parameter(Mandatory = $true)]
    [String] $SubscriptionId,

    [Parameter(Mandatory = $true)]
    [Boolean] $CreateClassicRunAsAccount,

    [Parameter(Mandatory = $true)]
    [String] $SelfSignedCertPlainPassword,

    [Parameter(Mandatory = $false)]
    [string] $EnterpriseCertPathForRunAsAccount,

    [Parameter(Mandatory = $false)]
    [String] $EnterpriseCertPlainPasswordForRunAsAccount,

    [Parameter(Mandatory = $false)]
    [String] $EnterpriseCertPathForClassicRunAsAccount,

    [Parameter(Mandatory = $false)]
    [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

    [Parameter(Mandatory = $false)]
    [ValidateSet("AzureCloud", "AzureUSGovernment")]
    [string]$EnvironmentName = "AzureCloud",

    [Parameter(Mandatory = $false)]
    [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
)

function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
    [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
    $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
        -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
        -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256

    $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
    Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
    Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
}

function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {
    $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
    $keyId = (New-Guid).Guid

    # Create an Azure AD application, AD App Credential, AD ServicePrincipal

    # Requires Application Developer Role, but works with Application administrator or GLOBAL ADMIN
    $Application = New-AzADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId)
    # Requires Application administrator or GLOBAL ADMIN
    $ApplicationCredential = New-AzADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
    # Requires Application administrator or GLOBAL ADMIN
    $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $Application.ApplicationId
    $GetServicePrincipal = Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id

    # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
    Sleep -s 15
    # Requires User Access Administrator or Owner.
    $NewRole = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
    $Retries = 0;
    While ($NewRole -eq $null -and $Retries -le 6) {
        Sleep -s 10
        New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
        $NewRole = Get-AzRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries++;
    }
    return $Application.ApplicationId.ToString();
}

function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
    $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force
    Remove-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
    New-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
}

function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
    Remove-AzAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
    New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
}

Import-Module AzureRm.Profile
Import-Module AzureRm.Resources

$AureRmProfileVersion = (Get-Module AzureRm.Profile).Version
if (!(($AzureRmProfileVersion.Major -ge 3 -and $AzureRmProfileVersion.Minor -ge 4) -or ($AzureRmProfileVersion.Major -gt 3))) {
    Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
    return
}

# To use the new Az modules to create your Run As accounts, please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation account see https://docs.microsoft.com/azure/automation/az-modules.

# Import-Module Az.Automation
# Enable-AzureRmAlias


Connect-AzAccount -Environment $EnvironmentName
$Subscription = Get-AzSubscription -SubscriptionId $SubscriptionId | Set-AzContext

# Create a Run As account by using a service principal
$CertifcateAssetName = "AzureRunAsCertificate"
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionTypeName = "AzureServicePrincipal"

if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
    $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
    $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
}
else {
    $CertificateName = $AutomationAccountName + $CertifcateAssetName
    $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
    $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
    $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
    CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
}

# Create a service principal
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
$ApplicationId = CreateServicePrincipal $PfxCert $ApplicationDisplayName

# Create the Automation certificate asset
CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

# Populate the ConnectionFieldValues
$SubscriptionInfo = Get-AzSubscription -SubscriptionId $SubscriptionId
$TenantID = $SubscriptionInfo | Select TenantId -First 1
$Thumbprint = $PfxCert.Thumbprint
$ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

# Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

if ($CreateClassicRunAsAccount) {
    # Create a Run As account by using a service principal
    $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
    $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
    $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
    $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
    "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
    "Then click Upload and upload the .cer format of #CERT#"

    if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
        $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
        $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
        $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
    }
    else {
        $ClassicRunAsAccountCertificateName = $AutomationAccountName + $ClassicRunAsAccountCertifcateAssetName
        $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
        $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
        $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
        $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
        CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
    }
    
    # Create the Automation certificate asset
    CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

    # Populate the ConnectionFieldValues
    $SubscriptionName = $subscription.Name
    $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues

    Write-Host -ForegroundColor red       $UploadMessage
}
```

>[!NOTE]
>`Add-AzAccount` и `Add-AzureRMAccount` являются псевдонимами [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Вы можете использовать эти командлеты или [обновить модули](automation-update-azure-modules.md) в учетной записи службы автоматизации до последних версий. Обновление модулей может потребоваться, даже если учетная запись службы автоматизации только что создана.

### <a name="execute-the-powershell-script"></a>Выполнение сценария PowerShell

1. На компьютере запустите с повышенными правами **Windows PowerShell** с **начального** экрана.
1. В оболочке командной строки с повышенными привилегиями перейдите в папку, которая содержит сценарий.
1. Выполните этот сценарий, указав значения параметров в зависимости от требуемой конфигурации.
1. Если вы создаете классическую учетную запись запуска от имени, то после выполнения сценария передайте открытый сертификат (файл в формате **CER**) в хранилище управления для подписки, в которой создана учетная запись службы автоматизации.

После выполнения сценария появится запрос на аутентификацию в Azure. Войдите в систему, используя учетную запись, которая является участником роли "Администраторы подписки" и соадминистратором подписки.

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>Создание учетной записи запуска от имени с использованием самозаверяющего сертификата

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate"></a>Создание учетной записи запуска от имени и классической учетной записи запуска от имени с использованием самозаверяющего сертификата

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-an-enterprise-certificate"></a>Создание учетной записи запуска от имени и классической учетной записи запуска от имени с использованием корпоративного сертификата

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
```

Если вы создали классическую учетную запись запуска от имени с использованием открытого корпоративного сертификата (**CER**-файл), используйте этот сертификат. Ознакомьтесь с разделом [Передача сертификата управления службами Azure](../azure-api-management-certs.md).

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>Создание учетной записи запуска от имени и классической учетной записи запуска от имени с использованием самозаверяющего сертификата в облаке Azure для государственных организаций.

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

Если вы создали классическую учетную запись запуска от имени с помощью самозаверяющего открытого сертификата (**CER**-файла), то сценарий создаст и сохранит его в папке временных файлов на компьютере. Его можно найти в профиля пользователя `%USERPROFILE%\AppData\Local\Temp`, который использовался для выполнения сеанса PowerShell.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Удаление учетной записи запуска от имени или классической учетной записи запуска от имени

В этом разделе описывается, как удалить учетную запись запуска от имени или классическую учетную запись запуска от имени. При выполнении этого действия учетная запись службы автоматизации сохраняется. После удаления учетной записи ее можно создать заново на портале Azure.

1. На портале Azure откройте учетную запись службы автоматизации.

2. В области слева выберите **Учетные записи запуска от имени** в разделе параметров учетной записи.

3. На странице свойств учетных записей запуска от имени выберите учетную запись запуска от имени или классическую учетную запись запуска от имени, которую нужно удалить. 

4. Затем в области "Свойства" выбранной учетной записи щелкните **Удалить**.

   ![Удаление учетной записи запуска от имени](media/manage-runas-account/automation-account-delete-runas.png)

5. Ход удаления учетной записи можно отслеживать в разделе **Уведомления** в меню.

6. После удаления учетной записи ее можно повторно создать на странице свойств учетных записей запуска от имени, выбрав параметр создания **Azure Run As Account** (Учетная запись запуска от имени Azure).

   ![Повторное создание учетной записи запуска от имени службы автоматизации](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Продление самозаверяющего сертификата

Срок действия самозаверяющего сертификата, созданного для учетной записи запуска от имени, составляет один год с даты создания. В определенный момент перед истечением срока действия учетной записи запуска от имени вам потребуется продлить сертификат. Его можно продлить в любое время до истечения его срока действия. 

При продлении самозаверяющего сертификата текущий действительный сертификат сохраняется, чтобы гарантировать, что все модули runbook, которые поставлены в очередь или активно выполняются и для аутентификации которых используется учетная запись запуска от имени, не пострадают. Сертификат будет существовать до окончания срока действия.

>[!NOTE]
>Если вы считаете, что учетная запись запуска от имени была скомпрометирована, то можете удалить самозаверяющий сертификат и создать его заново.

>[!NOTE]
>Если вы настроили учетную запись запуска от имени службы автоматизации для использования сертификата, выданного корпоративным центром сертификации, и используете параметр продления самозаверяющего сертификата, то этот корпоративный сертификат будет заменен самозаверяющим сертификатом.

Чтобы продлить самозаверяющий сертификат, выполните следующие действия.

1. На портале Azure откройте учетную запись службы автоматизации.

1. Выберите **Учетные записи запуска от имени** в разделе параметров учетной записи.

    ![Область свойств учетной записи службы автоматизации](media/manage-runas-account/automation-account-properties-pane.png)

1. На странице свойств учетных записей запуска от имени выберите обычную или классическую учетную запись запуска от имени, для которой нужно продлить сертификат.

1. В области свойств выбранной учетной записи щелкните **Продлить сертификат**.

    ![Обновление сертификата для учетной записи запуска от имени](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Ход обновления сертификата можно отслеживать в разделе **Уведомления** в меню.

## <a name="set-up-automatic-certificate-renewal-with-an-automation-runbook"></a><a name="auto-cert-renewal"></a>Настройка автоматического продления сертификатов с помощью runbook службы автоматизации

Для автоматического продления сертификатов можно использовать runbook службы автоматизации. Приведенный сценарий с сайта [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) обеспечивает эту функцию в учетной записи службы автоматизации.

>[!NOTE]
>Для выполнения этого сценария необходимы права глобального администратора или администратора компании в Azure AD.

Данный сценарий создает еженедельное расписание для продления сертификатов учетных записей запуска от имени. Он добавляет runbook **Update-AutomationRunAsCredential** в учетную запись службы автоматизации. Код runbook можно просмотреть на сайте GitHub в сценарии [Update-AutomationRunAsCredential.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1). При необходимости можно использовать код PowerShell в файле для продления сертификатов вручную.

Чтобы немедленно протестировать процесс продления, выполните следующие действия.

1. Измените runbook **Update-AutomationRunAsCredential** и добавьте знак комментария (#) в строку 122 перед командой **Exit(1)** .

   ```powershell
   #Exit(1)
   ```

2. Опубликуйте этот runbook.
3. Запустите runbook.
4. Проверьте успешность продления с помощью приведенного ниже кода.

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```
    Выходные данные:

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. После теста измените runbook и удалите знак комментария, добавленный на шаге 1.
6. Опубликуйте этот runbook.

## <a name="limit-run-as-account-permissions"></a>Ограничение разрешений учетной записи запуска от имени

Чтобы управлять доступом службы автоматизации к ресурсам в Azure, можно запустить сценарий [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8). Этот сценарий изменяет существующий субъект-службу учетной записи запуска от имени, чтобы создать и использовать пользовательское определение роли. Это роль с разрешениями для всех ресурсов, кроме [Key Vault](https://docs.microsoft.com/azure/key-vault/).

>[!IMPORTANT]
>После запуска сценария **Update-AutomationRunAsAccountRoleAssignments.ps1** модули runbook, которые обращаются к Key Vault с помощью учетных записей запуска от имени, больше не будут работать. Перед выполнением сценария необходимо проверить модули runbook в вашей учетной записи на наличие вызовов к Azure Key Vault. Чтобы разрешить доступ к Key Vault из модулей runbook службы автоматизации Azure, необходимо [добавить учетную запись запуска от имени в разрешения Key Vault](#add-permissions-to-key-vault).

Если необходимо ввести дополнительные ограничения, выходящие за рамки возможностей субъекта-службы запуска от имени, можно добавить другие типы ресурсов в элемент `NotActions` пользовательского определения роли. В следующем примере ограничивается доступ к `Microsoft.Compute/*`. Если вы добавите этот тип ресурса в `NotActions` в определении роли, данная роль не сможет предоставить доступ к вычислительному ресурсу. Чтобы узнать больше об определениях ролей, ознакомьтесь с разделом [Общие сведения об определениях ролей для ресурсов Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Определить, указан ли субъект-служба, используемый вашей учетной записью запуска от имени, в определении роли участника или пользовательском определении. 

1. Перейдите к учетной записи службы автоматизации и выберите **Учетные записи запуска от имени** в разделе параметров учетной записи.
2. Выберите **Azure Run As Account** (Учетная запись запуска от имени Azure). 
3. Выберите **Роль**, чтобы указать используемое определение роли.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Можно также задать определение роли, используемое учетными записями запуска от имени, для нескольких подписок или учетных записей автоматизации. Для этого используйте сценарий [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) из коллекции PowerShell.

### <a name="add-permissions-to-key-vault"></a>Добавление разрешений в Key Vault

Вы можете разрешить службе автоматизации Azure проверять, используют ли Key Vault и субъект-служба учетной записи запуска от имени пользовательское определение роли. Необходимо сделать следующее:

* Предоставьте разрешения для Key Vault.
* Настройте политику доступа.

Вы можете использовать сценарий [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) из коллекция PowerShell, чтобы предоставить учетной записи запуска от имени разрешения для Key Vault. Дополнительные сведения о настройке разрешений для Key Vault см. в разделе [Обеспечение проверки подлинности Azure Key Vault с помощью политики управления доступом](../key-vault/general/group-permissions-for-apps.md).

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Устранение проблем из-за неправильной конфигурации учетных записей запуска от имени

Во время начальной настройки некоторые из элементов конфигурации, необходимых для работы обычной или классической учетной записи запуска от имени, могут быть удалены или неправильно созданы. Возможные случаи неправильной конфигурации включают в себя:

* ресурс сертификата;
* ресурс подключения;
* удаление учетной записи запуска от имени из роли участника;
* субъект-службу или приложение в Azure AD.

В случае такой неправильной конфигурации учетная запись службы автоматизации обнаружит эти изменения и отобразит в области свойств учетных записей запуска от имени состояние *Не выполнено*.

![Сообщение о том, что настройка учетной записи запуска от имени не завершена](media/manage-runas-account/automation-account-runas-incomplete-config.png)

При выборе учетной записи запуска от имени в области свойств учетной записи отобразится приведенное ниже сообщение об ошибке.

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Эти проблемы с учетной записью запуска от имени можно быстро устранить, удалив и повторно создав ее.

## <a name="next-steps"></a>Дальнейшие действия

* [Объекты приложения и субъекта-службы](../active-directory/develop/app-objects-and-service-principals.md)
* [Общие сведения о сертификатах для Облачных служб Azure](../cloud-services/cloud-services-certs-create.md)
