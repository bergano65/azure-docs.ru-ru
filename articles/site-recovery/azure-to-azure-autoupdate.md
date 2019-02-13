---
title: Автоматическое обновление службы мобильности в Azure при аварийном восстановлении Azure | Документы Майкрософт
description: Содержит общие сведения об автоматическом обновлении службы мобильности при репликации виртуальных машин Azure с помощью Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 3f0f28ca22321b537ab7e8911c5cbb513a1ade81
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55818930"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Автоматическое обновление службы мобильности в Azure при репликации Azure

Для Azure Site Recovery установлена ежемесячная периодичность выпусков, когда добавляются новые или усовершенствованные функции, а также устраняются известные проблемы. Это означает, что для поддержания службы в актуальном состоянии нужно спланировать развертывание этих исправлений на ежемесячной основе. Во избежание дополнительной нагрузки, связанной с обновлением, пользователи могут разрешить Site Recovery управлять обновлениями компонентов. Как описано в [справочнике по архитектуре](azure-to-azure-architecture.md) Azure для аварийного восстановления Azure, служба мобильности устанавливается на всех виртуальных машинах Azure, для которых включена репликация, при их репликации из одного региона Azure в другой. После включения функции автоматического обновления расширение службы мобильности будет обновляться с каждым новым выпуском. Содержание этого документа:

- Как работает автоматическое обновление?
- Включение автоматических обновлений
- Распространенные проблемы и устранение неполадок
 
## <a name="how-does-automatic-update-work"></a>Как работает автоматическое обновление

Когда вы разрешаете Site Recovery управлять обновлениями, с помощью учетной записи службы автоматизации, создаваемой в одной подписке с хранилищем, развертывается глобальный runbook (используемый службами Azure). Для конкретного хранилища используется одна учетная запись службы автоматизации. Этот runbook проверяет каждую виртуальную машину в хранилище, для которой включено автоматическое обновление, и инициирует обновление расширения службы мобильности при наличии новой версии. Расписание по умолчанию для runbook повторяется ежедневно в 00:00 согласно часовому поясу географического расположения для реплицированной виртуальной машины. При необходимости пользователь также может изменить расписание runbook с помощью учетной записи службы автоматизации. 

> [!NOTE]
> Чтобы включить функцию автоматического обновления, перезагружать виртуальные машины Azure не требуется. Это также не влияет на выполняющуюся репликацию.

> [!NOTE]
> Счет за задания, используемые учетной записью службы автоматизации, выставляется по времени выполнения задания в минутах за месяц. По умолчанию для учетной записи службы автоматизации 500 минут предоставляется бесплатно. Выполнение этого задания занимает от **нескольких секунд до минуты** в день и будет **оплачиваться бесплатными кредитами**.

ВКЛЮЧЕННЫЕ БЕСПЛАТНЫЕ ЕДИНИЦЫ (В МЕСЯЦ)**   ЦЕНА Время выполнения задания    500 минут ₹0,14/мин.

## <a name="enable-automatic-updates"></a>Включение автоматических обновлений

Вы можете разрешить Site Recovery управлять обновлениями одним из следующих способов:

- [В рамках шага включения репликации](#as-part-of-the-enable-replication-step)
- [Переключение параметров обновления для расширения внутри хранилища](#toggle-the-extension-update-settings-inside-the-vault)

### <a name="as-part-of-the-enable-replication-step"></a>В рамках шага включения репликации:

При включении репликации для виртуальной машины [из представления виртуальной машины](azure-to-azure-quickstart.md) или [из хранилища служб восстановления](azure-to-azure-how-to-enable-replication.md) вы можете разрешить Site Recovery управлять обновлениями для расширения Site Recovery или управлять ими вручную.

![enable-replication-auto-update](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Переключение параметров обновления для расширения внутри хранилища

1. Внутри хранилища выберите **Управление**-> **Site Recovery Infrastructure** (Инфраструктура Site Recovery).
2. В разделе **For Azure virtual Machines**-> **Extension Update Settings** (Для виртуальных машин Azure > Параметры обновления для расширения) щелкните переключатель, чтобы разрешить *ASR управлять обновлениями* или *обновлять ими вручную*. Выберите команду **Сохранить**.

![vault-toggle-auto-update](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important] 
> Если выбрать значение *Allow ASR to manage* (Разрешить управление ASR), оно применяется ко всем виртуальным машинам в соответствующем хранилище.


> [!Note] 
> В обоих случаях вы получите уведомление об учетной записи службы автоматизации, используемой для управления обновлениями. При включении этой функции в первый раз в хранилище создается учетная запись службы автоматизации. При последующих включениях репликации в том же хранилище будет использоваться уже созданная учетная запись.

**Если вы хотите использовать пользовательскую учетную запись автоматизации, используйте следующий скрипт:-**

```azurepowershell
param(
    [Parameter(Mandatory=$true)]
    [String] $VaultResourceId,

    [Parameter(Mandatory=$true)]
    [ValidateSet("Enabled",'Disabled')]
    [Alias("Enabled or Disabled")]
    [String] $AutoUpdateAction,

    [Parameter(Mandatory=$false)]
    [String] $AutomationAccountArmId
)

$SiteRecoveryRunbookName = "Modify-AutoUpdateForVaultForPatner"
$TaskId = [guid]::NewGuid().ToString()
$SubscriptionId = "00000000-0000-0000-0000-000000000000"
$AsrApiVersion = "2018-01-10"
$RunAsConnectionName = "AzureRunAsConnection"
$ArmEndPoint = "https://management.azure.com"
$AadAuthority = "https://login.windows.net/"
$AadAudience = "https://management.core.windows.net/"
$AzureEnvironment = "AzureCloud"
$Timeout = "160"

function Throw-TerminatingErrorMessage
{
    Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
    )

    throw ("Message: {0}, TaskId: {1}.") -f $Message, $TaskId
}

function Write-Tracing
{
    Param
    (
        [Parameter(Mandatory=$true)]      
        [ValidateSet("Informational", "Warning", "ErrorLevel", "Succeeded", IgnoreCase = $true)]
        [String]
        $Level,

        [Parameter(Mandatory=$true)]
        [String]
        $Message,

        [Switch]
        $DisplayMessageToUser
    )

    Write-Output $Message

}

function Write-InformationTracing
{
    Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
    )

    Write-Tracing -Message $Message -Level Informational -DisplayMessageToUser
}

function ValidateInput()
{
    try
    {
        if(!$VaultResourceId.StartsWith("/subscriptions", [System.StringComparison]::OrdinalIgnoreCase))
        {
            $ErrorMessage = "The vault resource id should start with /subscriptions."
            throw $ErrorMessage
        }

        $Tokens = $VaultResourceId.SubString(1).Split("/")
        if(!($Tokens.Count % 2 -eq 0))
        {
            $ErrorMessage = ("Odd Number of tokens: {0}." -f $Tokens.Count)
            throw $ErrorMessage
        }

        if(!($Tokens.Count/2 -eq 4))
        {
            $ErrorMessage = ("Invalid number of resource in vault ARM id expected:4, actual:{0}." -f ($Tokens.Count/2))
            throw $ErrorMessage
        }

        if($AutoUpdateAction -ieq "Enabled" -and [string]::IsNullOrEmpty($AutomationAccountArmId))
        {
            $ErrorMessage = ("The automation account ARM id should not be null or empty when AutoUpdateAction is enabled.")
            throw $ErrorMessage
        }
    }
    catch
    {
        $ErrorMessage = ("ValidateInput failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Initialize-SubscriptionId()
{
    try
    {
        $Tokens = $VaultResourceId.SubString(1).Split("/")

        $Count = 0
        $ArmResources = @{}
        while($Count -lt $Tokens.Count)
        {
            $ArmResources[$Tokens[$Count]] = $Tokens[$Count+1]
            $Count = $Count + 2
        }
        
        return $ArmResources["subscriptions"]
    }
    catch
    {
        Write-Tracing -Level ErrorLevel -Message ("Initialize-SubscriptionId: failed with [Exception: {0}]." -f $_.Exception) -DisplayMessageToUser
        throw
    }
}

function Invoke-InternalRestMethod($Uri, $Headers, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-RestMethod -Uri $Uri -Headers $Headers    
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Invoke-InternalWebRequest($Uri, $Headers, $Method, $Body, $ContentType, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-WebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -UseBasicParsing
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Get-Header([ref]$Header, $AadAudience, $AadAuthority, $RunAsConnectionName){
    try 
    {
        $RunAsConnection = Get-AutomationConnection -Name $RunAsConnectionName
        $TenantId = $RunAsConnection.TenantId
        $ApplicationId = $RunAsConnection.ApplicationId
        $CertificateThumbprint = $RunAsConnection.CertificateThumbprint
        $Path = "cert:\CurrentUser\My\{0}" -f $CertificateThumbprint
        $Secret = Get-ChildItem -Path $Path
        $ClientCredential = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.ClientAssertionCertificate(
                $ApplicationId,
                $Secret)

        # Trim the forward slash from the AadAuthority if it exist.
        $AadAuthority = $AadAuthority.TrimEnd("/")
        $AuthContext = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(
            "{0}/{1}" -f $AadAuthority, $TenantId )
        $AuthenticationResult = $authContext.AcquireToken($AadAudience, $Clientcredential)
        $Header.Value['Content-Type'] = 'application\json'
        $Header.Value['Authorization'] = $AuthenticationResult.CreateAuthorizationHeader()
        $Header.Value["x-ms-client-request-id"] = $TaskId + "/" + (New-Guid).ToString() + "-" + (Get-Date).ToString("u")
    }
    catch
    {
        $ErrorMessage = ("Get-BearerToken: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Get-ProtectionContainerToBeModified([ref] $ContainerMappingList)
{
    try 
    {
        Write-InformationTracing ("Get protection container mappings : {0}." -f $VaultResourceId)
        $ContainerMappingListUrl = $ArmEndPoint + $VaultResourceId + "/replicationProtectionContainerMappings" + "?api-version=" + $AsrApiVersion
        
        Write-InformationTracing ("Getting the bearer token and the header.")
        Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
        
        $Result = @()
        Invoke-InternalRestMethod -Uri $ContainerMappingListUrl -Headers $header -Result ([ref]$Result)
        $ContainerMappings = $Result[0]

        Write-InformationTracing ("Total retrieved container mappings: {0}." -f $ContainerMappings.Value.Count)
        foreach($Mapping in $ContainerMappings.Value)
        {
            if(($Mapping.properties.providerSpecificDetails -eq $null) -or ($Mapping.properties.providerSpecificDetails.instanceType -ine "A2A"))
            {
                Write-InformationTracing ("Mapping properties: {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the provider does not match." -f ($Mapping.Id))
                continue;
            }

            if($Mapping.Properties.State -ine "Paired")
            {
                Write-InformationTracing ("Ignoring container mapping: {0} as the the state is not paired." -f ($Mapping.Id))
                continue;
            }

            Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties.providerSpecificDetails))
            $MappingAutoUpdateStatus = $Mapping.properties.providerSpecificDetails.agentAutoUpdateStatus
            $MappingAutomationAccountArmId = $Mapping.properties.providerSpecificDetails.automationAccountArmId
            $MappingHealthErrorCount = $Mapping.properties.HealthErrorDetails.Count

            if($AutoUpdateAction -ieq "Enabled" -and
                ($MappingAutoUpdateStatus -ieq "Enabled") -and
                ($MappingAutomationAccountArmId -ieq $AutomationAccountArmId) -and
                ($MappingHealthErrorCount -eq 0))
            {
                Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the auto update is already enabled and is healthy." -f ($Mapping.Id))
                continue;
            }

            ($ContainerMappingList.Value).Add($Mapping.id)
        }
    }
    catch
    {
        $ErrorMessage = ("Get-ProtectionContainerToBeModified: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

$OperationStartTime = Get-Date
$ContainerMappingList = New-Object System.Collections.Generic.List[System.String]
$JobsInProgressList = @()
$JobsCompletedSuccessList = @()
$JobsCompletedFailedList = @()
$JobsFailedToStart = 0
$JobsTimedOut = 0
$Header = @{}

$AzureRMProfile = Get-Module -ListAvailable -Name AzureRM.Profile | Select Name, Version, Path
$AzureRmProfileModulePath = Split-Path -Parent $AzureRMProfile.Path
Add-Type -Path (Join-Path $AzureRmProfileModulePath "Microsoft.IdentityModel.Clients.ActiveDirectory.dll")

$Inputs = ("Tracing inputs VaultResourceId: {0}, Timeout: {1}, AutoUpdateAction: {2}, AutomationAccountArmId: {3}." -f $VaultResourceId, $Timeout, $AutoUpdateAction, $AutomationAccountArmId)
Write-Tracing -Message $Inputs -Level Informational -DisplayMessageToUser
$CloudConfig = ("Tracing cloud configuration ArmEndPoint: {0}, AadAuthority: {1}, AadAudience: {2}." -f $ArmEndPoint, $AadAuthority, $AadAudience)
Write-Tracing -Message $CloudConfig -Level Informational -DisplayMessageToUser
$AutomationConfig = ("Tracing automation configuration RunAsConnectionName: {0}." -f $RunAsConnectionName)
Write-Tracing -Message $AutomationConfig -Level Informational -DisplayMessageToUser

ValidateInput
$SubscriptionId = Initialize-SubscriptionId
Get-ProtectionContainerToBeModified ([ref]$ContainerMappingList)

$Input = @{
  "properties"= @{
    "providerSpecificInput"= @{
        "instanceType" = "A2A"
        "agentAutoUpdateStatus" = $AutoUpdateAction
        "automationAccountArmId" = $AutomationAccountArmId
    }
  }
}
$InputJson = $Input |  ConvertTo-Json

if ($ContainerMappingList.Count -eq 0)
{
    Write-Tracing -Level Succeeded -Message ("Exiting as there are no container mappings to be modified.") -DisplayMessageToUser
    exit
}

Write-InformationTracing ("Container mappings to be updated has been retrieved with count: {0}." -f $ContainerMappingList.Count)

try
{
    Write-InformationTracing ("Start the modify container mapping jobs.")
    ForEach($Mapping in $ContainerMappingList)
    {
    try {
            $UpdateUrl = $ArmEndPoint + $Mapping + "?api-version=" + $AsrApiVersion
            Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
            
            $Result = @()
            Invoke-InternalWebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -Result ([ref]$Result)
            $Result = $Result[0]

            $JobAsyncUrl = $Result.Headers['Azure-AsyncOperation']
            Write-InformationTracing ("The modify container mapping job invoked with async url: {0}." -f $JobAsyncUrl)
            $JobsInProgressList += $JobAsyncUrl;

            # Rate controlling the set calls to maximum 60 calls per minute.
            # ASR throttling for set calls is 200 in 1 minute.
            Start-Sleep -Milliseconds 1000
        }
        catch{
            Write-InformationTracing ("The modify container mappings job creation failed for: {0}." -f $Ru)
            Write-InformationTracing $_
            $JobsFailedToStart++
        }
    }

    Write-InformationTracing ("Total modify container mappings has been initiated: {0}." -f $JobsInProgressList.Count)
}
catch
{
    $ErrorMessage = ("Modify container mapping jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

try
{
    while($JobsInProgressList.Count -ne 0)
    {
        Sleep -Seconds 30
        $JobsInProgressListInternal = @()
        ForEach($JobAsyncUrl in $JobsInProgressList)
        {
            try
            {
                Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
                $Result = Invoke-RestMethod -Uri $JobAsyncUrl -Headers $header
                $JobState = $Result.Status
                if($JobState -ieq "InProgress")
                {
                    $JobsInProgressListInternal += $JobAsyncUrl
                }
                elseif($JobState -ieq "Succeeded" -or `
                    $JobState -ieq "PartiallySucceeded" -or `
                    $JobState -ieq "CompletedWithInformation")
                {
                    Write-InformationTracing ("Jobs succeeded with state: {0}." -f $JobState)
                    $JobsCompletedSuccessList += $JobAsyncUrl
                }
                else
                {
                    Write-InformationTracing ("Jobs failed with state: {0}." -f $JobState)
                    $JobsCompletedFailedList += $JobAsyncUrl
                }
            }
            catch
            {
                Write-InformationTracing ("The get job failed with: {0}. Ignoring the exception and retrying the next job." -f $_.Exception)

                # The job on which the tracking failed, will be considered in progress and tried again later.
                $JobsInProgressListInternal += $JobAsyncUrl
            }

            # Rate controlling the get calls to maximum 120 calls per minute.
            # ASR throttling for get calls is 10000 in 60 minutes.
            Start-Sleep -Milliseconds 500
        }

        Write-InformationTracing ("Jobs remaining {0}." -f $JobsInProgressListInternal.Count)

        $CurrentTime = Get-Date
        if($CurrentTime -gt $OperationStartTime.AddMinutes($Timeout))
        {
            Write-InformationTracing ("Tracing modify cloud pairing jobs has timed out.")
            $JobsTimedOut = $JobsInProgressListInternal.Count
            $JobsInProgressListInternal = @()
        }

        $JobsInProgressList = $JobsInProgressListInternal
    }
}
catch
{
    $ErrorMessage = ("Tracking modify cloud pairing jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage  -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage 
}

Write-InformationTracing ("Tracking modify cloud pairing jobs completed.")
Write-InformationTracing ("Modify cloud pairing jobs success: {0}." -f $JobsCompletedSuccessList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed: {0}." -f $JobsCompletedFailedList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed to start: {0}." -f $JobsFailedToStart)
Write-InformationTracing ("Modify cloud pairing jobs timedout: {0}." -f $JobsTimedOut)

if($JobsTimedOut -gt  0)
{
    $ErrorMessage = "One or more modify cloud pairing jobs has timedout."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)   
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
elseif($JobsCompletedSuccessList.Count -ne $ContainerMappingList.Count)
{
    $ErrorMessage = "One or more modify cloud pairing jobs failed."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

Write-Tracing -Level Succeeded -Message ("Modify cloud pairing completed.") -DisplayMessageToUser
```

### <a name="manage-manually"></a>Управление вручную

1. Если доступны новые обновления для мобильной службы, установленной на виртуальных машинах Azure, вы увидите уведомление: "Доступно новое обновление агента репликации сайта восстановления. Щелкните, чтобы установить его."

     ![Окно "Реплицированные элементы"](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)
3. Выберите уведомление, чтобы открыть страницу выбора виртуальной машины.
4. Выберите виртуальные машины, на которых нужно обновить службу Mobility Service, и нажмите кнопку **ОК**.

     ![Список виртуальных машин "Реплицированные элементы"](./media/vmware-azure-install-mobility-service/update-okpng.png)

Для каждой выбранной виртуальной машины будет запущено задание обновления службы Mobility Service.


## <a name="common-issues--troubleshooting"></a>Распространенные проблемы и устранение неполадок

При возникновении проблемы с автоматическими обновлениями вы получите уведомление в разделе "Configuration issues" (Проблемы с конфигурацией) на панели мониторинга хранилища. 

Если вы попытались включить автоматические обновления и произошел сбой, см. сведения об устранении неполадок ниже.

**Ошибка**: "У вас нет разрешений для создания учетной записи запуска от имени Azure (субъекта-службы) и предоставления роли участника субъекту-службе". 
- Рекомендуемое действие: убедитесь, что учетной записи, используемой для входа, назначена роль "Участник", и повторите операцию. Дополнительные сведения о назначении нужных разрешений см. в [этом](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) документе.
 
После включения автоматических обновлений большинство проблем может быть решено службой Site Recovery, для чего нужно нажать кнопку "**Исправить**".

![repair-button](./media/azure-to-azure-autoupdate/repair.png)

Если кнопка "Исправить" недоступна, ознакомьтесь с сообщением об ошибке, отображаемым под панелью параметров расширения.

 - **Ошибка**: "Учетная запись запуска от имени не имеет разрешений на доступ к ресурсу служб восстановления".

    **Рекомендуемое действие.** Удалите и [повторно создайте учетную запись запуска от имени](https://docs.microsoft.com/azure/automation/automation-create-runas-account) или убедитесь, что приложение Azure Active Directory, которое использует учетную запись запуска от имени службы автоматизации, имеет доступ к ресурсу служб восстановления.

- **Ошибка**: "Учетная запись запуска от имени не найдена". Не был создан или был удален один из следующих объектов: приложение Azure Active Directory, субъект-служба, роль, ресурс сертификата службы автоматизации, ресурс подключения службы автоматизации. Либо не совпадает отпечаток у сертификата и подключения. 

    **Рекомендуемое действие.** Удалите и [повторно создайте учетную запись запуска от имени](https://docs.microsoft.com/azure/automation/automation-create-runas-account).
