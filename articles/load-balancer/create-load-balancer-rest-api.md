---
title: Создание Azure Load Balancer с помощью REST API | Документация Майкрософт
description: Узнайте, как создать Azure Load Balancer с помощью REST API.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: load-balancer
ms.date: 06/06/2018
ms.author: kumud
ms.openlocfilehash: ca952fa4fbea742121e579b28be35d834f17eade
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44056883"
---
# <a name="create-an-azure-basic-load-balancer-using-rest-api"></a>Создание Azure Load Balancer (цен. категория "Базовый") с помощью REST API

Azure Load Balancer распределяет новые входящие потоки, поступающие на внешний интерфейс подсистемы балансировки нагрузки, в экземпляры серверных пулов в соответствии с правилами и проверками работоспособности. Доступно два типа SKU Load Balancer: цен. категории "Базовый" и цен. категории "Стандартный". Чтобы понять разницу между двумя версиями SKU, обратитесь к разделу [Сравнение SKU Load Balancer](load-balancer-overview.md#skus).
 
В этом практическом руководстве показано, как создать Azure Load Balancer (цен. категория "Базовый"), используя [Azure REST API](/rest/api/azure/) для распределения нагрузки входящих запросов между несколькими виртуальными машинами в рамках виртуальной сети Azure. Полную справочную документацию и дополнительные образцы для REST API можно найти в статье [Load Balancer REST API](/rest/api/load-balancer/) (REST API Load Balancer).
 
## <a name="build-the-request"></a>Создание запроса
Следующий запрос HTTP PUT можно использовать для создания Azure Load Balancer (цен. категория "Базовый").
 ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}?api-version=2018-02-01
  ```
### <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

|ИМЯ  |В  |Обязательно |type |ОПИСАНИЕ |
|---------|---------|---------|---------|--------|
|subscriptionId   |  path       |  Истина       |   строка      |  Учетные данные подписки, позволяющие уникально идентифицировать подписку Microsoft Azure. Идентификатор подписки формирует часть URI для каждого вызова службы.      |
|имя_группы_ресурсов     |     path    | Истина        |  строка       |   Имя группы ресурсов.     |
|loadBalancerName     |  path       |      Истина   |    строка     |    Имя подсистемы балансировки нагрузки.    |
|api-version    |   query     |  Истина       |     строка    |  Версия API клиента.      |



### <a name="request-body"></a>Тело запроса

Единственным обязательным параметром является `location`. Если не определить версию *SKU*, по умолчанию создается Load Balancer (цен. категория "Базовый").  Используйте [необязательные параметры](https://docs.microsoft.com/rest/api/load-balancer/loadbalancers/createorupdate#request-body) для настройки подсистемы балансировки нагрузки.

| ИМЯ | type | ОПИСАНИЕ |
| :--- | :--- | :---------- |
| location | строка | Расположение ресурса. Получите текущий список расположений с помощью операции [List Locations](https://docs.microsoft.com/rest/api/resources/subscriptions/listlocations). |


## <a name="example-create-and-update-a-basic-load-balancer"></a>Пример: создание и обновление Azure Load Balancer (цен. категория "Базовый")

В этом примере вы создадите Load Balancer (цен. категория "Базовый") вместе с ресурсами. Далее следует настроить ресурсы подсистемы балансировки нагрузки, содержащие конфигурацию IP внешнего интерфейса, пул адресов серверной части, правило балансировки нагрузки, проверку работоспособности и правило NAT для входящего трафика.

Перед созданием подсистемы балансировки нагрузки с помощью приведенного ниже примера создайте виртуальную сеть *vnetlb* с подсетью *subnetlb* в группе ресурсов *rg1* в регионе **Восточная часть США**.

### <a name="step-1-create-a-basic-load-balancer"></a>ШАГ 1. Создание подсистемы балансировки нагрузки уровня "Базовый"
На этом шаге создайте Azure Load Balancer (цен. категория "Базовый") с именем *lb* в регионе **Восточная часть США** в рамках группы ресурсов *rg1*.
#### <a name="sample-request"></a>Пример запроса

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>Тело запроса

  ```JSON
   {
    "location": "eastus",
   }
  ```
### <a name="step-2-configure-load-balancer-resources"></a>ШАГ 2. Настройка ресурсов подсистемы балансировки нагрузки
На этом этапе вы настроите ресурсы подсистемы балансировки нагрузки *lb*, включая конфигурацию IP-адреса внешнего интерфейса (*fe-lb*), пул адресов серверной части (*be-lb*), правило балансировки нагрузки (*rulelb*), проверку работоспособности (*probe-lb*) и правило NAT для входящего трафика (*in-nat-rule*).
#### <a name="sample-request"></a>Пример запроса

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>Тело запроса

  ```JSON
{
  "properties": {
    "frontendIPConfigurations": [
      {
        "name": "fe-lb",
        "properties": {
          "subnet": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/vnetlb/subnets/subnetlb"
          },
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ],
          "inboundNatRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/inboundNatRules/in-nat-rule"
            }  ]  }  }  ],
    "backendAddressPools": [
      {
        "name": "be-lb",
        "properties": {
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]   }   }   ],
    "loadBalancingRules": [
      {
        "name": "rulelb",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 80,
          "backendPort": 80,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp",
          "loadDistribution": "Default",
          "backendAddressPool": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/backendAddressPools/be-lb"
          },
          "probe": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/probes/probe-lb"
          }  }  }  ],
    "probes": [
      {
        "name": "probe-lb",
        "properties": {
          "protocol": "Http",
          "port": 80,
          "requestPath": "healthcheck.aspx",
          "intervalInSeconds": 15,
          "numberOfProbes": 2,
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]  }  } ],
    "inboundNatRules": [
      {
        "name": "in-nat-rule",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 3389,
          "backendPort": 3389,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp"
        } } ],
    "inboundNatPools": [],
    "outboundNatRules": []
  }  }
```
