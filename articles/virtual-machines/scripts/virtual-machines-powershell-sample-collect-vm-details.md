---
title: Сбор сведений обо всех виртуальных машинах в подписке с помощью PowerShell
description: Сбор сведений обо всех виртуальных машинах в подписке с помощью PowerShell
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
ms.openlocfilehash: a45f0a882c58c7035badcc1270c66bd9c6fb252a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86526917"
---
# <a name="collect-details-about-all-vms-in-a-subscription-with-powershell"></a>Сбор сведений обо всех виртуальных машинах в подписке с помощью PowerShell

Этот скрипт создает CSV-файл, содержащий имя и размер виртуальной машины, имя группы ресурсов, частный IP-адрес, сведения о регионе, виртуальной сети, подсети и типе ОС, а также общедоступный IP-адрес виртуальных машин в указанной подписке.

Если у вас еще нет [подписки Azure](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free), прежде чем начать работу.

## <a name="launch-azure-cloud-shell"></a>Запуск Azure Cloud Shell

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. 

Чтобы открыть Cloud Shell, просто выберите **Попробовать** в правом верхнем углу блока кода. Cloud Shell можно также запустить в отдельной вкладке браузера, перейдя на страницу [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Нажмите кнопку **Копировать**, чтобы скопировать блоки кода. Вставьте код в Cloud Shell и нажмите клавишу "ВВОД", чтобы выполнить его.

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
    $info = "" | Select VmName, ResourceGroupName, Region, VmSize, VirturalNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress 
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
        $info.VmSize = $vm.HardwareProfile.VmSize
        $info.VirturalNetwork = $nic.IpConfigurations.subnet.Id.Split("/")[-3] 
        $info.Subnet = $nic.IpConfigurations.subnet.Id.Split("/")[-1] 
        $info.PrivateIpAddress = $nic.IpConfigurations.PrivateIpAddress 
        $report+=$info 
    } 
$report | ft VmName, ResourceGroupName, Region, VmSize, VirturalNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress 
$report | Export-CSV "$home/$reportName"
```

## <a name="script-explanation"></a>Описание скрипта
Этот скрипт использует следующие команды для экспорта сведений о виртуальных машинах в подписке в CSV-файл. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

|Get-Help|Примечания|
|-|-|
|[Select-AzSubscription](/powershell/module/az.accounts/set-azcontext)|Задает клиента, подписку и среду, которые будут использовать командлеты в текущем сеансе.|
|[Get-AzVM](/powershell/module/az.compute/get-azvm)|Возвращает свойства виртуальной машины.|
|[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)|Возвращает общедоступный IP-адрес.|
|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface)|Возвращает сетевой интерфейс.|

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для виртуальных машин представлены в [документации по виртуальным машинам Azure под управлением Windows](../windows/powershell-samples.md?toc=/azure/virtual-machines/windows/toc.json).
