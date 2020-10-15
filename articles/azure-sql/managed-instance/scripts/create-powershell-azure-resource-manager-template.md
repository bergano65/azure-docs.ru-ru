---
title: Создание управляемого экземпляра (шаблон Resource Manager и PowerShell)
titleSuffix: Azure SQL Managed Instance
description: Используйте этот пример сценария Azure PowerShell для создания управляемого экземпляра.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-dt-2019, devx-track-azurepowershell
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 6758aed67949032bb93086527623b60af33e716e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079563"
---
# <a name="use-powershell-with-an-azure-resource-manager-template-to-create-a-managed-instance"></a>Создание управляемого экземпляра с помощью PowerShell с шаблоном Azure Resource Manager

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Вы можете создать управляемый экземпляр с помощью библиотеки Azure PowerShell и шаблонов Azure Resource Manager.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Чтобы установить и использовать PowerShell локально, вам понадобится Azure PowerShell 1.4.0 или последующей версии для работы с этим учебником. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, выполните командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

Начать развертывание можно с помощью команд Azure PowerShell, используя предопределенный шаблон Azure Resource Manager. В шаблоне можно указать приведенные ниже свойства.

- Имя управляемого экземпляра
- Имя пользователя и пароль администратора SQL.
- Размер экземпляра (число ядер и максимальный размер хранилища).
- Виртуальную сеть и подсеть, где будет размещаться экземпляр.
- Параметры сортировки серверного уровня для экземпляра (предварительная версия).

Позже нельзя изменить имя экземпляра, имя пользователя администратора SQL, виртуальной сети или подсети и параметры сортировки. Другие свойства экземпляра можно изменить.

## <a name="prerequisites"></a>Предварительные требования

В этом примере предполагается, что вы [создали допустимую сетевую среду](../virtual-network-subnet-create-arm-template.md) или [изменили существующую виртуальную сеть](../vnet-existing-add-subnet.md) для вашего управляемого экземпляра. При необходимости можно подготовить сетевую среду, используя отдельный [шаблон Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment). 


В этом примере используются командлеты [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) и [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork), поэтому установите перечисленные ниже модули PowerShell.

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Шаблон Azure Resource Manager


Сохраните следующий сценарий в JSON-файле и запишите расположение файла: 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "string"
        },
        "user": {
            "type": "string"
        },
        "pwd": {
            "type": "securestring"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('instance')]",
            "location": "West Central US",
            "tags": {
                "Description":"GP Instance with custom instance collation - Serbian_Cyrillic_100_CS_AS"
            },
            "sku": {
                "name": "GP_Gen5",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS"
            },
            "type": "Microsoft.Sql/managedInstances",
            "identity": {
                "type": "SystemAssigned"
            },
            "apiVersion": "2015-05-01-preview"
        }
    ]
}
```

Обновите следующий сценарий PowerShell, указав правильный путь к JSON-файлу, который вы сохранили ранее, и измените имена объектов в сценарии:

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed instance properties
$resourceGroup = "rg_mi"
$location = "West Central US"
$name = "managed-instance-name"
$user = "miSqlAdmin"
$secpasswd = ConvertTo-SecureString "<Put some strong password here>" -AsPlainText -Force

# Network configuration
$vNetName = "my_vnet"
$vNetResourceGroup = "rg_mi_vnet"
$subnetName = "ManagedInstances"
$vNet = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

После завершения выполнения сценария доступ к управляемому экземпляру можно получить из всех служб Azure, а также по настроенному IP-адресу.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure PowerShell см. в [этой документации](/powershell/azure/).

Дополнительные примеры скриптов PowerShell для Управляемого экземпляра SQL Azure можно найти [здесь](../../database/powershell-script-content-guide.md).
