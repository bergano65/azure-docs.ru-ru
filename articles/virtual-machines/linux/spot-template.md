---
title: Используйте шаблон для развертывания VMs Azure Spot (Предварительный просмотр)
description: Узнайте, как использовать шаблон для развертывания Спотных ВМ для экономии затрат.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 0e635fe7ce9b442a9cc8f0fdf614feef5a3a756a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082801"
---
# <a name="deploy-spot-vms-using-a-resource-manager-template"></a>Развертывание VMs-ресурсов с помощью шаблона «Менеджер ресурсов»

Использование [Spot VM](spot-vms.md) позволяет воспользоваться нашими неиспользованными мощностями при значительной экономии средств. В любой момент времени, когда Azure требуется обратной мощности, инфраструктура Azure будет выселять Spot VMs. Таким образом, Spot VMs отлично подходят для рабочих нагрузок, которые могут обрабатывать перерывы, такие как задания по обработке пакетов, среды для разработки разработчиков/тестов, большие вычислительные нагрузки и многое другое.

Цены на Spot VMs является переменной, на основе региона и SKU. Для получения дополнительной информации [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) см. [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)

У вас есть возможность установить максимальную цену, которую вы готовы платить, в час, для VM. Максимальная цена на Spot VM может быть установлена в долларах США (USD), используя до 5 десятичных мест. Например, значение `0.98765`будет максимальной ценой $0.98765 USD в час. Если вы установите максимальную цену, чтобы быть, `-1`VM не будет выселен в зависимости от цены. Цена для VM будет текущая цена для Spot или цена для стандартного VM, который всегда меньше, до тех пор, пока есть емкость и квота доступна. Для получения дополнительной информации об установлении максимальной цены, см [Spot VMs - Цены](spot-vms.md#pricing).

> [!IMPORTANT]
> Точечные экземпляры в настоящее время находятся в общедоступном предварительном просмотре.
> Эта предварительная версия не рекомендуется для производственных нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="use-a-template"></a>Использование шаблона

Для развертывания шаблонов`"apiVersion": "2019-03-01"` Spot используйте или позже. Добавьте `priority` `evictionPolicy` в `billingProfile` шаблон и свойства:

```json
"priority": "Spot",
"evictionPolicy": "Deallocate",
"billingProfile": {
    "maxPrice": -1
}
```

Вот пример шаблона с добавленными свойствами для Spot VM. Замените имена ресурсов своими именами и `<password>` паролем для учетной записи локального администратора на VM.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2019-03-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "vnetId": "/subscriptions/ec9fcd04-e188-48b9-abfc-abcd515f1836/resourceGroups/spotVM/providers/Microsoft.Network/virtualNetworks/spotVM",
        "subnetName": "default",
        "networkInterfaceName": "spotVMNIC",
        "publicIpAddressName": "spotVM-ip",
        "publicIpAddressType": "Dynamic",
        "publicIpAddressSku": "Basic",
        "virtualMachineName": "spotVM",
        "osDiskType": "Premium_LRS",
        "virtualMachineSize": "Standard_D2s_v3",
        "adminUsername": "azureuser",
        "adminPassword": "<password>",
        "diagnosticsStorageAccountName": "diagstoragespot2019",
        "diagnosticsStorageAccountId": "Microsoft.Storage/storageAccounts/diagstoragespot2019",
        "diagnosticsStorageAccountType": "Standard_LRS",
        "diagnosticsStorageAccountKind": "Storage",
        "subnetRef": "[concat(variables('vnetId'), '/subnets/', variables('subnetName'))]"
    },
    "resources": [
        {
            "name": "spotVM",
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2019-03-01",
            "location": "eastus",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            },
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIpAddress": {
                                "id": "[resourceId(resourceGroup().name, 'Microsoft.Network/publicIpAddresses', variables('publicIpAddressName'))]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "name": "[variables('publicIpAddressName')]",
            "type": "Microsoft.Network/publicIpAddresses",
            "apiVersion": "2019-02-01",
            "location": "eastus",
            "properties": {
                "publicIpAllocationMethod": "[variables('publicIpAddressType')]"
            },
            "sku": {
                "name": "[variables('publicIpAddressSku')]"
            }
        },
        {
            "name": "[variables('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2019-03-01",
            "location": "eastus",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', variables('networkInterfaceName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[variables('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[variables('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "18.04-LTS",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[variables('virtualMachineName')]",
                    "adminUsername": "[variables('adminUsername')]",
                    "adminPassword": "[variables('adminPassword')]"
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('https://', variables('diagnosticsStorageAccountName'), '.blob.core.windows.net/')]"
                    }
                },
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            }
        },
        {
            "name": "[variables('diagnosticsStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "location": "eastus",
            "properties": {},
            "kind": "[variables('diagnosticsStorageAccountKind')]",
            "sku": {
                "name": "[variables('diagnosticsStorageAccountType')]"
            }
        }
    ],
    "outputs": {
        "adminUsername": {
            "type": "string",
            "value": "[variables('adminUsername')]"
        }
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

Вы также можете создать Spot VM с помощью [Azure PowerShell](../windows/spot-powershell.md) или [Azure CLI.](spot-cli.md)

Если вы столкнулись с ошибкой, просмотрите [коды ошибок](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).