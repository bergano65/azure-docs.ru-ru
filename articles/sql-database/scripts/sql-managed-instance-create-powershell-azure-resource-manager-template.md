---
title: Пример для PowerShell. Создание управляемого экземпляра в Базе данных SQL Azure | Документация Майкрософт
description: Пример сценария Azure PowerShell для создания управляемого экземпляра в Базе данных SQL Azure
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 5a60e8efad41c94deeedd545e6e0c1c96ff04e25
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569738"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-a-managed-instance-in-azure-sql-database"></a>Создание управляемого экземпляра в Базе данных SQL Azure с помощью PowerShell с шаблоном Azure Resource Manager

С помощью библиотеки Azure PowerShell и шаблонов Azure Resource Manager можно создать Управляемый экземпляр Базы данных SQL Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Чтобы установить и использовать PowerShell локально, для работы с этим руководством вам понадобится AZ PowerShell 1.4.0 или более поздней версии. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

Начать развертывание можно с помощью команд Azure PowerShell, используя предопределенный шаблон Azure Resource Manager. В шаблоне можно указать приведенные ниже свойства.

- Имя экземпляра
- Имя пользователя и пароль администратора SQL.
- Размер экземпляра (число ядер и максимальный размер хранилища).
- Виртуальную сеть и подсеть, где будет размещаться экземпляр.
- Параметры сортировки серверного уровня для экземпляра (предварительная версия).

Позже нельзя изменить имя экземпляра, имя пользователя администратора SQL, виртуальной сети или подсети и параметры сортировки. Другие свойства экземпляра можно изменить.

## <a name="prerequisites"></a>Предварительные требования

В этом примере предполагается, что вы [создали допустимую сетевую среду](../sql-database-managed-instance-create-vnet-subnet.md) или [изменили существующую виртуальную сеть](../sql-database-managed-instance-configure-vnet-subnet.md) для Управляемого экземпляра. В этом примере используются командлеты [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) и [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork), поэтому установите перечисленные ниже модули PowerShell.

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Шаблон Azure Resource Manager

В файле, представляющем шаблон, который будет использоваться для создания экземпляра, должно размещаться следующее содержимое.

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
                "name": "GP_Gen4",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen4",
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

Предполагается, что уже существует виртуальная сеть Azure с правильно настроенной подсетью. Если ваша подсеть настроена неправильно, подготовьте сетевую среду с применением различных [шаблонов управляемых ресурсов Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment), которые можно выполнять независимо друг от друга или которые включены в этот шаблон.

Сохраните содержимое этого файла в формате JSON-файла, поместите путь к файлу в следующий сценарий PowerShell и измените имена объектов в скрипте.

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed Instance properties
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

# Deploy Instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

После успешного выполнения скрипта доступ к базе данных SQL можно получить из всех служб Azure, а также по настроенному IP-адресу.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для базы данных SQL Azure можно найти [здесь](../sql-database-powershell-samples.md).
