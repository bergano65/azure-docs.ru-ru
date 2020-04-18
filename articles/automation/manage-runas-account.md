---
title: Управление учетными записями запуска от имени службы автоматизации Azure
description: В этой статье объясняется, как управлять учетными записями запуска от имени с помощью PowerShell или портала.
services: automation
ms.subservice: shared-capabilities
ms.date: 05/24/2019
ms.topic: conceptual
ms.openlocfilehash: 341db4ffa5b2e0641572f2c9dc011e91fac6a1e9
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617326"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Управление учетными записями запуска от имени службы автоматизации Azure

Выполнить как учетные записи в Azure Automation обеспечить аутентификацию для управления ресурсами в Azure с помощью cmdlets Azure. При создании учетной записи Run As он создает нового основного пользователя службы в active Directory (AD) и назначает роль автора этому пользователю на уровне подписки. Для модулей runbook, использующих гибридные рабочие роли Runbook на виртуальных машинах Azure, можно использовать [управляемые удостоверения для ресурсов Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) вместо учетной записи запуска от имени для аутентификации при получении доступа к ресурсам Azure.

Основной сервис для выполнения учетной записи не имеет разрешений на чтение Azure AD по умолчанию. Если вы хотите добавить разрешения для чтения или управления AD Azure, вам необходимо предоставить разрешения на основной службы под **разрешениями API.** Чтобы узнать больше, [см. Добавление разрешений для доступа к web-aIS.](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

## <a name="types-of-run-as-accounts"></a>Типы выполнения как учетные записи

Azure Automation использует два типа учетных записей:

* Azure Run Как учетная запись
* Azure Классический запуск как учетная запись

>[!NOTE]
>Подписки поставщика облачных решений Azure (CSP) поддерживают только модель управления ресурсами Azure. Службы менеджера ресурсов, не связанные с Azure, недоступны в программе. При использовании подписки CSP учетная запись Azure Classic Run As не создается, но создается учетная запись Azure Run As. Дополнительные сведения о подписках CSP см. в разделе [Доступные службы в подписках CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).

### <a name="run-as-account"></a>учетная запись запуска от имени

Учетная запись Run As управляет [ресурсами модели развертывания диспетчера ресурсов ресурсов ресурсов.](../azure-resource-manager/management/deployment-models.md) Он выполняет следующие задачи.

* Создается приложение Azure AD с самозаверяющим сертификатом, учетная запись субъекта-службы для этого приложения в Azure AD, а также назначается роль участника для учетной записи в текущей подписке. Вы можете изменить настройку сертификата на Владельца или любую другую роль. Для получения дополнительной информации смотрите [элемент управления доступом на основе ролей в Azure Automation.](automation-role-based-access-control.md)
  
* Создает сертификат автоматизации, указанный `AzureRunAsCertificate` в указанной учетной записи Automation. Актив сертификата содержит закрытый ключ сертификата, который использует приложение Azure AD.
  
* Создает актив подключения `AzureRunAsConnection` автоматизации, указанный в указанной учетной записи Automation. Актив подключения содержит идентификатор приложения, идентификатор клиента, идентификатор подписки и отпечаток пальца сертификата.

### <a name="azure-classic-run-as-account"></a>Классическая учетная запись запуска от имени Azure

Учетная запись Azure Classic Run As управляет ресурсами [модели развертывания Classic.](../azure-resource-manager/management/deployment-models.md) Вы должны быть соадминистратором по подписке для создания или обновления этого типа учетной записи.

Учетная запись Azure Classic Run As выполняет следующие задачи.

  * Создает сертификат управления в подписке.

  * Создает сертификат автоматизации, указанный `AzureClassicRunAsCertificate` в указанной учетной записи Automation. Этот ресурс содержит закрытый ключ сертификата, используемый в сертификате управления.

  * Создает актив подключения `AzureClassicRunAsConnection` автоматизации, указанный в указанной учетной записи Automation. Актив подключения содержит имя подписки, идентификатор подписки и имя актива сертификата.

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Запуск как разрешения учетной записи

Этот раздел определяет разрешения как для регулярных учетных записей Run As, так и для учетных записей Classic Run As.

### <a name="permissions-to-configure-run-as-accounts"></a>Разрешения для настройки учетных записей запуска от имени

Чтобы создать или обновить учетную запись запуска от имени, необходимо иметь привилегии и разрешения. Администратор приложений в Active Directory Azure и владелец в подписке могут выполнить все задачи. В ситуации, когда у вас есть разделение обязанностей, в следующей таблице показан список задач, эквивалентный cmdlet, и разрешения, необходимые:

|Задача|Командлет  |Минимальные разрешения  |Где необходимо установить разрешения|
|---|---------|---------|---|
|Создание приложения Azure AD|[Новое Азадприложение](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication?view=azps-3.5.0)     | Роль разработчика приложений<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Главная > Azure AD > Регистрация приложений |
|Добавление учетных данных приложения|[Нью-Азадаппcredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential?view=azps-3.5.0)     | Администратор приложений или Глобальный администратор<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Главная > Azure AD > Регистрация приложений|
|Создание и создание основного сервиса Azure AD|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-3.5.0)</br>[Получить-AzaDServiceдиректор](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal?view=azps-3.5.0)     | Администратор приложений или Глобальный администратор<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Главная > Azure AD > Регистрация приложений|
|Назначение или получение роли RBAC для указанного субъекта|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment?view=azps-3.5.0)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment?view=azps-3.5.0)      | Администратор пользовательского доступа или владелец, или иметь следующие разрешения:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Подписка](../role-based-access-control/role-assignments-portal.md)</br>"Начальная страница > Подписки > \<имя подписки\> — Управление доступом (IAM)"|
|Создание или удаление сертификата службы автоматизации|[Новый АзАвтоматизацияСертификат](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.5.0)</br>[Удалить-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate?view=azps-3.5.0)     | Автор в группе ресурсов         |Группа ресурсов учетных записей автоматизации|
|Создание или удаление подключения службы автоматизации|[Новое-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.5.0)</br>[Удалить-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection?view=azps-3.5.0)|Автор в группе ресурсов |Группа ресурсов учетных записей автоматизации|

<sup>1</sup> Неадминистраторные пользователи в вашем арендаторе Azure AD могут [зарегистрировать приложения AD,](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) если пользователи Azure AD могут зарегистрировать опцию **приложений** на странице настроек пользователя, настроенной на **Да.** Если настройка регистрации приложения **не**установлена, пользователь, выполняющий это действие, должен быть определен в этой таблице.

Если вы не являетесь участником экземпляра Active Directory, прежде чем быть добавлены к роли глобального администратора подписки, вы добавлены в качестве гостя. В этом случае вы получите предупреждение `You do not have permissions to create…` на странице добавления учетной записи службы автоматизации. 

Если вы являетесь участником экземпляра Active Directory подписки при присвоении роли `You do not have permissions to create…` Глобального администратора, вы также можете получить предупреждение на странице учетной записи Add Automation. В этом случае можно запросить удаление из экземпляра Active Directory подписки, а затем запросить повторное добавление, чтобы вы стали полноправным пользователем в Active Directory.

Чтобы убедиться, что ситуация, связанная с сообщением об ошибке, была исправлена:

1. Из панели Active Directory На портале Azure выберите **пользователей и группы.** 
2. Выберите **всех пользователей.**
3. Выберите имя, а затем выберите **профиль**. 
4. Убедитесь, что значение атрибута **типа пользователя** в профиле пользователя не устанавливается **для Guest.**

### <a name="permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Разрешения на настройку Классический Запуск Как счета

Для настройки или обновления учетных записей Classic Run As необходимо иметь роль соадминистратора на уровне подписки. Чтобы узнать больше о классических разрешениях подписки, смотрите [классические администраторы подписки Azure.](../role-based-access-control/classic-administrators.md#add-a-co-administrator)

## <a name="creating-a-run-as-account-in-azure-portal"></a>Создание учетной записи Run As на портале Azure

Выполните следующие действия по обновлению учетной записи Azure Automation на портале Azure. Создайте Run As и Classic Run As счета по отдельности. Если вам не нужно управлять классическими ресурсами, достаточно создать учетную запись запуска от имени Azure.

1. Войдите на портал Azure с помощью учетной записи, которая является участником роли администраторов подписки и соадминистратором подписки.
2. Поиск и выбор **учетных записей автоматизации.**
3. На странице учетных записей автоматизации выберите учетную запись Автоматизации из списка.
4. В левой панели выберите **«Выполнить как учетные записи»** в разделе настроек учетной записи.
5. В зависимости от того, какую учетную запись необходимо создать, выберите **Учетная запись запуска от имени Azure** или **Классическая учетная запись запуска от имени Azure**. 
6. В зависимости от учетной записи, используйте **добавить Azure Run as** или **добавить Azure Classic Run в качестве панели учетной записи.** После просмотра информации об обзоре нажмите **Создать**.
6. Ход создания учетной записи запуска от имени в Azure можно отслеживать в разделе **Уведомления** в меню. Также отображается баннер, в котором говорится, что учетная запись создается. Процесс может занять несколько минут.

## <a name="creating-a-run-as-account-using-powershell"></a>Создание учетной записи Run As с помощью PowerShell

В следующем списке указаны требования к созданию учетной записи Run As в PowerShell. Эти требования применяются к обоим типам учетных записей Run As.

* Windows 10 или Windows Server 2016 с модулями Azure Resource Manager версии 3.4.1 и выше. Скрипт PowerShell не поддерживает более ранние версии Windows.
* Azure PowerShell 1.0 или более поздней версии. Сведения о выпуске PowerShell 1.0 см. в статье [Приступая к работе с командлетами Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Учетная запись автоматизации, на которую `AutomationAccountName` `ApplicationDisplayName` ссылаются как на значение для параметров.
* Разрешения, эквивалентные разрешениям, перечисленным в [Требуемых разрешениях на настройку учетных записей Run As.](#permissions)

Чтобы получить значения `SubscriptionId`для, `ResourceGroupName`и , что требуется параметров для сценария PowerShell, завершить следующие шаги.

1. На портале Azure выберите **учетные записи автоматизации.**
1. На странице учетных записей автоматизации выберите учетную запись Автоматизации.
1. В разделе настройки учетной записи выберите **Свойства**.
1. Обратите внимание на значения **для NAME,** **SUBSCRIPTION ID**и **RESOURCE GROUP** на странице Properties. Эти значения соответствуют значениям `AutomationAccountName`для `SubscriptionId`параметров сценария , и `ResourceGroupName` PowerShell, соответственно.

   ![Страница свойств автоматизации](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Скрипт PowerShell для создания учетной записи Run As

В этом разделе приведен скрипт PowerShell для создания учетной записи Run As. Скрипт включает поддержку нескольких конфигураций.

* создание учетной записи запуска от имени с использованием самозаверяющего сертификата;
* создание учетной записи запуска от имени и классической учетной записи запуска от имени с использованием самозаверяющего сертификата;
* Создайте учетную запись запуска от имени и классическую учетную запись запуска от имени, используя сертификат, выданный центром сертификации предприятия.
* создание учетной записи запуска от имени и классической учетной записи запуска от имени с использованием самозаверяющего сертификата в облаке Azure для государственных организаций.

Для создания ресурсов сценарий использует несколько командлетов Azure Resource Manager. Для cmdlets и разрешений, которые им требуются, [см. Разрешения на настройку учетных записей Run As.](#permissions-to-configure-run-as-accounts)

Сохранить скрипт на компьютере с помощью файла **New-RunAsAccount.ps1**.

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
$Subscription = Get-AzSubscription -SubscriptionId $SubscriptionId

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
    $SubscriptionName = $subscription.Subscription.Name
    $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues

    Write-Host -ForegroundColor red       $UploadMessage
}
```

>[!NOTE]
>`Add-AzAccount`и `Add-AzureRMAccount` являются псевдонимами для [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Вы можете использовать эти cmdlets или вы можете [обновить свои модули](automation-update-azure-modules.md) в вашей учетной записи автоматизации до последних версий. Возможно, потребуется обновить модули, даже если вы только что создали новую учетную запись Automation.

### <a name="execute-the-powershell-script"></a>Выполнение сценария PowerShell

1. На компьютере запустите с повышенными правами **Windows PowerShell** с **начального** экрана.
1. Из оболочки повышенной строки перейдите в папку, содержащую ваш сценарий.
1. Выполните сценарий, используя значения параметров для требуемой конфигурации.
1. При создании учетной записи Classic Run As после выполнения сценария загрузите общедоступный сертификат **(расширение файла .cer)** в магазин управления для подписки, в которой была создана учетная запись Automation.

После выполнения сценария вам будет предложено проверить подлинность с помощью Azure. Восвай с учетной записью, которая является членом роли администраторов подписки и соадминистратором подписки.

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>Создание учетной записи Run As с помощью сертификата, подписанного самостоятельно

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate"></a>Создание учетной записи запуска от имени и классической учетной записи запуска от имени с использованием самозаверяющего сертификата

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-an-enterprise-certificate"></a>Создание учетной записи запуска от имени и классической учетной записи запуска от имени Azure с использованием корпоративного сертификата

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
```

Если вы создали учетную запись Classic Run As с корпоративным публичным сертификатом (файл **.cer),** используйте этот сертификат. Просмотреть [загрузку сертификата API управления на портал Azure](../azure-api-management-certs.md).

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>Создание учетной записи запуска от имени и классической учетной записи запуска от имени с использованием самозаверяющего сертификата в облаке Azure для государственных организаций

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

Если вы создали учетную запись Classic Run As с самоподписанным публичным сертификатом **(файл .cer),** скрипт создает и сохраняет его в папке временных файлов на вашем компьютере. Его можно найти в `%USERPROFILE%\AppData\Local\Temp`профиле пользователя, который вы использовали для выполнения сеанса PowerShell.

## <a name="deleting-a-run-as-or-classic-run-as-account"></a>Удалять run As или Classic Run As account

В этом разделе описывается, как удалить run As или Classic Run As аккаунт. При выполнении этого действия учетная запись службы автоматизации сохраняется. После удаления учетной записи можно воссоздать ее на портале Azure.

1. На портале Azure откройте учетную запись службы автоматизации.

2. В левой панели выберите **«Выполнить как учетные записи»** в разделе настроек учетной записи.

3. На странице свойств Run As Accounts (Учетные записи запуска от имени) выберите учетную запись запуска от имени или классическую учетную запись запуска от имени, которую нужно удалить. 

4. На панели свойств для выбранной учетной записи нажмите **«Удалить**».

   ![Удаление учетной записи запуска от имени](media/manage-runas-account/automation-account-delete-runas.png)

5. Ход удаления учетной записи можно отслеживать в разделе **Уведомления** в меню.

6. После удаления учетной записи ее можно повторно создать на странице свойств Run As Accounts (Учетные записи запуска от имени), выбрав параметр создания **Azure Run As Account** (Учетная запись запуска от имени Azure).

   ![Повторное создание учетной записи запуска от имени службы автоматизации](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renewing-a-self-signed-certificate"></a><a name="cert-renewal"></a>Обновление сертификата, подписанного самостоятельно

Сертификат, созданный для учетной записи Run As, истекает через год с даты создания. В какой-то момент до истечения срока действия учетной записи Run As необходимо обновить сертификат. Вы можете продлить его в любое время до истечения срока его действия. 

При обновлении сертификата, подписанного самостоятельно, текущий действительный сертификат сохраняется, чтобы гарантировать, что все запущенные в очередь или активно запущенные, и что аутентификация с учетной записью Run As не будет негативно затронута. Сертификат будет существовать до окончания срока действия.

>[!NOTE]
>Если вы считаете, что учетная запись Run As была скомпрометирована, вы можете удалить и воссоздать сертификат, подписанный самостоятельно.

>[!NOTE]
>Если вы настроили учетную запись Run As для использования сертификата, выданного вашим органом сертификата предприятия, и вы используете опцию продления опции сертификата, подписанной самостоятельной, сертификат предприятия заменяется сертификатом, подписанным самостоятельно.

Используйте следующие шаги для продления сертификата, подписанного самостоятельно.

1. На портале Azure откройте учетную запись службы автоматизации.

1. Выберите **«Выполнить как учетные записи»** в разделе настроек учетной записи.

    ![Область свойств учетной записи службы автоматизации](media/manage-runas-account/automation-account-properties-pane.png)

1. На странице свойств Run As accounts выберите либо учетную запись Run As, либо учетную запись Classic Run As, для которой можно обновить сертификат.

1. На панели свойств для выбранной учетной записи нажмите **сертификат Обновления.**

    ![Обновление сертификата для учетной записи запуска от имени](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Ход обновления сертификата можно отслеживать в разделе **Уведомления** в меню.

## <a name="setting-up-automatic-certificate-renewal-with-an-automation-runbook"></a><a name="auto-cert-renewal"></a>Настройка автоматического обновления сертификата с помощью автоматической запущенной книги

Чтобы автоматически обновлять сертификаты, можно использовать запуск автоматической автоматизации. Этот скрипт на [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) позволяет эту функциональность в вашей учетной записи Автоматизация.

>[!NOTE]
>Для выполнения сценария вы должны быть глобальным администратором или администратором компании в Azure AD.

Этот скрипт создает еженедельное расписание для обновления сертификатов учетной записи Run As. Он добавляет в свою учетную запись **Update-AutomationRunAsCredential.** Вы можете просмотреть код runbook на GitHub, в скрипте [Update-AutomationRunAsCredential.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1). Вы можете использовать код PowerShell в файле для обновления сертификатов вручную, по мере необходимости.

Используйте следующие шаги, чтобы проверить процесс обновления немедленно.

1. Отойдите в runbook **Update-AutomationAsCredential** и разместите символ комментария (кв. м) на строке 122 перед командой **Exit (1).**

   ```powershell
   #Exit(1)
   ```

2. Опубликуйте книгу.
3. Запустите модуль runbook.
4. Проверить успешное обновление со следующим кодом:

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```
    Выходные данные:

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. После теста отодевайте книгу и удалите символ комментария, добавленный в шаге 1.
6. Опубликуйте книгу.

## <a name="limiting-run-as-account-permissions"></a><a name="limiting-run-as-account-permissions"></a>Ограничение разрешений учетной записи запуска от имени

Чтобы контролировать таргетинг автоматизации против ресурсов в Azure, можно запустить скрипт [Update-AutomationAsAccountRoleAssignments.ps1.](https://aka.ms/AA5hug8) Этот скрипт изменяет существующий принцип службы run As as для создания и использования пользовательского определения ролей. Роль имеет разрешения для всех ресурсов, кроме [Key Vault.](https://docs.microsoft.com/azure/key-vault/)

>[!IMPORTANT]
>После запуска сценария **Update-AutomationAsAccountRoleAssignments.ps1,** запущенные книги, которые получают доступ к Key Vault с помощью Run As, учетные записи больше не работают. Перед запуском скрипта следует просмотреть книги в учетной записи для звонков в Azure Key Vault. Чтобы включить доступ к Key Vault из runbooks Azure Automation, необходимо [добавить учетную запись Run As к разрешениям Key Vault.](#add-permissions-to-key-vault)

Если вам нужно ограничить, далее то, что может сделать директор `NotActions` службы Run As, можно добавить другие типы ресурсов к элементу пользовательского определения роли. Следующий пример ограничивает `Microsoft.Compute/*`доступ к . Если вы добавите `NotActions` этот тип ресурса к определению роли, роль не сможет получить доступ к любому ресурсу Compute. Чтобы узнать больше об определениях ролей, [см.](../role-based-access-control/role-definitions.md)

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Вы можете определить, является ли основной сервис, используемый вашей учетной записью Run As, определениеролиролей вкладчика или пользовательский. 

1. Перейдите на учетную запись Automation и выберите **учетные записи Run As** в разделе настроек учетной записи.
2. Выберите **Azure Run в качестве учетной записи.** 
3. Выберите **роль,** чтобы найти используемое определение роли.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Можно также определить определение роли, используемое в run As счетах для нескольких подписок или учетных записей автоматизации. Сделайте это с помощью [скрипта Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) в галерее PowerShell.

### <a name="add-permissions-to-key-vault"></a>Добавление разрешений в Key Vault

Вы можете разрешить Azure Automation проверить, используют ли Key Vault и ваш директор службы run As, используя пользовательское определение роли. Необходимо сделать следующее:

* Предоставлять разрешения Key Vault.
* Установите политику доступа.

Вы можете использовать скрипт [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) в галерее PowerShell, чтобы дать разрешение на выполнение в качестве учетной записи Key Vault. Для получения более подробной информации о настройке разрешений в Key Vault можно получить [доступ к приложениям Grant к хранилищу ключей.](../key-vault/general/group-permissions-for-apps.md)

## <a name="resolving-misconfiguration-issues-for-run-as-accounts"></a>Устранение проблем неправильной настройки для учетных записей Run As

Некоторые элементы конфигурации, необходимые для Run As или Classic Run As, могли быть удалены или созданы неправильно во время первоначальной настройки. Возможные случаи неправильной настройки включают:

* ресурс сертификата,
* ресурс подключения,
* Выполнить Как учетная запись удалена из роли вкладчика
* субъект-служба или приложение-служба в Azure AD,

Для таких случаев неправильной настройки учетная запись Automation `Incomplete` обнаруживает изменения и отображает состояние на панели свойств Run As для панели свойств учетных записей.

![Сообщение о том, что настройка учетной записи запуска от имени не завершена](media/manage-runas-account/automation-account-runas-incomplete-config.png)

При выборе учетной записи Run As панель свойств учетной записи отображает следующее сообщение об ошибке:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Эти проблемы с учетной записью запуска от имени можно быстро устранить, удалив и повторно создав ее.

## <a name="next-steps"></a>Дальнейшие действия

* Для получения дополнительной информации [Application Objects and Service Principal Objects](../active-directory/develop/app-objects-and-service-principals.md)о принципах обслуживания см.
* Дополнительные сведения о сертификатах и службах Azure см. в статье [Общие сведения о сертификатах для облачных служб Azure](../cloud-services/cloud-services-certs-create.md).
