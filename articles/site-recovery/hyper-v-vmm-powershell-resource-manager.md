---
title: Настройка аварийного восстановления виртуальных машин Hyper-V в облаках VMM на дополнительный сайт с помощью Azure Site Recovery и PowerShell | Документация Майкрософт
description: В этой статье описана настройка аварийного восстановления виртуальных машин Hyper-V в облаках VMM на дополнительный сайт VMM с помощью Azure Site Recovery и PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 78bd077b5491b093510b9c55bf7b5a42ee9cb578
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60362362"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Настройка аварийного восстановления виртуальных машин Hyper-V на дополнительный сайт с помощью PowerShell (Resource Manager)

В этой статье показано, как автоматизировать шаги для репликации виртуальных машин Hyper-V из облаков System Center Virtual Machine Manager в облако Virtual Machine Manager на вторичном локальном сайте с помощью [Azure Site Recovery](site-recovery-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Технические условия

- Ознакомьтесь с [архитектурой и компонентами сценария](hyper-v-vmm-architecture.md).
- [Ознакомьтесь](site-recovery-support-matrix-to-sec-site.md) с требованиями поддержки для всех компонентов.
- Убедитесь, что серверы Virtual Machine Manager и узлы Hyper-V соответствуют [требованиям поддержки](site-recovery-support-matrix-to-sec-site.md).
- Убедитесь, что виртуальные машины, которые следует реплицировать, соответствуют [требованиям поддержки реплицируемых машин](site-recovery-support-matrix-to-sec-site.md).


## <a name="prepare-for-network-mapping"></a>Подготовка к сопоставлению сетей

[Сопоставление сетей](hyper-v-vmm-network-mapping.md) происходит между локальными сетями виртуальных машин Virtual Machine Manager в исходном и целевом облаках. Это предоставляет следующие возможности:

- Подключение виртуальных машин к соответствующим целевым сетям виртуальных машин после отработки отказа. 
- Оптимальное размещение виртуальных машин реплик на целевых серверах узлов Hyper-V. 
- Если не настроить сетевое сопоставление, виртуальные машины реплик не будут подключены к каким-либо сетям виртуальных машин после отработки отказа.

Подготовьте Virtual Machine Manager следующим образом:

* Убедитесь, что на исходном и целевом серверах Virtual Machine Manager имеются [логические сети Virtual Machine Manager](https://docs.microsoft.com/system-center/vmm/network-logical).

    - Логическая сеть на исходном сервере должна быть связана с исходным облаком, в котором находятся узлы Hyper-V.
    - Логическая сеть на целевом сервере должна быть связана с целевым облаком.
* Убедитесь, что на исходном и целевом серверах Virtual Machine Manager имеются [виртуальные сети виртуальной машины](https://docs.microsoft.com/system-center/vmm/network-virtual). Сети виртуальных машин должны быть связаны с логической сетью в каждом расположении.
* Подключите виртуальные машины на исходных узлах Hyper-V к исходной сети виртуальных машин. 

## <a name="prepare-for-powershell"></a>Подготовка для работы с PowerShell

Прежде чем приступать, убедитесь, что среда Azure PowerShell готова к работе.

- Если вы уже используете PowerShell, выполните обновление до версии 0.8.10 или более поздней. Дополнительные сведения о настройке PowerShell см. [здесь](/powershell/azureps-cmdlets-docs).
- После установки и настройки PowerShell проверьте [командлеты службы](/powershell/azure/overview).
- Дополнительные сведения о том, как использовать значения параметров, входных и выходных данных в PowerShell, см. в руководстве [по началу работы](/powershell/azure/get-started-azureps).

## <a name="set-up-a-subscription"></a>Настройка подписки
1. Войдите в свою учетную запись Azure из PowerShell.

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Connect-AzAccount #-Credential $Cred
2. Получите список подписок вместе с идентификаторами. Запишите идентификатор подписки, в которой необходимо создать хранилище служб восстановления. 

        Get-AzSubscription
3. Настройте подписку для хранилища.

        Set-AzContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Создание хранилища служб восстановления
1. Если у вас еще нет группы ресурсов Azure Resource Manager, создайте ее.

        New-AzResourceGroup -Name #ResourceGroupName -Location #location
2. Создайте хранилище служб восстановления. Сохраните объект хранилища в переменной, которая будет использоваться позже. 

        $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   
    Объект хранилища можно получить после ее создания с помощью командлета Get-AzRecoveryServicesVault.

## <a name="set-the-vault-context"></a>Задание контекста хранилища
1. Получите имеющееся хранилище.

       $vault = Get-AzRecoveryServicesVault -Name #vaultname
2. Задание контекста хранилища.

       Set-AzSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-site-recovery-provider"></a>Установка поставщика Site Recovery
1. На компьютере с Virtual Machine Manager создайте каталог, выполнив следующую команду:

       New-Item c:\ASR -type directory
2. Извлеките файлы с помощью загруженного файла установки поставщика.

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Начните установку поставщика и дождитесь ее завершения.

       .\SetupDr.exe /i
       $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
       do
       {
         $isNotInstalled = $true;
         if(Test-Path $installationRegPath)
         {
           $isNotInstalled = $false;
         }
       }While($isNotInstalled)

4. Зарегистрируйте сервер в хранилище.

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="create-and-associate-a-replication-policy"></a>Создание и связывание политики репликации
1. Создайте политику репликации (в данном случае для Hyper-V 2012 R2) следующим образом:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > Облако Virtual Machine Manager может содержать узлы Hyper-V под управлением разных версий Windows Server, но политика репликации зависит от версии ОС. Если вы используете узлы на разных версиях операционных систем, создайте отдельные политики репликации для каждой системы. Например, если у вас есть пять узлов, работающих с Windows Server 2012, и три — с Windows Server 2012 R2, создайте две политики репликации. По одной для каждого типа ОС.

2. Получите первичный контейнер защиты (основного облака Virtual Machine Manager) и контейнер защиты для восстановления (облако Virtual Machine Manager восстановления).

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
3. Извлеките созданную политику репликации, используя понятное имя.

       $policy = Get-AzSiteRecoveryPolicy -FriendlyName $policyname
4. Запустите связывание контейнера защиты (облака Virtual Machine Manager) с политикой репликации.

       $associationJob  = Start-AzSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
5. Дождитесь завершения задания связывания политики. Используйте приведенный ниже фрагмент кода PowerShell, чтобы проверить, завершено ли задание.

       $job = Get-AzSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

6. После того, как задание завершило обработку, выполните следующую команду:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Для проверки выполнения операции выполните действия, описанные в разделе [Мониторинг действий](#monitor-activity).

##  <a name="configure-network-mapping"></a>Настройка сетевого сопоставления
1. Используйте эту команду, чтобы получить серверы для текущего хранилища. Эта команда сохраняет серверы Site Recovery в массиве $Servers.

        $Servers = Get-AzSiteRecoveryServer
2. Чтобы извлечь сети для исходного и целевого серверов Virtual Machine Manager, выполните следующую команду:

        $PrimaryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > Исходный сервер Virtual Machine Manager может быть первым или вторым в массиве серверов. Проверьте имена серверов Virtual Machine Manager и получите сети соответствующим образом.


3. Этот командлет создает сопоставление между основной сетью и сетью восстановления. Он устанавливает основную сеть в качестве первого элемента $PrimaryNetworks. Он устанавливает сеть восстановления в качестве первого элемента $RecoveryNetworks.

        New-AzSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>Включение защиты для виртуальных машин
После успешной настройки серверов, облаков и сетей можно включить защиту для виртуальных машин в облаке.

1. Чтобы включить защиту, выполните следующую команду для получения контейнера защиты.

          $PrimaryProtectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Получите сущность защиты (виртуальной машины) следующим образом:

           $protectionEntity = Get-AzSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Включите репликацию для виртуальной машины.

          $jobResult = Set-AzSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>Запуск тестовой отработки отказа

Чтобы протестировать развертывание, запустите тестовую отработку отказа для одной виртуальной машины. Также можно создать план восстановления, который содержит несколько виртуальных машин, и запустить для него тестовую отработку отказа. Тестовая обработка отказа имитирует механизм отработки отказа и восстановления в изолированной сети.

1. Получите виртуальную машину, куда будет выполнена отработка отказа виртуальных машин.

       $Servers = Get-AzSiteRecoveryServer
       $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

2. Выполните тестовую отработка отказа.

   Для одной виртуальной машины:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
   Для плана восстановления:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

Для проверки выполнения операции выполните действия, описанные в разделе [Мониторинг действий](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Запуск плановых и внеплановых отработок отказа

1. Выполните плановую отработку отказа.

   Для одной виртуальной машины:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Для плана восстановления:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

2. Выполните внеплановую отработку отказа.

   Для одной виртуальной машины:
        
        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Для плана восстановления:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>Мониторинг действий
Используйте следующие команды для мониторинга действий отработки отказа. Дождитесь завершения обработки, прежде чем запускать следующие задания.

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

    if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>Дальнейшие действия

[Узнайте больше](/powershell/module/az.recoveryservices) об использовании командлетов PowerShell Resource Manager для службы Site Recovery.
