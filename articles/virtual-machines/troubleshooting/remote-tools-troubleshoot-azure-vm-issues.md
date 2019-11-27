---
title: Использование инструментов удаленного управления для устранения неполадок виртуальной машины Azure | Документация Майкрософт
description: ''
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
ms.openlocfilehash: 3f028431fcd4b338d2e610ce1828a02b753c4d32
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483703"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Использование инструментов удаленного управления для устранения неполадок виртуальной машины Azure

При устранении неполадок в виртуальной машине Azure можно подключиться к ВИРТУАЛЬНОЙ машине с помощью средств удаленной проверки, описанных в этой статье, вместо использования протокол удаленного рабочего стола (RDP).

## <a name="serial-console"></a>Последовательная консоль

Используйте [последовательную консоль для виртуальных машин Azure](serial-console-windows.md) , чтобы выполнять команды на удаленной виртуальной машине Azure.

## <a name="remote-cmd"></a>Удаленный сеанс командной строки

Скачайте [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Подключитесь к виртуальной машине с помощью следующей команды.

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* Команда должна выполняться на компьютере, который находится в той же виртуальной сети.
>* Для замены \<> компьютера можно использовать DIP или HostName.
>* Параметр -s гарантирует, что команда вызывается с помощью системной учетной записи (с разрешениями администратора).
>* PsExec использует TCP-порты 135 и 445. В результате два порта должны быть открыты в брандмауэре.

## <a name="run-command"></a>Команда "Выполнить"

Дополнительные сведения об использовании функции выполнить команду для выполнения скриптов на виртуальной машине см. [в статье запуск сценариев PowerShell в виртуальной машине Windows с помощью команды Run](../windows/run-command.md).

## <a name="custom-script-extension"></a>Расширение пользовательских сценариев

Можно использовать функцию расширения пользовательских сценариев для выполнения пользовательского сценария на целевой виртуальной машине. Для этого должны быть выполнены следующие условия.

* Виртуальная машина доступна в сети.
* Агент виртуальной машины Azure установлен и работает должным образом на виртуальной машине.
* Расширение не было ранее установлено на виртуальной машине.
 
  Расширение внедряет скрипт только при первом использовании. Если использовать эту функцию позднее, расширение распознает, что оно уже использовалось и не отправит новый скрипт.

Отправьте скрипт в учетную запись хранения и создайте собственный контейнер. Затем выполните приведенный ниже сценарий в Azure PowerShell на компьютере, который может подключиться к виртуальной машине.

### <a name="for-classic-deployment-model-vms"></a>Для виртуальных машин классической модели развертывания

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

### <a name="for-azure-resource-manager-vms"></a>Для Azure Resource Manager виртуальных машин

 

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
>Для Azure Resource Manager виртуальных машин необходимо открыть порт 5986 в группе безопасности сети (NSG). Чтобы получить дополнительные сведения, ознакомьтесь с группами безопасности. 
>
>Для виртуальных машин RDFE необходима конечная точка с частным портом (5986) и общедоступным портом. Затем также необходимо открыть общедоступный порт на NSG.

### <a name="set-up-the-client-computer"></a>Настройка клиентского компьютера

Чтобы использовать PowerShell для удаленного подключения к виртуальной машине, необходимо сначала настроить клиентский компьютер, чтобы разрешить такое подключение. Для этого добавьте виртуальную машину в список доверенных узлов PowerShell, выполнив приведенную ниже команду соответствующим образом.

Чтобы добавить одну виртуальную машину в список надежных узлов, выполните следующие действия.

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Чтобы добавить несколько виртуальных машин в список надежных узлов, выполните следующие действия.

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Вот как можно добавить все компьютеры в список доверенных узлов.

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Включение RemotePS на виртуальной машине

Для виртуальных машин, созданных с помощью классической модели развертывания, используйте расширение пользовательских сценариев, чтобы выполнить следующий скрипт:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

Для Azure Resource Manager виртуальных машин используйте команды Run на портале для запуска скрипта Енаблеремотепс:

![Команда "Выполнить"](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Подключение к виртуальной машине

Выполните следующую команду на основе расположения клиентского компьютера:

* За пределами виртуальной сети или развертывания

  * Для виртуальной машины, созданной с помощью классической модели развертывания, выполните следующую команду:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Для виртуальной машины Azure Resource Manager сначала добавьте DNS-имя в общедоступный IP-адрес. Подробные инструкции приведены в статье [Создание полного доменного имени на портале Azure для виртуальной машины Windows](../windows/portal-create-fqdn.md). Затем выполните следующую команду:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* В виртуальной сети или развертывании выполните следующую команду:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>Если установить флаг SkipCaCheck, то можно обойти обязательный импорт сертификата на виртуальную машину при запуске сеанса.

Можно также использовать командлет Invoke-Command для удаленного выполнения сценария на виртуальной машине.

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Удаленный реестр

>[!NOTE]
>TCP-порты 135 и 445 должны быть открыты для использования этой функции.
>
>Для Azure Resource Manager виртуальных машин необходимо открыть порт 5986 на NSG. Чтобы получить дополнительные сведения, ознакомьтесь с группами безопасности. 
>
>Для виртуальных машин RDFE необходима конечная точка с частным портом (5986) и общедоступным портом. Также необходимо открыть общедоступный порт на NSG.

1. Из другой виртуальной машины в той же виртуальной сети откройте редактор реестра (regedit. exe).

2. Выберите **Файл** > **Подключить сетевой реестр**.

   ![Редактор реестра](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Выберите целевую виртуальную машину по **имени узла** или **динамическому IP-адресу** (предпочтительно), введя его в поле **введите имя объекта для выбора** .

   ![Введите имя объекта в поле выбора](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Введите учетные данные для целевой виртуальной машины.

5. Внесите в реестр необходимые изменения.

## <a name="remote-services-console"></a>Консоль удаленных служб

>[!NOTE]
>TCP-порты 135 и 445 должны быть открыты для использования этой функции.
>
>Для Azure Resource Manager виртуальных машин необходимо открыть порт 5986 на NSG. Чтобы получить дополнительные сведения, ознакомьтесь с группами безопасности. 
>
>Для виртуальных машин RDFE необходима конечная точка с частным портом (5986) и общедоступным портом. Также необходимо открыть общедоступный порт на NSG.

1. На другой виртуальной машине в той же виртуальной сети откройте экземпляр **Services. msc**.

2. Щелкните правой кнопкой мыши **Службы (локальные)** .

3. Выберите **Connect to another computer** (Подключение к другому компьютеру).

   ![Удаленная служба](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Введите динамический IP-адрес целевой виртуальной машины.

   ![Динамический ввод IP-адреса](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Внесите необходимые изменения в службы.

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о командлете Enter-PSSession см. в разделе [Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx).
- Дополнительные сведения о расширении пользовательских скриптов для Windows с помощью классической модели развертывания см. в разделе [расширение пользовательских скриптов для Windows](../extensions/custom-script-classic.md).
- PsExec является частью [набора PSTools](https://download.sysinternals.com/files/PSTools.zip).
- Дополнительные сведения о пакете PSTools см. в разделе [PsTools](https://docs.microsoft.com/sysinternals/downloads/pstools).


