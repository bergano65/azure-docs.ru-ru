---
title: Автоматическое обновление службы Mobility Service в Azure на аварийное восстановление Azure | Документация Майкрософт
description: Общие сведения об автоматическом обновлении службы Mobility Service при репликации виртуальных машин Azure с помощью Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/24/2019
ms.author: rajanaki
ms.openlocfilehash: 79a11a58f11486f3eda0205e62e7a4a92ff070b2
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933929"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Автоматическое обновление службы Mobility Service в репликации Azure в Azure

Azure Site Recovery использует ежемесячную частоту выпуска для устранения любых проблем и улучшения существующих функций или добавления новых. Для поддержания актуальности службы необходимо планировать развертывание исправлений каждый месяц. Чтобы избежать издержек, связанных с каждым обновлением, можно вместо этого разрешить Site Recovery управлять обновлениями компонентов.

Как упоминалось в [архитектуре аварийного восстановления Azure в Azure](azure-to-azure-architecture.md), служба Mobility Service устанавливается на всех виртуальных машинах Azure, для которых включена репликация, при репликации виртуальных машин из одного региона Azure в другой. При использовании автоматического обновления каждый новый выпуск обновляет расширение службы Mobility Service.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Как работают автоматические обновления

При использовании Site Recovery для управления обновлениями он развертывает глобальный модуль Runbook (используемый службами Azure) через учетную запись службы автоматизации, созданную в той же подписке, что и хранилище. В каждом хранилище используется одна учетная запись службы автоматизации. Модуль Runbook проверяет наличие активных автообновлений для каждой виртуальной машины в хранилище и обновляет расширение службы Mobility Service, если доступна более новая версия.

Расписание Runbook по умолчанию повторяется ежедневно в 12:00 часового пояса для Geo реплицированной виртуальной машины. Вы также можете изменить расписание Runbook с помощью учетной записи службы автоматизации.

> [!NOTE]
> Начиная с накопительного пакета обновления 35, можно выбрать существующую учетную запись службы автоматизации для использования при обновлениях. До этого обновления Site Recovery создать эту учетную запись по умолчанию. Этот параметр доступен при включении репликации для виртуальной машины. Если изменить этот параметр, он будет применяться ко всем виртуальным машинам Azure, защищенным в том же хранилище.
 
> Включение автоматического обновления не требует перезапуска виртуальных машин Azure или повлияет на текущую репликацию.

> Выставление счетов за задание в учетной записи службы автоматизации зависит от количества минут выполнения заданий, использованных в месяц. По умолчанию 500 минут включается в качестве свободных единиц для учетной записи службы автоматизации. Каждый день выполнение задания занимает несколько секунд и рассматривается как свободные единицы.

| Количество свободных единиц (каждый месяц) | Цена |
|---|---|
| Время выполнения задания 500 минут | ₹ 0.14/мин

## <a name="enable-automatic-updates"></a>Включение автоматических обновлений

Вы можете разрешить Site Recovery управлять обновлениями следующими способами.

### <a name="manage-as-part-of-the-enable-replication-step"></a>Управление в рамках шага включения репликации

При включении репликации для виртуальной машины либо из [представления виртуальной машины](azure-to-azure-quickstart.md) , либо [из хранилища служб восстановления](azure-to-azure-how-to-enable-replication.md)можно либо разрешить Site Recovery управлять обновлениями для расширения Site Recovery, либо управлять им вручную.

![Параметры расширения](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Переключение параметров обновления для расширения внутри хранилища

1. В хранилище перейдите к разделу **управление** > **инфраструктурой Site Recovery**.
2. В разделе **для виртуальных машин Azure** > **Параметры обновления расширения**включите переключатель **Разрешить Site Recovery управление** . Для управления вручную отключите его. 
3. Щелкните **Сохранить**.

![Параметры обновления расширения](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important]
> При выборе параметра **разрешить Site Recovery для управления**этот параметр применяется ко всем виртуальным машинам в соответствующем хранилище.


> [!Note]
> Любой параметр уведомляет вас об учетной записи службы автоматизации, используемой для управления обновлениями. Если вы используете эту функцию в хранилище впервые, по умолчанию создается новая учетная запись службы автоматизации. Кроме того, можно настроить параметр и выбрать существующую учетную запись службы автоматизации. Все последующие включения репликации в том же хранилище используют созданную ранее. В настоящее время раскрывающийся список будет вычислять только учетные записи службы автоматизации, которые находятся в той же группе ресурсов, что и хранилище.  

> [!IMPORTANT]
> Приведенный ниже сценарий необходимо выполнить в контексте учетной записи службы автоматизации для настраиваемой учетной записи службы автоматизации, используя следующий скрипт:

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
                Write-InformationTracing ("Ignoring container mapping: {0} as the state is not paired." -f ($Mapping.Id))
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

$AzureRMProfile = Get-Module -ListAvailable -Name Az.Accounts | Select Name, Version, Path
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

1. Если на виртуальных машинах установлены новые обновления для службы Mobility Service, вы увидите следующее уведомление: "новое Site Recovery доступно обновление агента репликации. Щелкните, чтобы установить

     ![Окно "Реплицированные элементы"](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)
2. Выберите уведомление, чтобы открыть страницу выбора виртуальной машины.
3. Выберите виртуальные машины, которые требуется обновить, и нажмите кнопку **ОК**. Служба обновления Mobility Service будет запущена для каждой выбранной виртуальной машины.

     ![Список виртуальных машин "Реплицированные элементы"](./media/vmware-azure-install-mobility-service/update-okpng.png)


## <a name="common-issues-and-troubleshooting"></a>Распространенные проблемы и устранение неполадок

Если возникла проблема с автоматическими обновлениями, вы увидите уведомление об ошибке в разделе **проблемы конфигурации** на панели мониторинга хранилища.

Если не удалось включить автоматическое обновление, ознакомьтесь со следующими распространенными ошибками и рекомендуемыми действиями.

- **Ошибка**: у вас нет разрешений на создание учетной записи запуска от имени Azure (субъекта-службы) и предоставление субъекту-службе роли участника.

   **Рекомендуемое действие**: Убедитесь, что учетная запись для входа назначена как участник, и повторите попытку. Дополнительные сведения о назначении разрешений см. в разделе "необходимые разрешения" раздела [Использование портала для создания приложения Azure AD и субъекта-службы, которые могут получить доступ к ресурсам](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) .
 
   Чтобы устранить большинство проблем после включения автоматического обновления, выберите **восстановить**. Если кнопка восстановить недоступна, см. сообщение об ошибке, отображаемое в области Параметры обновления расширений.

   ![Кнопка восстановления службы Site Recovery в параметрах обновления расширения](./media/azure-to-azure-autoupdate/repair.png)

- **Ошибка**: учетная запись запуска от имени не имеет разрешения на доступ к ресурсу служб восстановления.

    **Рекомендуемое действие**: удалите и [повторно создайте учетную запись запуска от имени](https://docs.microsoft.com/azure/automation/automation-create-runas-account). Или убедитесь, что Azure Active Directory приложение учетной записи запуска от имени службы автоматизации имеет доступ к ресурсу служб восстановления.

- **Ошибка**: учетная запись запуска от имени не найдена. Не был создан или был удален один из следующих объектов: приложение Azure Active Directory, субъект-служба, роль, ресурс сертификата службы автоматизации, ресурс подключения службы автоматизации. Либо не совпадает отпечаток у сертификата и подключения. 

    **Рекомендуемое действие**: удалите и [повторно создайте учетную запись запуска от имени](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

-  **Ошибка**: истекает срок действия сертификата запуска от имени Azure, используемого учетной записью службы автоматизации. 

    Срок действия самозаверяющего сертификата, созданного для учетной записи запуска от имени, истекает один год с даты создания. Его можно обновить в любое время до истечения его срока действия. Если вы подписались на уведомления по электронной почте, вы также получаете сообщения электронной почты, когда на стороне вас потребуются какие-либо действия. Эта ошибка будет отображаться на два месяца раньше даты истечения срока действия и изменится на критическую ошибку, если срок действия сертификата истек. После истечения срока действия сертификата автоматическое обновление не будет работать до тех пор, пока вы не обновите его.

   **Рекомендуемое действие**: чтобы устранить эту проблему, щелкните "восстановить", а затем "обновить сертификат".
    
   ![продление сертификата](media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG)

> [!NOTE]
> После продления сертификата обновите страницу, чтобы обновить текущее состояние.
