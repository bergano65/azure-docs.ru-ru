---
title: Развертывание виртуальных наборов масштабов машин с IPv6 в Azure
titlesuffix: Azure Virtual Network
description: В этой статье показано, как развертывать виртуальные наборы масштабов машин с IPv6 в виртуальной сети Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: kumud
ms.openlocfilehash: b90910614bcd86a54198b1a0961a3378427ea87e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73164995"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure-preview"></a>Развертывание виртуальных наборов масштабов машин с IPv6 в Azure (Предварительный просмотр)

В этой статье показано, как развернуть двойной стек (IPv4 - IPv6) Виртуальный набор шкалы машин с двойным балансом внешней нагрузки в виртуальной сети Azure. Процесс создания набора виртуальных машин IPv6, способных к созданию виртуальной машины, практически идентичен процессу создания отдельных виртуальных машин, описанных [здесь.](ipv6-configure-standard-load-balancer-template-json.md) Вы начнете с шагов, похожих на те, которые описаны для отдельных VMs:
1.  Создание публичных ИП IPv4 и IPv6.
2.  Создайте двойной балансисатор нагрузки стека.  
3.  Создание правил группы сетевой безопасности (NSG).  

Единственным шагом, который отличается от отдельных Виртуальных мв., является создание конфигурации сетевого интерфейса (NIC), которая использует ресурс набора виртуальной шкалы машин: networkProfile/networkInterfaceConfigurations. Структура JSON аналогична структуре объекта Microsoft.Network/networkInterfaces, используемого для отдельных VMs с добавлением установки NIC и IPv4 IpConfiguration в качестве основного интерфейса с использованием **"первичного": истинный** атрибут, как видно в следующем примере:

```json
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[variables('nicName')]",
                "properties": {
                  "primary": true,
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups','VmssNsg')]"
          },                  
                  "ipConfigurations": [
                    {
                      "name": "[variables('ipConfigName')]",
                      "properties": {
                        "primary": true,
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv4",                       
                        "publicipaddressconfiguration": {
                          "name": "pub1",
                          "properties": {
                            "idleTimeoutInMinutes": 15
                          }
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer', 'bePool'))]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatPools', 'loadBalancer', 'natPool')]"
                          }
                        ]
                      }
                    },
                    {
                      "name": "[variables('ipConfigNameV6')]",
                      "properties": {
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets','MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv6",
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer','bePoolv6')]"
                          }
                        ],                        
                      }
                    }
                  ]
                }
              }
            ]
          }

```


## <a name="sample-virtual-machine-scale-set-template-json"></a>Пример виртуального шаблона набора масштабов машины JSON

Для развертывания двойного стека (IPv4 - IPv6) Виртуальный набор шкалы машин с двойным стеком внешнего баланса нагрузки и виртуальным шаблоном образца просмотра сети [здесь](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/).
## <a name="next-steps"></a>Дальнейшие действия

Подробнее о поддержке IPv6 в виртуальных сетях Azure читайте в пример [IPv6 для виртуальной сети Azure?.](ipv6-overview.md)
