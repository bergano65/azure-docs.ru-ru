---
title: Сбор сведений о всех виртуальных машин в подписке с помощью PowerShell | Документация Майкрософт
description: Сбор сведений о всех виртуальных машин в подписке с помощью PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: v-miegge
manager: ???
editor: v-miegge
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/01/2019
ms.author: v-miegge
ms.custom: mvc
ms.openlocfilehash: b4828b34a089a59e630aaaf7652e8623b3e2c7b8
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659714"
---
# <a name="collect-details-about-all-vms-in-a-subscription-with-powershell"></a>Сбор сведений о всех виртуальных машин в подписке с помощью PowerShell

Этот скрипт создает CSV-файл, содержащий имя, группа ресурсов виртуальной Машины имя, регион, виртуальной сети, подсети, частный IP-адрес, тип операционной системы и общедоступный IP-адрес из виртуальных машин в выбранной подписке.

Если у вас еще нет [подписки Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free), прежде чем начать работу.

## <a name="launch-azure-cloud-shell"></a>Запуск Azure Cloud Shell

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. 

Чтобы открыть Cloud Shell, просто выберите **Попробовать** в правом верхнем углу блока кода. Cloud Shell можно также запустить в отдельной вкладке браузера, перейдя на страницу [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Нажмите кнопку **Копировать**, чтобы скопировать блоки кода. Вставьте код в Cloud Shell и нажмите клавишу "ВВОД", чтобы выполнить его.

## <a name="sample-script"></a>Пример скрипта

```azurepowershell-interactive
#Provide the subscription Id where the VMs reside
$subscriptionId = "ea7ded4e-153a-4e65-ad70-25bf9f7b91bc"

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
Этот скрипт использует следующие команды для создания Экспорт в формат csv различными аспектами виртуальных машин в подписке. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

|Command|Примечания|
|-|-|
|[Выберите AzSubscription](https://docs.microsoft.com/powershell/module/Az.Accounts/Set-AzContext)|Задает клиента, подписку и среды для командлетов для использования в текущем сеансе.|
|[Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM)|Возвращает свойства виртуальной машины.|
|[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/Az.Network/Get-AzPublicIpAddress)|Возвращает общедоступный IP-адрес.|
|[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/Az.Network/Get-AzNetworkInterface)|Получает сетевого интерфейса.|

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для виртуальных машин представлены в [документации по виртуальным машинам Azure под управлением Windows](https://docs.microsoft.com/azure/virtual-machines/windows/powershell-samples?toc=/azure/virtual-machines/windows/toc.json).


