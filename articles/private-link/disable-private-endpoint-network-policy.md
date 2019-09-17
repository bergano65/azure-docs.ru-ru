---
title: Отключение сетевых политик для частных конечных точек в Azure
description: Узнайте, как отключить политики сети для частных конечных точек.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 8b5e989db46bce3fdf652a0b64db86cdcc16f9f1
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018001"
---
# <a name="disable-network-policies-for-private-endpoints"></a>Отключение сетевых политик для частных конечных точек

Сетевые политики, такие как группы безопасности сети (NSG), не поддерживаются для частных конечных точек. Чтобы развернуть закрытую конечную точку в заданной подсети, для этой подсети требуется явный параметр отключения. Этот параметр применим только к закрытой конечной точке. Для других ресурсов в подсети управление доступом осуществляется на основе определения правил безопасности группы безопасности сети (NSG). 
 
При использовании портала для создания частной конечной точки этот параметр автоматически отключается в рамках процесса создания. Развертывание с использованием других клиентов требует дополнительного шага для изменения этого параметра. Вы можете отключить этот параметр с помощью Cloud Shell из портал Azure или локальной установки Azure PowerShell, Azure CLI или использовать шаблоны Azure Resource Manager.  
 
В следующих примерах `PrivateEndpointNetworkPolicies` описывается отключение для виртуальной сети с именем *myVirtualNetwork* и подсети *по умолчанию* , размещенной в группе ресурсов с именем *myResourceGroup*.

## <a name="using-azure-powershell"></a>Использование Azure PowerShell
В этом разделе описывается, как отключить политики частной конечной точки подсети с помощью Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork ` 
  | Select -ExpandProperty subnets ` 
  | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Использование Azure CLI
В этом разделе описывается, как отключить политики частной конечной точки подсети с помощью Azure CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
```
## <a name="using-a-template"></a>Использование шаблона
В этом разделе описывается, как отключить политики частной конечной точки подсети с помощью шаблона Azure Resource Manager.
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
## <a name="next-steps"></a>Следующие шаги
- Дополнительные сведения о [частной конечной точке Azure](private-endpoint-overview.md)
 
