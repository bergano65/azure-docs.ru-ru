---
title: Использование инструментов удаленного управления для устранения неполадок виртуальной машины Azure | Документация Майкрософт
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: 2126ed2624d735d88d52ddc1ee97bfb970cc8a74
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58119774"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Использование инструментов удаленного управления для устранения неполадок виртуальной машины Azure

При устранении неполадок на виртуальной машине Azure можно подключиться к ней с помощью инструментов удаленного управления, которые рассматриваются в этой статье, а не использовать протокол удаленного рабочего стола (RDP).

## <a name="serial-console"></a>Серийная консоль

Используйте [Серийную консоль виртуальной машины](serial-console-windows.md) для выполнения команд на удаленной виртуальной машине Azure.

## <a name="remote-cmd"></a>Удаленный сеанс командной строки

Скачайте [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Подключитесь к виртуальной машине с помощью следующей команды.

```cmd
psexec \\<computer>-u user -s cmd
```

>[!Note]
>* Команда должна выполняться на компьютере, который находится в той же виртуальной сети.
>* Вместо <computer> можно указать DIP-адрес или имя узла.
>* Параметр -s гарантирует, что команда вызывается с помощью системной учетной записи (с разрешениями администратора).
>* PsExec использует TCP-порты 135 и 445. Поэтому эти два порта должны быть открыты в брандмауэре.

## <a name="run-commands"></a>Команды запуска

В разделе [Выполнение сценариев PowerShell в виртуальной машине Windows с помощью команды "Выполнить"](../windows/run-command.md) приведены дополнительные сведения о том, как использовать функцию "Команды запуска" для выполнения сценариев на виртуальной машине.

## <a name="customer-script-extension"></a>Расширение пользовательских сценариев

Можно использовать функцию расширения пользовательских сценариев для выполнения пользовательского сценария на целевой виртуальной машине. Для этого должны быть выполнены следующие условия.

* Виртуальная машина доступна в сети.

* На виртуальной машине установлен и правильно работает агент Azure.

* Расширение не было ранее установлено на виртуальной машине.
 
  Расширение будет внедрять сценарий только при первом использовании. Если вы используете эту функцию позже, расширение определит, что оно уже использовалось, и не передаст новый сценарий.

Нужно передать сценарий в учетную запись хранения и создать для него контейнер. Затем выполните приведенный ниже сценарий в Azure PowerShell на компьютере, который может подключиться к виртуальной машине.

### <a name="for-v1-vms"></a>Для виртуальных машин версии 1

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group/Cloud Service where the VM is hosted. I.E.: For "demo305.cloudapp.net" the cloud service is going to be demo305

#Setup the Azure Powershell module and ensure the access to the subscription
Import-Module Azure
Add-AzureAccount  #Ensure Login with account associated with subscription ID
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Setup the access to the storage account and upload the script
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-v2-vms"></a>Для виртуальных машин версии 2

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for blob in storage
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Setup the Azure Powershell module and ensure the access to the subscription
Login-AzAccount #Ensure Login with account associated with subscription ID
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Setup the access to the storage account and upload the script 
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>Удаленный сеанс PowerShell

>[!Note]
>Необходимо открыть TCP-порт 5986 (HTTPS), чтобы можно было использовать эту возможность.
>
>Для виртуальных машин ARM необходимо открыть порт 5986 в группе безопасности сети (NSG). Чтобы получить дополнительные сведения, ознакомьтесь с группами безопасности. 
>
>Для виртуальных машин RDFE необходима конечная точка с частным портом (5986) и общедоступным портом. Затем необходимо также открыть этот общедоступный порт в группе безопасности сети.

### <a name="set-up-the-client-computer"></a>Настройка клиентского компьютера

Чтобы использовать PowerShell для удаленного подключения к виртуальной машине, необходимо сначала настроить клиентский компьютер, чтобы разрешить такое подключение. Для этого добавьте виртуальную машину в список доверенных узлов PowerShell, выполнив приведенную ниже команду соответствующим образом.

Вот как можно добавить одну виртуальную машину в список доверенных узлов.

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Вот как можно добавить в список доверенных узлов несколько виртуальных машин.

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Вот как можно добавить все компьютеры в список доверенных узлов.

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Включение RemotePS на виртуальной машине

Для классических виртуальных машин используйте расширение пользовательских сценариев, чтобы выполнить приведенный ниже сценарий.

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

Для виртуальных машин ARM используйте функцию "Команды запуска" на портале, чтобы выполнить сценарий EnableRemotePS.

![Запуск команды](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Подключение к виртуальной машине

Выполните следующую команду с учетом расположения клиентского компьютера.

* За пределами виртуальной сети или развертывания

  * Для классической виртуальной машины выполните следующую команду.

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Для виртуальной машины ARM сначала добавьте DNS-имя в общедоступный IP-адрес. Подробные инструкции приведены в статье [Создание полного доменного имени на портале Azure для виртуальной машины Windows](../windows/portal-create-fqdn.md). Затем выполните следующую команду:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* За пределами виртуальной сети или развертывания выполните следующую команду.
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!Note] 
>Если установить флаг SkipCaCheck, то можно обойти обязательный импорт сертификата на виртуальную машину при запуске сеанса.

Также можно использовать командлет Invoke-Command для удаленного выполнения сценария на виртуальной машине.

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Удаленный реестр

>[!Note]
>TCP-порты 135 и 445 должны быть открыты для использования этой функции.
>
>Для виртуальных машин ARM необходимо открыть порт 5986 в группе безопасности сети. Чтобы получить дополнительные сведения, ознакомьтесь с группами безопасности. 
>
>Для виртуальных машин RDFE необходима конечная точка с частным портом (5986) и общедоступным портом. Кроме того, необходимо открыть этот общедоступный порт в группе безопасности сети.

1. На другой виртуальной машине в той же виртуальной сети откройте редактор реестра (regedit.exe).

2. Выберите **Файл** >**Подключить сетевой реестр**.

   ![Параметры удаленной работы](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Найдите целевую виртуальную машину по **имени узла** или **динамическому IP-адресу** (предпочтительно), введя его в поле "Enter the object name to select" (Введите имя выбираемого объекта).

   ![Параметры удаленной работы](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Введите учетные данные для целевой виртуальной машины.

5. Внесите в реестр необходимые изменения.

## <a name="remote-services-console"></a>Консоль удаленных служб

>[!Note]
>TCP-порты 135 и 445 должны быть открыты для использования этой функции.
>
>Для виртуальных машин ARM необходимо открыть порт 5986 в группе безопасности сети. Чтобы получить дополнительные сведения, ознакомьтесь с группами безопасности. 
>
>Для виртуальных машин RDFE необходима конечная точка с частным портом (5986) и общедоступным портом. Затем необходимо также открыть этот общедоступный порт в группе безопасности сети.

1. На другой виртуальной машине в той же виртуальной сети откройте экземпляр **Services.msc**.

2. Щелкните правой кнопкой мыши **Службы (локальные)**.

3. Выберите **Connect to another computer** (Подключение к другому компьютеру).

   ![Удаленная служба](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Введите динамический IP-адрес целевой виртуальной машины.

   ![Ввод DIP-адреса](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Внесите необходимые изменения в службы.

## <a name="next-steps"></a>Следующие шаги

[Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx)

[Расширение пользовательских сценариев для Windows с помощью классической модели развертывания](../extensions/custom-script-classic.md)

PsExec является частью [набора PSTools](https://download.sysinternals.com/files/PSTools.zip).

Дополнительные сведения о наборе PSTools см. в разделе [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).


