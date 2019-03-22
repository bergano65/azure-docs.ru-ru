---
title: Автоматическое обновление службы Mobility Service в Azure в Azure аварийного восстановления | Документация Майкрософт
description: Обзор автоматического обновления службы Mobility Service при репликации виртуальных машин Azure с помощью Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: f2467314a4f131b88fc1baf2233ca8ce74d488cb
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548955"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Автоматическое обновление службы Mobility Service в репликации из Azure в Azure

Azure Site Recovery использует месячную периодичность выпуска для устранения неисправностей и улучшения существующих функций или добавить новые. Чтобы актуальность со службой, необходимо спланировать развертывание исправлений каждый месяц. Чтобы избежать издержек, связанных с каждого обновления, вместо этого можно разрешить Site Recovery для управления обновлениями компонента.

Как упоминалось в [Azure в Azure аварийного восстановления архитектура](azure-to-azure-architecture.md), служба Mobility service устанавливается на всех виртуальных машинах Azure (ВМ) для которых включена репликация, при репликации виртуальных машин из одного региона Azure в другой. При использовании автоматического обновления, каждый новый выпуск обновляет расширение службы мобильности.
 
## <a name="how-automatic-updates-work"></a>Работы автоматического обновления

При использовании Site Recovery для управления обновлениями, она развертывает глобального runbook (используется службами Azure) с помощью учетной записи службы автоматизации, созданный в той же подписке, что и хранилище. Каждое хранилище использует учетную запись службы автоматизации. Модуль runbook проверяет для каждой виртуальной Машины в хранилище active автоматического обновления и обновляет расширение службы мобильности, если доступна более новая версия.

Ежедневно в 12:00 по часовому поясу реплицированная виртуальная машина geo повторения расписания runbook по умолчанию. Можно также изменить расписание runbook с помощью учетной записи службы автоматизации.

> [!NOTE]
> Включение автоматического обновления не требуют перезагрузки ваших виртуальных машин Azure и не влияют на текущую репликацию.

> [!NOTE]
> Задания, выставление счетов в учетной записи службы автоматизации зависит от среды выполнения задания в минутах в месяц. По умолчанию 500 минут включены как бесплатные единицы для учетной записи службы автоматизации. Выполнение задания занимает несколько секунд, чтобы около минуты каждый день, которая рассматривается как бесплатные единицы.

| Включенные бесплатные единицы (каждый месяц) | Цена |
|---|---|
| Время выполнения задания 500 минут | ₹0.14 / мин

## <a name="enable-automatic-updates"></a>Включение автоматических обновлений

Вы можете разрешить Site Recovery для управления обновлениями, одним из следующих способов.

### <a name="manage-as-part-of-the-enable-replication-step"></a>Управление системами на шаге включения репликации

При включении репликации для виртуальной Машины либо начиная [в представлении виртуальной Машины](azure-to-azure-quickstart.md) или [из хранилища служб восстановления](azure-to-azure-how-to-enable-replication.md), можно либо разрешить Site Recovery, чтобы управлять обновлениями для расширения Site Recovery или управлять им вручную.

![Параметры расширений](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Переключение параметров обновления для расширения внутри хранилища

1. В хранилище, перейдите к **управление** > **инфраструктура Site Recovery**.
2. В разделе **для виртуальных машин Azure** > **параметры обновления расширений**, включите **разрешить Site Recovery для управления** переключателя. Чтобы управлять вручную, отключите его. 
3. Щелкните **Сохранить**.

![Параметры обновления расширений](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important]
> При выборе **разрешить Site Recovery для управления**, то установка применяется ко всем виртуальным машинам в соответствующее хранилище.


> [!Note]
> Любой из вариантов уведомит пользователя учетной записи службы автоматизации, используемой для управления обновлениями. Если вы используете эту функцию в хранилище в первый раз, создается учетной записи службы автоматизации. Все последующие включения репликации в том же хранилище использовать ранее созданный.

Для учетной записи пользовательской автоматизацией тестирования используйте следующий сценарий:

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

            # Rate controlling the get calls to maximum 120 calls each minute.
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

### <a name="manage-updates-manually"></a>Управление обновлениями вручную

1. При наличии новых обновлений для мобильной службы, установленной на виртуальных машинах, вы увидите следующее уведомление: «Доступно новое обновление агента репликации сайта восстановления. Щелкните, чтобы установить»

     ![Окно "Реплицированные элементы"](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)
2. Выберите уведомление, чтобы открыть страницу выбора виртуальной Машины.
3. Выберите виртуальные машины, необходимо обновить и затем выберите **ОК**. Обновить службу Mobility service, запускаемых для каждой выбранной виртуальной Машины.

     ![Список виртуальных машин "Реплицированные элементы"](./media/vmware-azure-install-mobility-service/update-okpng.png)


## <a name="common-issues-and-troubleshooting"></a>Распространенные проблемы и устранение неполадок

Если имеется проблема с помощью автоматического обновления, вы увидите уведомление об ошибке в разделе **проблемы с конфигурацией** на панели мониторинга хранилища.

Если вы не включали автоматическое обновление, см. в разделе следующих распространенных ошибок и рекомендуемые действия:

- **Ошибка**: "У вас нет разрешений для создания учетной записи запуска от имени Azure (субъекта-службы) и предоставления роли участника субъекту-службе".

   **Рекомендуемое действие**: Убедитесь, что назначенный учетной записи вошедшего в систему в качестве участника и повторите попытку. См. в разделе "необходимые разрешения" в [использовать портал для создания приложения и субъекта-службы с доступом к ресурсам Azure AD](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) Дополнительные сведения о назначении разрешений.
 
   Чтобы устранить большинство проблем, после включения автоматического обновления, выберите **восстановления**. Если кнопка восстановления недоступна, см. сообщение об ошибке в области параметров обновления расширения.

   ![Кнопка восстановления службы восстановления сайта в параметры обновления расширений](./media/azure-to-azure-autoupdate/repair.png)

- **Ошибка**: "Учетная запись запуска от имени не имеет разрешений на доступ к ресурсу служб восстановления".

    **Рекомендуемое действие**: Удалить и затем [повторно создайте учетную запись запуска от имени](https://docs.microsoft.com/azure/automation/automation-create-runas-account). Также убедитесь, что приложение Azure Active Directory автоматизации запуска от имени учетной записи имеет доступ к ресурсу служб восстановления.

- **Ошибка**: "Учетная запись запуска от имени не найдена". Не был создан или был удален один из следующих объектов: приложение Azure Active Directory, субъект-служба, роль, ресурс сертификата службы автоматизации, ресурс подключения службы автоматизации. Либо не совпадает отпечаток у сертификата и подключения. 

    **Рекомендуемое действие**: Удалить и затем [повторно создайте учетную запись запуска от имени](https://docs.microsoft.com/azure/automation/automation-create-runas-account).
