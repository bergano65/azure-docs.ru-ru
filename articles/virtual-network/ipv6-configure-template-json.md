---
title: Развертывание двойного стека IPv6 с помощью базового баланса нагрузки в виртуальной сети Azure - шаблон Resource Manger
titlesuffix: Azure Virtual Network
description: В этой статье показано, как развернуть приложение с двойным стеком IPv6 в виртуальной сети Azure с помощью vM-шаблонов Azure Resource Manager.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 7c2f66d92597801aa7c6b0b32f86f594d3effb05
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420601"
---
# <a name="deploy-an-ipv6-dual-stack-application-with-basic-load-balancer-in-azure---template"></a>Развертывание приложения с двойным стеком IPv6 с помощью базового баланса нагрузки в Azure - Шаблон

В этой статье приводится список задач конфигурации IPv6 с той частью шаблона VM-шаблона Управления ресурсами Azure, к которым применяется. Используйте шаблон, описанный в этой статье, чтобы развернуть двойной стек (IPv4 - IPv6) приложение с Basic Load Balancer, который включает в себя двойной стек виртуальной сети с IPv4 и IPv6 подсети, базовая нагрузка балансер с двойной (IPv4 IPv6) фронтальной конфигурации, VMs с NICs, которые имеют двойную конфигурацию IP-адреса, группы безопасности сети, и общественных IP-адресов.

Для развертывания двойного стека (IPV4 - IPv6) приложения с помощью Standard Load Balancer [см. Развертывание приложения iPv6 с двойным стеком со стандартной нагрузкой Balancer - Template.](ipv6-configure-standard-load-balancer-template-json.md)

## <a name="required-configurations"></a>Необходимые конфигурации

Поиск разделов шаблона в шаблоне, чтобы увидеть, где они должны произойти.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>IPv6 addressSpace для виртуальной сети

Шаблон раздела для добавления:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>Подсеть IPv6 в виртуальном сетевом адресе IPv6

Шаблон раздела для добавления:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>Конфигурация IPv6 для NIC

Шаблон раздела для добавления:
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>Правила группы сетевой безопасности IPv6 (NSG)

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "ace:cab:deca:deed::/64",
              "destinationAddressPrefix": "cab:ace:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>Условная конфигурация

Если вы используете сетевой виртуальный прибор, добавьте маршруты IPv6 в таблицу route Table. В противном случае эта конфигурация не является обязательной.

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "ace:cab:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "deca:cab:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>Дополнительные настройки

### <a name="ipv6-internet-access-for-the-virtual-network"></a>IPv6 Доступ в Интернет для виртуальной сети

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>IPv6 Публичные IP-адреса

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>IPv6 Передняя часть для балансора загрузочика

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>IPv6 Пул адресов для балансораза нагрузки

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>Правила балансоостатка нагрузки IPv6 связывают входящие и исходящие порты

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>Пример VM шаблона JSON
Чтобы развернуть приложение с двойным стеком IPv6 с помощью виртуальной сети Basic Load Balancer в виртуальной сети Azure с помощью шаблона Azure Resource Manager, просмотрите шаблон образца [здесь.](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/)

## <a name="next-steps"></a>Следующие шаги

Вы можете найти подробную информацию о ценах на [общедоступные IP-адреса,](https://azure.microsoft.com/pricing/details/ip-addresses/) [пропускную способность сети](https://azure.microsoft.com/pricing/details/bandwidth/)или [балансер нагрузки.](https://azure.microsoft.com/pricing/details/load-balancer/)
