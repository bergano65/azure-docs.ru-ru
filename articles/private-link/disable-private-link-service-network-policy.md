---
title: 'Отключить сетевые политики для IP-адреса источника службы Azure Private Link '
description: Узнайте, как отключить сетевые политики для частной ссылки Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 4c6bd64d141341e0b7fa5641e04320a95d7951bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453008"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>Отключить сетевые политики для IP-адреса источника услуг Private Link

Для выбора исходного IP-адреса для службы Private Link `privateLinkServiceNetworkPolicies` в подсети требуется явная настройка отработки. Эта настройка применима только для конкретного частного IP-адреса, выбранного вами в качестве исходного IP-адреса службы Private Link. Для других ресурсов в подсети доступ контролируется на основе определения правил безопасности Network Security Groups (NSG). 
 
При использовании любого клиента Azure (PowerShell, CLI или шаблоны) требуется дополнительный шаг для изменения этого свойства. Отключить политику можно с помощью облачной оболочки с портала Azure или локальных установок Azure PowerShell, Azure CLI, или использовать шаблоны управления ресурсами Azure.  
 
Следуйте ниже, чтобы отключить частные политики сети услуг ссылка для виртуальной сети под названием *myVirtualNetwork* с подсетью *по умолчанию,* размещенной в группе ресурсов под названием *myResourceGroup*. 

## <a name="using-azure-powershell"></a>Использование Azure PowerShell
В этом разделе описывается, как отключить политики частной конечных точек с помощью Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Использование Azure CLI
В этом разделе описывается, как отключить политики частной конечных точек с помощью политикAzура CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
```
## <a name="using-a-template"></a>Использование шаблона
В этом разделе описывается, как отключить политики частной конечных точек подсети с помощью шаблона менеджера ресурсов Azure.
```json
{ 
    "name": "myVirtualNetwork", 
    "type": "Microsoft.Network/virtualNetworks", 
    "apiVersion": "2019-04-01", 
    "location": "WestUS", 
    "properties": { 
        "addressSpace": { 
            "addressPrefixes": [ 
                "10.0.0.0/16" 
             ] 
        }, 
        "subnets": [ 
               { 
                 "name": "default", 
                 "properties": { 
                        "addressPrefix": "10.0.0.0/24", 
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>Дальнейшие действия
- Узнайте больше о [частной точке зрения Azure](private-endpoint-overview.md)
 
