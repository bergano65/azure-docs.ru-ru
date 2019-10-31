---
title: Развертывание масштабируемых наборов виртуальных машин с помощью IPv6 в Azure
titlesuffix: Azure Virtual Network
description: В этой статье показано, как развертывать масштабируемые наборы виртуальных машин с IPv6 в виртуальной сети Azure.
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
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164995"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure-preview"></a>Развертывание масштабируемых наборов виртуальных машин с помощью IPv6 в Azure (Предварительная версия)

В этой статье показано, как развернуть масштабируемый набор виртуальных машин с двумя стеками (IPv4 + IPv6) с помощью внешней подсистемы балансировки нагрузки в виртуальной сети Azure. Процесс создания масштабируемого набора виртуальных машин с поддержкой IPv6 практически идентичен процессу создания отдельных виртуальных машин, описанных [здесь](ipv6-configure-standard-load-balancer-template-json.md). Начнем с действий, которые похожи на процедуры, описанные для отдельных виртуальных машин.
1.  Создайте общедоступные IP-адреса IPv4 и IPv6.
2.  Создайте подсистему балансировки нагрузки с двумя стеками.  
3.  Создание правил группы безопасности сети (NSG).  

Единственным шагом, который отличается от отдельных виртуальных машин, является создание конфигурации сетевого интерфейса (NIC), использующей ресурс масштабируемого набора виртуальных машин: networkProfile/networkInterfaceConfigurations. Структура JSON аналогична структуре объекта Microsoft. Network/networkInterfaces, которая используется для отдельных виртуальных машин с добавлением сетевой карты и IP-конфигурации IPv4 в качестве основного интерфейса с помощью атрибута **PRIMARY: true** , как показано в Следующий пример:

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


## <a name="sample-virtual-machine-scale-set-template-json"></a>Пример шаблона масштабируемого набора виртуальных машин в формате JSON

[Вот](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/)как можно развернуть масштабируемый набор виртуальных машин с двойным стеком (IPv4 + IPv6) с двумя внешними Load Balancer и примером шаблона представления виртуальной сети.
## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о поддержке IPv6 в виртуальных сетях Azure см. в статье [что такое IPv6 для виртуальной сети Azure?](ipv6-overview.md).
