---
title: Развертывание приложения двойного стека IPv6 с базовыми Load Balancer в виртуальной сети Azure шаблон Resource Manager (Предварительная версия)
titlesuffix: Azure Virtual Network
description: В этой статье показано, как развернуть приложение с двумя стеками IPv6 в виртуальной сети Azure с помощью Azure Resource Manager шаблонов виртуальных машин.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/26/2019
ms.author: kumud
ms.openlocfilehash: e97a5a478871d94e074b59558a11df1ec752b0f9
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249847"
---
# <a name="deploy-an-ipv6-dual-stack-application-with-basic-load-balancer-in-azure---template-preview"></a>Развертывание приложения с двойным стеком IPv6 с помощью базового Load Balancer в Azure — шаблон (Предварительная версия)

В этой статье представлен список задач настройки IPv6 с частью шаблона Azure Resource Manager виртуальной машины, которая применяется к. Используйте шаблон, описанный в этой статье, чтобы развернуть приложение с двойным стеком (IPv4 + IPv6) в Azure, включающее в себя виртуальную сеть с двумя стеками с подсетями IPv4 и IPv6, подсистемой балансировки нагрузки с двумя интерфейсными конфигурациями (IPv4 + IPv6), виртуальными машинами с сдвоенным IP-адресом. Конфигурация, группа безопасности сети и общедоступные IP-адреса. 

## <a name="required-configurations"></a>Требуемые конфигурации

Найдите разделы шаблона в шаблоне, чтобы узнать, где они должны происходить.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>Аддрессспаце IPv6 для виртуальной сети

Добавляемый раздел шаблона:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>Подсеть IPv6 в виртуальной сети IPv6 Аддрессспаце

Добавляемый раздел шаблона:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>Конфигурация IPv6 для сетевой карты

Добавляемый раздел шаблона:
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

### <a name="ipv6-network-security-group-nsg-rules"></a>Правила группы безопасности сети (NSG) IPv6

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

Если вы используете виртуальный сетевой модуль, добавьте IPv6-маршруты в таблицу маршрутов. В противном случае эта конфигурация является необязательной.

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

### <a name="ipv6-internet-access-for-the-virtual-network"></a>Доступ к Интернету по протоколу IPv6 для виртуальной сети

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>Общедоступные IP-адреса IPv6

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

### <a name="ipv6-front-end-for-load-balancer"></a>Внешний интерфейс IPv6 для Load Balancer

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>Пул адресов серверной части IPv6 для Load Balancer

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

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>Правила балансировщика нагрузки IPv6 для связывания входящих и исходящих портов

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

## <a name="sample-vm-template-json"></a>Пример шаблона виртуальной машины JSON
Чтобы развернуть приложение с двойным стеком IPv6 в виртуальной сети Azure с помощью шаблона Azure Resource Manager, просмотрите пример шаблона [здесь](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/).

## <a name="next-steps"></a>Следующие шаги

Вы можете узнать о ценах на общедоступные [IP-адреса](https://azure.microsoft.com/pricing/details/ip-addresses/), пропускную [способность сети](https://azure.microsoft.com/pricing/details/bandwidth/)или [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).
