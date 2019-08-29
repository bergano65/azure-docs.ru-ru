---
title: Создание виртуальной машины Windows с помощью шаблона в Azure | Документация Майкрософт
description: Простое создание виртуальной машины Windows с помощью шаблона Resource Manager и PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7e1f50753f155d1583de3a1e8426975e1b0d6aee
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102519"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Создание виртуальной машины Windows с использованием шаблона Resource Manager

Узнайте, как создать виртуальную машину Windows с помощью шаблона Azure Resource Manager и Azure PowerShell из Azure Cloud Shell. Шаблон, используемый в этой статье, развертывает одну виртуальную машину под Windows Server в новой виртуальной сети с одной подсетью. Сведения о создании виртуальной машины Linux см. [в статье Создание виртуальной машины Linux с помощью шаблонов Azure Resource Manager](../linux/create-ssh-secured-vm-from-template.md).

## <a name="create-a-virtual-machine"></a>Создать виртуальную машину

Создание виртуальной машины Azure обычно включает в себя два этапа:

- Создайте группу ресурсов. Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. Группу ресурсов следует создавать до виртуальной машины.
- Создайте виртуальную машину.

В следующем примере создается виртуальная машина на основе шаблона быстрого запуска [Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json). Ниже приведена копия шаблона:

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

Чтобы запустить сценарий PowerShell, выберите " **попробовать** ", чтобы открыть Azure Cloud Shell. Чтобы вставить скрипт, щелкните оболочку правой кнопкой мыши и выберите команду **Вставить**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter an unique DNS name for the public IP"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" `
    -adminUsername $adminUsername `
    -adminPassword $adminPassword `
    -dnsLabelPrefix $dnsLabelPrefix

 (Get-AzVm -ResourceGroupName $resourceGroupName).name

```

Если вы решили установить и использовать PowerShell локально, а не в Azure Cloud Shell, для работы с этим руководством требуется модуль Azure PowerShell. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). При использовании PowerShell на локальном компьютере также нужно запустить `Connect-AzAccount`, чтобы создать подключение к Azure.

В предыдущем примере указан шаблон, хранящийся в GitHub. Вы также можете скачать или создать шаблон и указать локальный путь с параметром `--template-file`.

Ниже приведены некоторые дополнительные ресурсы.

- Чтобы узнать, как создавать шаблоны Resource Manager, ознакомьтесь с [документацией по Azure Resource Manager](/azure/azure-resource-manager/).
- Схемы виртуальных машин Azure см. в справочнике по [шаблонам Azure](/azure/templates/microsoft.compute/allversions).
- Дополнительные примеры шаблонов виртуальных машин см. в статье [шаблоны](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)быстрого запуска Azure.

## <a name="connect-to-the-virtual-machine"></a>Подключение к виртуальной машине

Последняя команда PowerShell из предыдущего скрипта показывает имя виртуальной машины. Чтобы подключиться к виртуальной машине, см. раздел [как подключиться к виртуальной машине Azure под Windows и войти](./connect-logon.md)в нее.

## <a name="next-steps"></a>Следующие шаги

- Если возникнут проблемы с развертыванием, см. статью [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
- Сведения о создании виртуальной машины и управлении ею см. в статье [Создание виртуальных машин Windows и управление ими с помощью модуля Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Дополнительные сведения о создании шаблонов, синтаксисе и свойствах JSON для типов ресурсов, которые вы развернули, см. в этих статьях:

- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
