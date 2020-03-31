---
title: Сбор информации обо всех вс-маховикв в подписке с PowerShell
description: Сбор информации обо всех вс-маховикв в подписке с PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: v-miegge
manager: dcscontentpm
editor: v-miegge
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/01/2019
ms.author: v-miegge
ms.custom: mvc
ms.openlocfilehash: 237081380445f2b2e4168ee3afe9a3ed7544fc89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900196"
---
# <a name="collect-details-about-all-vms-in-a-subscription-with-powershell"></a>Сбор информации обо всех вс-маховикв в подписке с PowerShell

Этот скрипт создает csv, который содержит имя VM, название группы ресурсов, регион, Виртуальную сеть, Subnet, частный IP-адрес, тип ОС и публичный IP-адрес виртуальных адресов в предоставленной подписке.

Если у вас нет [подписки Azure,](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)создайте [бесплатную учетную запись](https://azure.microsoft.com/free) перед началом.

## <a name="launch-azure-cloud-shell"></a>Запуск Azure Cloud Shell

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. 

Чтобы открыть Cloud Shell, просто выберите **Попробовать** в правом верхнем углу блока кода. Вы также можете запустить Cloud Shell в [https://shell.azure.com/powershell](https://shell.azure.com/powershell)отдельной вкладке браузера, перейдя к . Выберите **Copy,** чтобы скопировать блоки кода, вставьте его в облачную оболочку и нажмите введите, чтобы запустить его.

## <a name="sample-script"></a>Пример скрипта

```azurepowershell-interactive
#Provide the subscription Id where the VMs reside
$subscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

#Provide the name of the csv file to be exported
$reportName = "myReport.csv"

Select-AzSubscription $subscriptionId
$report = @()
$vms = Get-AzVM
$publicIps = Get-AzPublicIpAddress 
$nics = Get-AzNetworkInterface | ?{ $_.VirtualMachine -NE $null} 
foreach ($nic in $nics) { 
    $info = "" | Select VmName, ResourceGroupName, Region, VirturalNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress 
    $vm = $vms | ? -Property Id -eq $nic.VirtualMachine.id 
    foreach($publicIp in $publicIps) { 
        if($nic.IpConfigurations.id -eq $publicIp.ipconfiguration.Id) {
            $info.PublicIPAddress = $publicIp.ipaddress
            } 
        } 
        $info.OsType = $vm.StorageProfile.OsDisk.OsType 
        $info.VMName = $vm.Name 
        $info.ResourceGroupName = $vm.ResourceGroupName 
        $info.Region = $vm.Location 
        $info.VirturalNetwork = $nic.IpConfigurations.subnet.Id.Split("/")[-3] 
        $info.Subnet = $nic.IpConfigurations.subnet.Id.Split("/")[-1] 
        $info.PrivateIpAddress = $nic.IpConfigurations.PrivateIpAddress 
        $report+=$info 
    } 
$report | ft VmName, ResourceGroupName, Region, VirturalNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress 
$report | Export-CSV "$home/$reportName"
```

## <a name="script-explanation"></a>Описание скрипта
Этот скрипт использует следующие команды для создания csv экспорта деталей VMs в подписке. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

|Команда|Примечания|
|-|-|
|[Выберите-AzSubscription](https://docs.microsoft.com/powershell/module/Az.Accounts/Set-AzContext)|Устанавливает арендатор, подписку и среду для использования cmdlets в текущем сеансе.|
|[Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM)|Возвращает свойства виртуальной машины.|
|[Get-AzPublicIpАдрес](https://docs.microsoft.com/powershell/module/Az.Network/Get-AzPublicIpAddress)|Получает общедоступный IP-адрес.|
|[Get-AzNetworkИнтерфейс](https://docs.microsoft.com/powershell/module/Az.Network/Get-AzNetworkInterface)|Получает сетевой интерфейс.|

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для виртуальных машин представлены в [документации по виртуальным машинам Azure под управлением Windows](https://docs.microsoft.com/azure/virtual-machines/windows/powershell-samples?toc=/azure/virtual-machines/windows/toc.json).


