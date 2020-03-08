---
title: Управление учетными записями запуска от имени службы автоматизации Azure
description: В этой статье объясняется, как управлять учетными записями запуска от имени с помощью PowerShell или портала.
services: automation
ms.subservice: shared-capabilities
ms.date: 05/24/2019
ms.topic: conceptual
ms.openlocfilehash: f0ba7a9f196379aa16d9b652e8b1f33df6118c6e
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78893250"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Управление учетными записями запуска от имени службы автоматизации Azure

Учетные записи запуска от имени в службе автоматизации Azure обеспечивают проверку подлинности для управления ресурсами в Azure с помощью командлетов Azure. При создании учетной записи запуска от имени создается новый пользователь субъекта-службы в Azure Active Directory (AD) и назначается роль участника этому пользователю на уровне подписки. Для модулей runbook, использующих гибридные рабочие роли Runbook на виртуальных машинах Azure, можно использовать [управляемые удостоверения для ресурсов Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) вместо учетной записи запуска от имени для аутентификации при получении доступа к ресурсам Azure.

Субъект-служба для учетной записи запуска от имени не имеет разрешений на чтение Azure AD по умолчанию. Если вы хотите добавить разрешения для чтения или управления Azure AD, необходимо предоставить разрешения на субъект-службу в разделе **разрешения API**. Дополнительные сведения см. в разделе [Добавление разрешений для доступа к веб-API](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке AZ Module в гибридной рабочей роли Runbook см. в статье [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Для учетной записи службы автоматизации можно обновить модули до последней версии, используя [обновление модулей Azure PowerShell в службе автоматизации Azure](automation-update-azure-modules.md).

## <a name="types-of-run-as-accounts"></a>Типы учетных записей запуска от имени

Служба автоматизации Azure использует два типа учетных записей запуска от имени:

* Учетная запись запуска от имени Azure
* Классическая учетная запись запуска от имени Azure

>[!NOTE]
>Подписки поставщика облачных решений Azure (CSP) поддерживают только модель Azure Resource Manager. Службы, не относящиеся к Azure Resource Manager, недоступны в программе. При использовании подписки CSP Классическая учетная запись запуска от имени Azure не создается, но создается учетная запись запуска от имени Azure. Дополнительные сведения о подписках CSP см. в разделе [Доступные службы в подписках CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).

### <a name="run-as-account"></a>учетная запись запуска от имени

Учетная запись запуска от имени управляет ресурсами [модели развертывания Диспетчер ресурсов](../azure-resource-manager/management/deployment-models.md) . Она выполняет следующие задачи.

* Создается приложение Azure AD с самозаверяющим сертификатом, учетная запись субъекта-службы для этого приложения в Azure AD, а также назначается роль участника для учетной записи в текущей подписке. Вы можете изменить параметр сертификата на "владелец" или любую другую роль. Дополнительные сведения см. в статье [Управление доступом на основе ролей в службе автоматизации Azure](automation-role-based-access-control.md).
  
* Ресурс сертификатов службы автоматизации с именем **AzureRunAsCertificate** в указанной учетной записи службы автоматизации. Ресурс сертификата содержит закрытый ключ сертификата, используемый приложением Azure AD.
  
* Ресурс подключений службы автоматизации с именем **AzureRunAsConnection** в указанной учетной записи службы автоматизации. Ресурс подключения содержит идентификатор приложения, идентификатор клиента, идентификатор подписки и отпечаток сертификата.

### <a name="azure-classic-run-as-account"></a>Классическая учетная запись запуска от имени Azure

Классическая учетная запись запуска от имени Azure управляет ресурсами [классической модели развертывания](../azure-resource-manager/management/deployment-models.md) . Для создания или обновления учетной записи этого типа необходимо быть соадминистратором подписки.

Классическая учетная запись запуска от имени Azure выполняет следующие задачи.

  * Создает сертификат управления в подписке.

  * Ресурс сертификатов службы автоматизации с именем **AzureClassicRunAsCertificate** в указанной учетной записи службы автоматизации. Этот ресурс содержит закрытый ключ сертификата, используемый в сертификате управления.

  * Ресурс подключений службы автоматизации с именем **AzureClassicRunAsConnection** в указанной учетной записи службы автоматизации. Ресурс подключения содержит имя подписки, идентификатор подписки и имя ресурса сертификата.

## <a name="permissions"></a>Разрешения учетной записи запуска от имени

В этом разделе определяются разрешения для обычных учетных записей запуска от имени и классической учетной записи запуска от имени.

### <a name="permissions-to-configure-run-as-accounts"></a>Разрешения для настройки учетных записей запуска от имени

Чтобы создать или обновить учетную запись запуска от имени, необходимо иметь привилегии и разрешения. Администратор приложения в Azure Active Directory и владелец в подписке могут выполнять все задачи. В следующей таблице показаны списки задач, эквивалентный командлет и необходимые разрешения для ситуаций, когда присутствует разделение обязанностей:

|Задача|Командлет  |Минимальные разрешения  |Где необходимо установить разрешения|
|---|---------|---------|---|
|Создание приложения Azure AD|[New-Азадаппликатион](/https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication?view=azps-3.5.0)     | Роль разработчика<sup>1</sup> для приложения        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Главная > Регистрация приложений > Azure AD |
|Добавление учетных данных приложения|[New-Азадаппкредентиал](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential?view=azps-3.5.0)     | Администратор приложения или глобальный администратор<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Главная > Регистрация приложений > Azure AD|
|Создание и получение субъекта-службы Azure AD|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-3.5.0)</br>[Get-АзадсервицепринЦипал](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal?view=azps-3.5.0)     | Администратор приложения или глобальный администратор<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Главная > Регистрация приложений > Azure AD|
|Назначение или получение роли RBAC для указанного субъекта|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment?view=azps-3.5.0)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment?view=azps-3.5.0)      | Администратор доступа пользователей или владелец или иметь следующие разрешения:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [подписка](../role-based-access-control/role-assignments-portal.md)</br>"Начальная страница > Подписки > \<имя подписки\> — Управление доступом (IAM)"|
|Создание или удаление сертификата службы автоматизации|[New-Азаутоматионцертификате](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.5.0)</br>[Remove-Азаутоматионцертификате](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate?view=azps-3.5.0)     | Участник группы ресурсов         |Группа ресурсов учетной записи службы автоматизации|
|Создание или удаление подключения службы автоматизации|[New-Азаутоматионконнектион](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.5.0)</br>[Remove-Азаутоматионконнектион](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection?view=azps-3.5.0)|Участник группы ресурсов |Группа ресурсов учетной записи службы автоматизации|

<sup>1</sup> пользователи без прав администратора в клиенте Azure AD могут [регистрировать приложения AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) , если параметр пользователи клиента Azure AD **может зарегистрировать приложения** на странице Параметры пользователя имеет значение **Да**. Если параметр регистрации приложения имеет значение **нет**, пользователь, выполняющий это действие, должен быть определен в этой таблице.

Если вы не являетесь членом Active Directory экземпляра подписки, прежде чем добавить роль глобального администратора в подписке, вы будете добавлены в качестве гостя. В этом случае вы получаете сообщение о том, что у **вас нет разрешений на создание...** предупреждение на странице Добавление учетной записи службы автоматизации. 

Если вы являетесь членом Active Directory экземпляра подписки при назначении роли глобального администратора, вы также можете получить, у **вас нет разрешений на создание...** предупреждение на странице Добавление учетной записи службы автоматизации. В этом случае можно запросить удаление из экземпляра Active Directory подписки, а затем запросить повторное добавление, чтобы стать полным пользователем в Active Directory. 

Чтобы убедиться, что ситуация, создающая сообщение об ошибке, была исправленоа:

1. В области Azure Active Directory в портал Azure выберите **Пользователи и группы**. 
2. Выберите **Все пользователи**.
3. Выберите свое имя, а затем щелкните **профиль**. 
4. Убедитесь, что значение атрибута **Тип пользователя** в профиле пользователя не задано как **гость**.

### <a name="permissions-classic"></a>Разрешения для настройки классической учетной записи запуска от имени

Чтобы настроить или обновить Классические учетные записи запуска от имени, необходимо иметь роль соадминистратора на уровне подписки. Дополнительные сведения о классических разрешениях подписки см. в статье [Администраторы классической подписки Azure](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="creating-a-run-as-account-in-azure-portal"></a>Создание учетной записи запуска от имени в портал Azure

Выполните следующие действия, чтобы обновить учетную запись службы автоматизации Azure в портал Azure. Учетные записи запуска от имени и классические запуска от имени необходимо создавать отдельно. Если вам не нужно управлять классическими ресурсами, достаточно создать учетную запись запуска от имени Azure.

1. Войдите на портал Azure с помощью учетной записи, которая является участником роли администраторов подписки и соадминистратором подписки.
2. Найдите и выберите **учетные записи службы автоматизации**.
3. На странице учетные записи службы автоматизации выберите учетную запись службы автоматизации из списка учетных записей службы автоматизации.
4. В левой области выберите **учетные записи запуска от имени** в разделе Параметры учетной записи.
5. В зависимости от того, какую учетную запись необходимо создать, выберите **Учетная запись запуска от имени Azure** или **Классическая учетная запись запуска от имени Azure**. 
6. В зависимости от интересующей учетной записи используйте панель добавить классическую учетную запись запуска от имени Azure или добавить классический. Просмотрев общие сведения, нажмите кнопку **создать** , чтобы продолжить создание учетной записи запуска от имени.
6. Ход создания учетной записи запуска от имени в Azure можно отслеживать в разделе **Уведомления** в меню. Также отображается баннер, в котором говорится, что создается учетная запись. Процесс может занять несколько минут.

## <a name="creating-a-run-as-account-using-powershell"></a>Создание учетной записи запуска от имени с помощью PowerShell

В следующем списке приведены требования для создания учетной записи запуска от имени в PowerShell. Эти требования применяются к обоим типам учетных записей запуска от имени.

* Windows 10 или Windows Server 2016 с модулями Azure Resource Manager версии 3.4.1 и выше. Скрипт PowerShell не поддерживает более ранние версии Windows.
* Azure PowerShell 1.0 или более поздней версии. Сведения о выпуске PowerShell 1.0 см. в статье [Приступая к работе с командлетами Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Учетная запись службы автоматизации, на которую ссылаются как на значения параметров *AutomationAccountName* и *ApplicationDisplayName* .
* Разрешения, аналогичные указанным в списке [необходимые разрешения для настройки учетных записей запуска от имени](#permissions).

Чтобы получить значения для *SubscriptionId*, *ResourceGroupName*и *AutomationAccountName*, которые являются обязательными параметрами для скрипта PowerShell, выполните следующие действия.

1. В портал Azure выберите **учетные записи службы автоматизации**.
1. На странице учетные записи службы автоматизации выберите свою учетную запись службы автоматизации.
1. В разделе Параметры учетной записи выберите **Свойства**.
1. Обратите внимание на значения параметров **имя**, **идентификатор подписки**и **Группа ресурсов** на странице Свойства. Эти значения соответствуют значениям для параметров сценария *AutomationAccountName*, *SubscriptionId*и *ResourceGroupName* PowerShell соответственно.

   ![Страница свойств учетной записи службы автоматизации](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Сценарий PowerShell для создания учетной записи запуска от имени

В этом разделе содержится сценарий PowerShell для создания учетной записи запуска от имени. Скрипт включает поддержку нескольких конфигураций.

* создание учетной записи запуска от имени с использованием самозаверяющего сертификата;
* создание учетной записи запуска от имени и классической учетной записи запуска от имени с использованием самозаверяющего сертификата;
* Создайте учетную запись запуска от имени и классическую учетную запись запуска от имени, используя сертификат, выданный центром сертификации предприятия.
* создание учетной записи запуска от имени и классической учетной записи запуска от имени с использованием самозаверяющего сертификата в облаке Azure для государственных организаций.

Для создания ресурсов сценарий использует несколько командлетов Azure Resource Manager. Сведения о командлетах и требуемых им разрешениях см. [в разделе разрешения на настройку учетных записей запуска от имени](#permissions-to-configure-run-as-accounts).

Сохраните сценарий на компьютере, используя имя файла **Нев-рунасаккаунт. ps1**.

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
>**Add-азаккаунт** и **Add-AzureRMAccount** являются псевдонимами для [Connect-азаккаунт](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Вы можете использовать эти командлеты или [обновить модули](automation-update-azure-modules.md) в учетной записи службы автоматизации до последних версий. Вам может потребоваться обновить модули, даже если вы только что создали новую учетную запись службы автоматизации.

### <a name="execute-the-powershell-script"></a>Выполнение скрипта PowerShell

1. На компьютере запустите с повышенными правами **Windows PowerShell** с **начального** экрана.
1. Из оболочки командной строки с повышенными привилегиями перейдите в папку, которая содержит сценарий, созданный на этапе 1.
1. Выполните этот сценарий, установив значения параметров в зависимости от требуемой конфигурации.
1. При создании классической учетной записи запуска от имени после выполнения скрипта передайте общедоступный сертификат (расширение CER) в хранилище управления для подписки, в которой была создана учетная запись службы автоматизации.

После выполнения сценария появится запрос на проверку подлинности в Azure. Войдите в систему, используя учетную запись, которая является участником роли "Администраторы подписки" и соадминистратором подписки.

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>Создание учетной записи запуска от имени с помощью самозаверяющего сертификата

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate"></a>Создание учетной записи запуска от имени и классической учетной записи запуска от имени с помощью самозаверяющего сертификата

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-an-enterprise-certificate"></a>Создание учетной записи запуска от имени и классической учетной записи запуска от имени с помощью корпоративного сертификата

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
```

Если вы создали классическую учетную запись запуска от имени с открытым общедоступным сертификатом предприятия (CER-файл), используйте этот сертификат. Следуйте инструкциям для [загрузки сертификата API управления на портал Azure](../azure-api-management-certs.md).

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>Создание учетной записи запуска от имени и классической учетной записи запуска от имени с помощью самозаверяющего сертификата в облаке Azure для государственных организаций

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

Если вы создали классическую учетную запись запуска от имени с самозаверяющим открытым сертификатом (CER-файл), скрипт создаст и сохранит его в папке временных файлов на компьютере. Его можно найти в профиле пользователя **appdata\local\temp**, который использовался для выполнения сеанса PowerShell.

## <a name="deleting-a-run-as-or-classic-run-as-account"></a>Удаление запуска от имени или классической учетной записи запуска от имени

В этом разделе описано, как удалить учетную запись запуска от имени или классическую. При выполнении этого действия учетная запись службы автоматизации сохраняется. После удаления учетной записи ее можно создать повторно в портал Azure.

1. На портале Azure откройте учетную запись службы автоматизации.

2. В левой области выберите **учетные записи запуска от имени** в разделе Параметры учетной записи.

3. На странице свойства учетные записи запуска от имени выберите учетную запись запуска от имени или классическую учетную запись запуска от имени, которую требуется удалить. 

4. На панели свойства выбранной учетной записи нажмите кнопку **Удалить**.

   ![Удаление учетной записи запуска от имени](media/manage-runas-account/automation-account-delete-runas.png)

5. Ход удаления учетной записи можно отслеживать в разделе **Уведомления** в меню.

6. После удаления учетной записи ее можно создать повторно на странице свойств учетные записи запуска от имени, выбрав параметр создать **учетную запись запуска от имени Azure**.

   ![Повторное создание учетной записи запуска от имени службы автоматизации](media/manage-runas-account/automation-account-create-runas.png)

## <a name="cert-renewal"></a>Продление срока действия самозаверяющего сертификата

Срок действия самозаверяющего сертификата, созданного для учетной записи запуска от имени, истекает один год с даты создания. В некоторый момент до истечения срока действия учетной записи запуска от имени необходимо продлить сертификат. Его можно обновить в любое время до истечения его срока действия. 

При продлении самозаверяющего сертификата текущий действительный сертификат сохраняется, чтобы гарантировать, что все модули Runbook, находящиеся в очереди или активно запущенные, и которые проходят проверку подлинности с учетной записью запуска от имени, не будут затронуты негативно. Сертификат будет существовать до окончания срока действия.

>[!NOTE]
>Если вы считаете, что учетная запись запуска от имени была скомпрометирована, можно удалить и повторно создать самозаверяющий сертификат.

>[!NOTE]
>Если учетная запись запуска от имени настроена на использование сертификата, выданного центром сертификации предприятия, и вы используете параметр для продления самозаверяющего сертификата, сертификат предприятия заменяется самозаверяющим сертификатом.

Чтобы продлить самозаверяющий сертификат, выполните следующие действия.

1. На портале Azure откройте учетную запись службы автоматизации.

1. Выберите **учетные записи запуска от имени** в разделе Параметры учетной записи.

    ![Область свойств учетной записи службы автоматизации](media/manage-runas-account/automation-account-properties-pane.png)

1. На странице свойства учетные записи запуска от имени выберите учетную запись запуска от имени или классическую учетную запись запуска от имени, для которой необходимо продлить сертификат.

1. На панели свойства выбранной учетной записи нажмите кнопку **Обновить сертификат**.

    ![Обновление сертификата для учетной записи запуска от имени](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Ход обновления сертификата можно отслеживать в разделе **Уведомления** в меню.

## <a name="auto-cert-renewal"></a>Настройка автоматического продления сертификатов с помощью модуля Runbook службы автоматизации

Для автоматического обновления сертификатов можно использовать модуль Runbook службы автоматизации. Этот скрипт в [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) включает эту функцию в учетной записи службы автоматизации.

>[!NOTE]
>Для выполнения скрипта необходимо быть глобальным администратором или администратором компании в Azure AD.

Этот скрипт создает еженедельное расписание для продления сертификатов учетных записей запуска от имени. Он добавляет Runbook **Update-аутоматионрунаскредентиал** в учетную запись службы автоматизации. Код модуля Runbook можно просмотреть на сайте GitHub в скрипте [упдате-аутоматионрунаскредентиал. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1). При необходимости можно использовать код PowerShell в файле для обновления сертификатов вручную.

Чтобы немедленно протестировать процесс обновления, выполните следующие действия.

1. Измените модуль Runbook **Update-аутоматионрунаскредентиал** и поместите символ комментария (#) в строке 122 перед командой **Exit (1)** .

   ```powershell
   #Exit(1)
   ```

2. Опубликуйте модуль Runbook.
3. Запустите модуль Runbook.
4. Проверьте успешность обновления с помощью следующего кода:

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```
    Выходные данные.

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. После теста измените модуль Runbook и удалите символ комментария, добавленный на шаге 1.
6. Опубликуйте модуль Runbook.

## <a name="limiting-run-as-account-permissions"></a>Ограничение разрешений учетной записи запуска от имени

Для управления нацеливанием автоматизации на ресурсы в Azure можно запустить сценарий [упдате-аутоматионрунасаккаунтролеассигнментс. ps1](https://aka.ms/AA5hug8) . Этот скрипт изменяет существующий субъект-службу учетной записи запуска от имени для создания и использования определения пользовательской роли. Роль имеет разрешения для всех ресурсов, кроме [Key Vault](https://docs.microsoft.com/azure/key-vault/).

>[!IMPORTANT]
>После запуска скрипта **упдате-аутоматионрунасаккаунтролеассигнментс. ps1** модули Runbook, которые обращаются к Key Vault с помощью учетных записей запуска от имени, больше не работают. Перед выполнением скрипта необходимо проверить модули Runbook в вашей учетной записи на наличие вызовов к Azure Key Vault. Чтобы обеспечить доступ к Key Vault из модулей Runbook службы автоматизации Azure, необходимо [Добавить учетную запись запуска от имени к разрешениям Key Vault](#add-permissions-to-key-vault).

Если необходимо дополнительно ограничить возможности запуска в качестве субъекта-службы, можно добавить другие типы ресурсов в элемент " **без изменений** " определения пользовательской роли. В следующем примере доступ к `Microsoft.Compute/*`ограничен. Если добавить этот тип ресурса в **неизменные** для определения роли, роль не сможет получить доступ к ресурсам вычислений. Дополнительные сведения об определениях ролей см. в статье Общие сведения о [определениях ролей для ресурсов Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Определить, используется ли субъект-служба, используемый вашей учетной записью запуска от имени, можно в определении роли участника или в другой. Для этого выполните следующее действие.

1. Перейдите к учетной записи службы автоматизации и выберите **учетные записи запуска от имени** в разделе "Параметры учетной записи".
2. Выберите **учетную запись запуска от имени Azure**. 
3. Выберите **роль** , чтобы определить используемое определение роли.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Можно также определить определение роли, используемое учетными записями запуска от имени для нескольких подписок или учетных записей автоматизации. Это можно сделать с помощью скрипта [чекк-аутоматионрунасаккаунтролеассигнментс. ps1](https://aka.ms/AA5hug5) в коллекция PowerShell.

### <a name="add-permissions-to-key-vault"></a>Добавление разрешений в Key Vault

Вы можете разрешить службе автоматизации Azure проверять, использует ли Key Vault и субъект-службу учетной записи запуска от имени определение пользовательской роли. Для этого необходимо выполнить следующие действия.

* Предоставьте разрешения на Key Vault.
* Задайте политику доступа.

Вы можете использовать сценарий [екстенд-аутоматионрунасаккаунтролеассигнменттокэйваулт. ps1](https://aka.ms/AA5hugb) в коллекция PowerShell, чтобы предоставить учетной записи запуска от имени разрешения Key Vault. Дополнительные сведения о настройке разрешений на Key Vault см. в разделе [предоставление приложениям доступа к хранилищу ключей](../key-vault/key-vault-group-permissions-for-apps.md) .

## <a name="resolving-misconfiguration-issues-for-run-as-accounts"></a>Устранение проблем с неисправной конфигурацией для учетных записей запуска от имени

Некоторые элементы конфигурации, необходимые для правильной работы учетной записи запуска от имени или классической модели запуска от имени, могут быть удалены или неправильно созданы во время начальной установки. Ниже перечислены некоторые экземпляры неопределенной конфигурации.

* ресурс сертификата,
* ресурс подключения,
* Учетная запись запуска от имени удалена из роли участника
* субъект-служба или приложение-служба в Azure AD,

Для таких экземпляров с недопустимыми настройками учетная запись службы автоматизации обнаруживает изменения и отображает состояние "не **завершено** " в области свойств учетных записей запуска от имени.

![Сообщение о том, что настройка учетной записи запуска от имени не завершена](media/manage-runas-account/automation-account-runas-incomplete-config.png)

При выборе учетной записи запуска от имени в области Свойства учетной записи отображается следующее сообщение об ошибке:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Эти проблемы с учетной записью запуска от имени можно быстро устранить, удалив и повторно создав ее.

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о субъектах-службах см. в разделе [объекты приложения и объекты субъекта-службы](../active-directory/develop/app-objects-and-service-principals.md).
* Дополнительные сведения о сертификатах и службах Azure см. в статье [Общие сведения о сертификатах для облачных служб Azure](../cloud-services/cloud-services-certs-create.md).
