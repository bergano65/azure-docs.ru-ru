---
title: 'Отключение сетевых политик для IP-адреса источника службы частной связи Azure '
description: Узнайте, как отключить политики сети для частной связи Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 22dbb12de0793db8aac12f610ff94380542d426a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228105"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>Отключить политики сети для исходного IP-адреса службы частной связи

Чтобы выбрать исходный IP-адрес для службы частной связи, в подсети необходимо указать явный параметр отключения `privateLinkServiceNetworkPolicies`. Этот параметр применим только к конкретному частному IP-адресу, выбранному в качестве исходного IP-адреса службы частной связи. Для других ресурсов в подсети управление доступом осуществляется на основе определения правил безопасности группы безопасности сети (NSG). 
 
При использовании любого клиента Azure (PowerShell, CLI или шаблонов) для изменения этого свойства требуется дополнительный шаг. Вы можете отключить политику с помощью Cloud Shell из портал Azure или локальной установки Azure PowerShell, Azure CLI или использовать шаблоны Azure Resource Manager.  
 
Выполните следующие действия, чтобы отключить сетевые политики службы частной связи для виртуальной сети с именем *myVirtualNetwork* и подсетью *по умолчанию* , размещенной в группе ресурсов с именем *myResourceGroup*. 

## <a name="using-azure-powershell"></a>Использование Azure PowerShell
В этом разделе описывается, как отключить политики частной конечной точки подсети с помощью Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Использование Azure CLI
В этом разделе описывается, как отключить политики частной конечной точки подсети с помощью Azure CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
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
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения о [частной конечной точке Azure](private-endpoint-overview.md)
 
