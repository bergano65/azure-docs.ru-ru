---
title: Настройка управляемого кластера Service Fabric (Предварительная версия)
description: Узнайте, как настроить управляемый кластер Service Fabric для автоматического обновления ОС, правил NSG и многого другого.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: 90ba5057f06cf8841e278b8d921d812286459c49
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642496"
---
# <a name="service-fabric-managed-cluster-preview-configuration-options"></a>Параметры конфигурации управляемого кластера (Предварительная версия) Service Fabric

Помимо выбора [номера SKU управляемого кластера Service Fabric](overview-managed-cluster.md#service-fabric-managed-cluster-skus) при создании кластера существует ряд других способов его настройки. В текущей предварительной версии можно:

* Добавление [расширения масштабируемого набора виртуальных машин](how-to-managed-cluster-vmss-extension.md) в тип узла
* Включение [автоматического обновления ОС](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades) для узлов
* Включение [шифрования дисков ОС и данных](how-to-enable-managed-cluster-disk-encryption.md) на узлах
* О [конфигурациях сети](how-to-managed-cluster-configuration.md#networking-configurations)
* Настройка [управляемого удостоверения](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) для типов узлов

## <a name="networking-configurations"></a>Конфигурации сети

Service Fabric управляемые кластеры создаются с конфигурацией сети по умолчанию. Эта конфигурация состоит из обязательных правил для необходимых функциональных возможностей кластера и нескольких необязательных правил, которые призваны упростить настройку клиентов.

Помимо конфигурации сети по умолчанию, правила сети можно изменить в соответствии с потребностями вашего сценария. 

### <a name="nsg-rules-guidance"></a>Руководство по правилам NSG

* Service Fabric управляемые кластеры Зарезервируйте диапазон приоритетов правил NSG от 0 до 999 для важных функциональных возможностей. Нельзя создавать настраиваемые правила NSG со значением приоритета менее 1000. 
* Service Fabric управляемых кластерах для создания необязательных правил NSG резервируется диапазон приоритетов 3001 – 4000. Эти правила добавляются автоматически для быстрого и простого создания конфигураций. Эти правила можно переопределить, добавив настраиваемые правила NSG в диапазоне приоритета 1000 в 3000. 
* Настраиваемые правила NSG должны иметь приоритет в диапазоне от 1000 до 3000. 


### <a name="apply-nsg-rules"></a>Применить правила NSG

При использовании классических (не управляемых) Service Fabric кластеров для [применения правил группы безопасности сети (NSG) к кластеру](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-nsg-cluster-65-node-3-nodetype)необходимо объявить и управлять отдельным ресурсом *Microsoft. Network/networkSecurityGroups* . Service Fabric управляемые кластеры позволяют назначать правила NSG непосредственно в кластерном ресурсе шаблона развертывания.

Чтобы назначить правила NSG, используйте свойство [нетворксекуритирулес](/azure/templates/microsoft.servicefabric/managedclusters#managedclusterproperties-object) ресурса *Microsoft. ServiceFabric/манажедклустерс* (версия `2021-01-01-preview` или более поздней версии). Пример.

```json
            "apiVersion": "2021-01-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            "properties": {
                ...
                "networkSecurityRules" : [
                    {
                        "name": "AllowCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33000-33499",
                        "access": "Allow",
                        "priority": 2001,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "AllowARM",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "AzureResourceManager",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33500-33699",
                        "access": "Allow",
                        "priority": 2002,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "DenyCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33700-33799",
                        "access": "Deny",
                        "priority": 2003,
                        "direction": "Outbound"
                    },
                    {
                        "name": "DenyRDP",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "*",
                        "destinationAddressPrefix": "VirtualNetwork",
                        "destinationPortRange": "3389",
                        "access": "Deny",
                        "priority": 2004,
                        "direction": "Inbound",
                        "description": "Override for optional SFMC_AllowRdpPort rule. This is required in tests to avoid Sev2 incident for security policy violation."
                    }
                ],
                "fabricSettings": [
                ...
                ]
            }
```

### <a name="rdp-ports"></a>Порты RDP 

Service Fabric управляемые кластеры не разрешают доступ к портам RDP по умолчанию. Вы можете открыть порты RDP в Интернете, задав следующее свойство для Service Fabric управляемого ресурса кластера. 

```json
"allowRDPAccess": true 
```

Если свойство allowRDPAccess имеет значение true, в развертывание кластера будет добавлено следующее правило NSG.  

```json
{
    "name": "SFMC_AllowRdpPort", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open RDP ports.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3002,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRange": "3389"
    }
}
```

### <a name="clientconnection-and-httpgatewayconnection-ports"></a>Порты Клиентконнектион и Хттпгатевайконнектион 

**Правило NSG: SFMC_AllowServiceFabricGatewayToSFRP** Добавляется правило NSG по умолчанию, разрешающее поставщику ресурсов Service Fabric доступ к Клиентконнектионпорт и Хттпгатевайконнектионпорт кластера. Это правило разрешает доступ к портам через тег службы "ServiceFabric".

>[!NOTE]
>Это правило всегда добавляется и не может быть переопределено. 

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayToSFRP", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "This is required rule to allow SFRP to connect to the cluster. This rule cannot be overridden.", 
        "protocol": "TCP", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "ServiceFabric", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 500, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

**Правило NSG: SFMC_AllowServiceFabricGatewayPorts** Это необязательное правило NSG, разрешающее доступ к Клиентконнектионпорт и Хттпгатевайпорт из Интернета. Это правило позволяет клиентам получить доступ к SFX, подключиться к кластеру с помощью PowerShell и использовать Service Fabric конечные точки API кластера извне. 

>[!NOTE]
>Это правило не будет добавлено, если имеется настраиваемое правило с тем же доступом, направлением и значениями протокола для того же порта. Это правило можно переопределить с помощью настраиваемых правил NSG. 

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayPorts", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "Optional rule to open SF cluster gateway ports. To override add a custom NSG rule for gateway ports in priority range 1000-3000.", 
        "protocol": "tcp", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "*", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 3001, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

### <a name="load-balancer-ports"></a>Порты подсистемы балансировки нагрузки 

Service Fabric управляемых кластеров создайте правило NSG в диапазоне приоритетов по умолчанию для всех портов балансировки нагрузки, настроенных в разделе "ЛоадбаланЦингрулес" в разделе Свойства Манажедклустер. Это правило открывает порты балансировки нагрузки для входящего трафика из Интернета.  

>[!NOTE]
>Это правило добавляется в необязательный диапазон приоритета и может быть переопределено путем добавления пользовательских правил NSG. 

```json
{
    "name": "SFMC_AllowLoadBalancedPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open LB ports",
        "protocol": "*", 
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3003,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
        "80", "8080", "4343"
        ]
    }
}
```

### <a name="load-balancer-probes"></a>Зонды подсистемы балансировки нагрузки

Service Fabric управляемые кластеры автоматически создают проверки балансировщика нагрузки для портов шлюза структуры, а также все порты, настроенные в разделе "ЛоадбаланЦингрулес" свойств управляемого кластера.

```json
{ 
  "value": [ 
    { 
        "name": "FabricTcpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19000, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>"
                } 
            ] 
        }, 
        "type": "Microsoft.Network/loadBalancers/probes" 
    }, 
    { 
        "name": "FabricHttpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>" 
                } 
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes" 
    },
    {
        "name": "probe1_tcp_8080", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 8080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
            { 
                "id": "<>" 
            } 
        ] 
      }, 
      "type": "Microsoft.Network/loadBalancers/probes" 
    } 
  ] 
} 
```

## <a name="enable-automatic-os-image-upgrades"></a>Включить автоматическое обновление образа ОС

Вы можете включить автоматическое обновление образа ОС для виртуальных машин, на которых выполняются управляемые узлы кластера. Хотя ресурсы масштабируемого набора виртуальных машин управляются от вашего имени с помощью Service Fabric управляемых кластеров, вы можете включить автоматическое обновление образов ОС для узлов кластера. Как и в [классическом Service Fabric](service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration) кластерах, управляемые узлы кластера по умолчанию не обновляются, чтобы предотвратить непредвиденные перерывы в работе кластера.

Включение автоматического обновления ОС.

* Используйте `2021-01-01-preview` (или более позднюю) версию ресурсов *Microsoft. ServiceFabric/Манажедклустерс* и *Microsoft. ServiceFabric/манажедклустерс/NodeType* .
* Присвойте свойству кластера `enableAutoOSUpgrade` *значение true* .
* Присвойте свойству ресурса кластера nodeType значение `vmImageVersion` *Latest* .

Пример.

```json
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

После включения Service Fabric начнет запрашивать и отслеживать версии образа ОС в управляемом кластере. Если доступна новая версия ОС, типы узлов кластера (масштабируемые наборы виртуальных машин) будут обновлены по одному за раз. Service Fabric обновления среды выполнения выполняются только после подтверждения того, что обновления образа ОС узла кластера не выполняются.

В случае сбоя обновления Service Fabric повторит попытку через 24 часа, не более трех повторных попыток. Как и классические (неуправляемые) Service Fabric обновления, неработоспособные приложения или узлы могут блокировать обновление образа ОС.

Дополнительные сведения об обновлении образов см. в статье [Автоматическое обновление образа ОС с помощью масштабируемых наборов виртуальных машин Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).

## <a name="next-steps"></a>Дальнейшие действия

[Ссылка на образец шаблона]

[Обзор управляемого кластера]
