---
title: Отключить сетевые политики для частных конечных точек в Azure
description: Узнайте, как отключить сетевые политики для частных конечных точек.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: b5ab62e7ab57d32a11a45713519633034deb6a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453022"
---
# <a name="disable-network-policies-for-private-endpoints"></a>Отключить сетевые политики для частных конечных точек

Сетевые политики, такие как группы сетевой безопасности (NSG), не поддерживаются для частных конечных точек. Для развертывания частной конечных точек в данной подсети в этой подсети требуется явное отогнание. Этот параметр применим только для частной конечных точек. Для других ресурсов в подсети доступ контролируется на основе определения правил безопасности Network Security Groups (NSG). 
 
При использовании портала для создания частной конечной точки эта настройка автоматически отключается как часть процесса создания. Развертывание с использованием других клиентов требует дополнительного шага для изменения этой настройки. Можно отключить настройку с помощью облачной оболочки с портала Azure или локальных установок Azure PowerShell, Azure CLI или использовать шаблоны azure Resource Manager.  
 
Ниже приведены примеры, `PrivateEndpointNetworkPolicies` как отключить виртуальную сеть под названием *myVirtualNetwork* с подсетью *по умолчанию,* размещенной в группе ресурсов под названием *myResourceGroup*.

## <a name="using-azure-powershell"></a>Использование Azure PowerShell
В этом разделе описывается, как отключить политики частной конечных точек с помощью Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Использование Azure CLI
В этом разделе описывается, как отключить политики частной конечных точек с помощью политикAzура CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
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
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>Дальнейшие действия
- Узнайте больше о [частной конечной точке Azure](private-endpoint-overview.md)
 
