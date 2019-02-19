---
title: Руководство. Создание виртуальных машин с выполняемым стеком SQL, IIS и .NET в Azure | Документация Майкрософт
description: В этом руководстве описано, как установить стек SQL, IIS и .NET Azure на виртуальной машине Windows в Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: aa8f4e188761c50391cd2ead49ae8d8b9081188f
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55983497"
---
# <a name="tutorial-install-the-sql-iis-net-stack-in-a-windows-vm-with-azure-powershell"></a>Руководство. Установка стека SQL, IIS и .NET на виртуальной машине Windows с помощью Azure PowerShell

Используя это руководство, вы установите стек SQL, IIS и .NET с помощью Azure PowerShell. Этот стек состоит из двух виртуальных машин на базе Windows Server 2016: одной с IIS и .NET, а другой с SQL Server.

> [!div class="checklist"]
> * Создание виртуальной машины 
> * Установка пакетов SDK для IIS и .NET Core на виртуальной машине
> * создание виртуальной машины с SQL Server;
> * установка расширения SQL Server.

## <a name="launch-azure-cloud-shell"></a>Запуск Azure Cloud Shell

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. 

Чтобы открыть Cloud Shell, просто выберите **Попробовать** в правом верхнем углу блока кода. Cloud Shell можно также запустить в отдельной вкладке браузера, перейдя на страницу [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Нажмите кнопку **Копировать**, чтобы скопировать блоки кода. Вставьте код в Cloud Shell и нажмите клавишу "ВВОД", чтобы выполнить его.

## <a name="create-a-iis-vm"></a>Создание виртуальной машины с IIS 

В этом примере мы воспользуемся командлетом [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) в Cloud Shell для PowerShell, чтобы быстро создать виртуальную машину под управлением Windows Server 2016, а затем установим IIS и .NET Framework. Виртуальные машины с IIS и SQL используют одну группу ресурсов и виртуальную сеть, поэтому мы создадим переменные для этих имен.


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name $vmName `
    -Location "East US" `
    -VirtualNetworkName $vNetName `
    -SubnetName "myIISSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -AddressPrefix 192.168.0.0/16 `
    -PublicIpAddressName "myIISPublicIpAddress" `
    -OpenPorts 80,3389 
```

Установите IIS и .NET Framework, используя расширение пользовательского скрипта и командлет [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension).

```azurepowershell-interactive
Set-AzVMExtension `
    -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="create-another-subnet"></a>Создание другой подсети

Создайте вторую подсеть для виртуальной машины SQL. Получите виртуальную сеть, выполнив команду [Get-AzVirtualNetwork]{/powershell/module/az.network/get-azvirtualnetwork}.

```azurepowershell-interactive
$vNet = Get-AzVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

Создайте конфигурацию подсети с попощью командлета [Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig).


```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

Укажите для виртуальной сети сведения о новой подсети, выполнив командлет [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork).
   
```azurepowershell-interactive   
$vNet | Set-AzVirtualNetwork
```

## <a name="azure-sql-vm"></a>Виртуальная машина с SQL Azure

Для создания виртуальной машины c SQL используйте предварительно настроенный образ SQL Server из Azure Marketplace. Сначала мы создадим виртуальную машину, а затем установим на нее расширение SQL Server. 


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name "mySQLVM" `
    -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
    -Location eastus `
    -VirtualNetworkName $vNetName `
    -SubnetName "mySQLSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "mySQLPublicIpAddress" `
    -OpenPorts 3389,1401 
```

Чтобы добавить [расширение SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) на виртуальную машину SQL, используйте командлет [Set-AzVMSqlServerExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsqlserverextension).

```azurepowershell-interactive
Set-AzVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы установили стек для SQL, IIS и .NET с помощью Azure PowerShell. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Создание виртуальной машины 
> * Установка пакетов SDK для IIS и .NET Core на виртуальной машине
> * создание виртуальной машины с SQL Server;
> * установка расширения SQL Server.

Перейдите к следующему руководству, чтобы узнать, как защитить веб-сервер IIS с помощью SSL-сертификатов.

> [!div class="nextstepaction"]
> [Защита веб-сервера IIS с помощью SSL-сертификатов](tutorial-secure-web-server.md)

