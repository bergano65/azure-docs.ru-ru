---
title: Развертывание приложения двойной стек IPv6 в виртуальной сети Azure - шаблон диспетчера ресурсов (Предварительная версия)
titlesuffix: Azure Virtual Network
description: В этой статье показано, как развернуть приложение двойной стек IPv6 в виртуальной сети Azure, с помощью шаблонов виртуальных Машин Azure Resource Manager.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: ae90bc4a12763803f38224d917c4644a68ae7d6b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62131033"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---template-preview"></a>Развертывание приложения двойной стек IPv6 в Azure - шаблон (Предварительная версия)

В этой статье список задач настройки IPv6 с помощью части шаблона виртуальной Машины Azure Resource Manager, который применяется для. Используйте шаблон, описанный в этой статье для развертывания приложения двойной стек (IPv4 + IPv6) в Azure, которая включает в себя двойной стек виртуальной сети с подсетями IPv4 и IPv6, подсистему балансировки нагрузки с помощью интерфейсные конфигурации двумя (IPv4 + IPv6), виртуальные машины с сетевыми интерфейсами, которые два IP-адрес конфигурации группы безопасности сети и общедоступные IP-адреса. 

## <a name="required-configurations"></a>Требуемые настройки

Поиск в разделах шаблона в шаблоне, чтобы увидеть, где они должны происходить.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>AddressSpace IPv6 для виртуальной сети

Раздел шаблона, чтобы добавить:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>Подсеть IPv6 в addressSpace виртуальной сети IPv6

Раздел шаблона, чтобы добавить:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>Настройка IPv6 для сетевой КАРТЫ

Раздел шаблона, чтобы добавить:
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

### <a name="ipv6-network-security-group-nsg-rules"></a>Правила группы безопасности сети IPv6

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

## <a name="conditional-configuration"></a>Условный конфигурации

Если вы используете виртуальный сетевой модуль, добавьте маршруты IPv6 в таблице маршрутов. В противном случае эта конфигурация является необязательным.

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

## <a name="optional-configuration"></a>Необязательная конфигурация

### <a name="ipv6-internet-access-for-the-virtual-network"></a>Доступ к Интернету IPv6 для виртуальной сети

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>Общедоступный IP-адресов IPv6

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

### <a name="ipv6-front-end-for-load-balancer"></a>IPv6 интерфейса для подсистемы балансировки нагрузки

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>Пул адресов серверной части IPv6 для подсистемы балансировки нагрузки

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

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>IPv6 правил load balancer связываемый входящие и исходящие порты

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

## <a name="sample-vm-template-json"></a>Пример JSON-ФАЙЛ шаблона виртуальной Машины
Нажмите кнопку [здесь](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/) для развертывания приложения двойной стек IPv6 в виртуальной сети Azure, с помощью шаблона Azure Resource Manager.

## <a name="next-steps"></a>Дальнейшие действия

Сведения о ценах на [общедоступные IP-адреса](https://azure.microsoft.com/pricing/details/ip-addresses/), [пропускной способности сети](https://azure.microsoft.com/pricing/details/bandwidth/), или [подсистемы балансировки нагрузки](https://azure.microsoft.com/pricing/details/load-balancer/).
