---
title: Использование инструментов удаленного управления для устранения неполадок виртуальной машины Azure | Документация Майкрософт
description: Узнайте о скриптах PsExec, PowerShell и других удаленных инструментах, которые можно использовать для устранения проблем удаленного Управления по удалению Azure VM без использования RDP.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: d29b2b7c2b9194f20afe4c74d117847f0e343b12
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422607"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Использование инструментов удаленного управления для устранения неполадок виртуальной машины Azure

При устранении неполадок на виртуальном компьютере Azure (VM) можно подключиться к VM с помощью удаленных инструментов, которые обсуждаются в этой статье, вместо использования протокола удаленного рабочего стола (RDP).

## <a name="serial-console"></a>Последовательная консоль

Используйте [серийную консоль для виртуальных машин Azure](serial-console-windows.md) для выполнения команд на удаленном VM Azure.

## <a name="remote-cmd"></a>Удаленный сеанс командной строки

Скачайте [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Подключитесь к виртуальной машине с помощью следующей команды.

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* Команда должна быть запущена на компьютере, который в той же виртуальной сети.
>* DIP или HostName могут \<быть использованы для замены> компьютера.
>* Параметр -s гарантирует, что команда вызывается с помощью системной учетной записи (с разрешениями администратора).
>* PsExec использует TCP-порты 135 и 445. В результате два порта должны быть открыты на брандмауэре.

## <a name="run-command"></a>Команда "Выполнить"

Для получения дополнительной информации о том, как использовать функцию команды run для выполнения скриптов на VM, [см.](../windows/run-command.md)

## <a name="custom-script-extension"></a>Расширение пользовательских сценариев

Можно использовать функцию расширения пользовательских сценариев для выполнения пользовательского сценария на целевой виртуальной машине. Для этого должны быть выполнены следующие условия.

* Виртуальная машина доступна в сети.
* Устанавливается виртуальный машинный агент Azure, который работает, как и ожидалось, на VM.
* Расширение не было ранее установлено на VM.
 
  Расширение вводит скрипт только при первом использовании. Если вы используете эту функцию позже, расширение признает, что она уже была использована, и не загружает новый скрипт.

Загрузите свой скрипт в учетную запись хранения и создайте свой собственный контейнер. Затем выполните приведенный ниже сценарий в Azure PowerShell на компьютере, который может подключиться к виртуальной машине.

### <a name="for-classic-deployment-model-vms"></a>Для классических моделей развертывания VMs

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group or cloud service where the VM is hosted. For example, for "demo305.cloudapp.net" the cloud service is going to be demo305.

#Set up the Azure PowerShell module, and ensure the access to the subscription.
Import-Module Azure
Add-AzureAccount  #Ensure login with the account associated with the subscription ID.
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-azure-resource-manager-vms"></a>Для менеджеров ресурсов Azure

 

```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Set up the Azure PowerShell module, and ensure the access to the subscription.
Login-AzAccount #Ensure login with the account associated with the subscription ID.
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>Удаленный сеанс PowerShell

>[!NOTE]
>Необходимо открыть TCP-порт 5986 (HTTPS), чтобы можно было использовать эту возможность.
>
>Для управления ресурсами Azure Необходимо открыть порт 5986 в группе сетевой безопасности (NSG). Чтобы получить дополнительные сведения, ознакомьтесь с группами безопасности. 
>
>Для виртуальных машин RDFE необходима конечная точка с частным портом (5986) и общедоступным портом. Затем, вы также должны открыть, что общественные перед порт омск на NSG.

### <a name="set-up-the-client-computer"></a>Настройка клиентского компьютера

Чтобы использовать PowerShell для удаленного подключения к виртуальной машине, необходимо сначала настроить клиентский компьютер, чтобы разрешить такое подключение. Для этого добавьте виртуальную машину в список доверенных узлов PowerShell, выполнив приведенную ниже команду соответствующим образом.

Чтобы добавить один VM в список доверенных хостов:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Чтобы добавить несколько ВМ в список доверенных хостов:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Вот как можно добавить все компьютеры в список доверенных узлов.

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Включение RemotePS на виртуальной машине

Для вд, созданные с использованием классической модели развертывания, используйте расширение пользовательского сценария для выполнения следующего сценария:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

Для m-проектов управления ресурсами Azure используйте команды с портала для запуска сценария EnableRemotePS:

![Команда "Выполнить"](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Подключение к виртуальной машине

Выполнить следующую команду на основе местоположения компьютера клиента:

* Вне виртуальной сети или развертывания

  * Для VM, созданного с использованием классической модели развертывания, запустите следующую команду:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Для VM-менеджера ресурсов Azure сначала добавьте имя DNS в общедоступный IP-адрес. Подробные инструкции приведены в статье [Создание полного доменного имени на портале Azure для виртуальной машины Windows](../windows/portal-create-fqdn.md). Затем выполните следующую команду:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* Внутри виртуальной сети или развертывания запустите следующую команду:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>Если установить флаг SkipCaCheck, то можно обойти обязательный импорт сертификата на виртуальную машину при запуске сеанса.

Вы также можете использовать cmdlet Invoke-Command для удаленного выполнения сценария на VM.

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Удаленный реестр

>[!NOTE]
>TCP-порты 135 и 445 должны быть открыты для использования этой функции.
>
>Для vMs-ресурсов Azure, необходимо открыть порт 5986 на NSG. Чтобы получить дополнительные сведения, ознакомьтесь с группами безопасности. 
>
>Для виртуальных машин RDFE необходима конечная точка с частным портом (5986) и общедоступным портом. Вы также должны открыть этот открытый порт на NSG.

1. От другого VM в той же виртуальной сети, откройте редактор реестра (regedit.exe).

2. Выберите**реестр сетей** **File** > Connect .

   ![Редактор реестра](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Найдите целевой VM по **имени хоста** или **динамическому IP** (предпочтительно), введя ее в **имя объекта Enter, чтобы выбрать** поле.

   ![Введите имя объекта, чтобы выбрать поле](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Введите учетные данные для целевой виртуальной машины.

5. Внесите в реестр необходимые изменения.

## <a name="remote-services-console"></a>Консоль удаленных служб

>[!NOTE]
>TCP-порты 135 и 445 должны быть открыты для использования этой функции.
>
>Для vMs-ресурсов Azure, необходимо открыть порт 5986 на NSG. Чтобы получить дополнительные сведения, ознакомьтесь с группами безопасности. 
>
>Для виртуальных машин RDFE необходима конечная точка с частным портом (5986) и общедоступным портом. Вы также должны открыть этот открытый порт на NSG.

1. От другого VM в той же виртуальной сети, откройте экземпляр **Services.msc**.

2. Щелкните правой кнопкой мыши **Службы (локальные)**.

3. Выберите **Connect to another computer** (Подключение к другому компьютеру).

   ![Удаленная служба](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Введите динамический IP целевого VM.

   ![Динамика ввода IP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Внесите необходимые изменения в службы.

## <a name="next-steps"></a>Следующие шаги

- Для получения дополнительной информации о Смдлет Enter-PSSession [см.](https://technet.microsoft.com/library/hh849707.aspx)
- Для получения дополнительной информации о расширении пользовательского сценария для Windows с использованием классической модели развертывания [см.](../extensions/custom-script-classic.md)
- PsExec является частью [набора PSTools](https://download.sysinternals.com/files/PSTools.zip).
- Для получения дополнительной информации о PSTools Suite, [см.](https://docs.microsoft.com/sysinternals/downloads/pstools)


