---
title: Настройка Azure ExpressRoute Global Reach с помощью Azure CLI | Документация Майкрософт
description: В этой статье содержатся сведения о том, как связать каналы ExpressRoute, чтобы настроить частную сеть между локальными сетями и включить Global Reach.
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: cherylmc
ms.openlocfilehash: 9d41ab26876d464187466f566bbfafc4861c799d
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333300"
---
# <a name="configure-expressroute-global-reach-using-azure-cli-preview"></a>Настройка ExpressRoute Global Reach с помощью Azure CLI (предварительная версия)
Эта статья поможет вам настроить ExpressRoute Global Reach с помощью Azure CLI. Дополнительные сведения см. в разделе [Связывание каналов ExpressRoute для включения ExpressRoute Global Reach (предварительная версия)](expressroute-global-reach.md).
 
## <a name="before-you-begin"></a>Перед началом работы
> [!IMPORTANT]
> Эта общедоступная предварительная версия предоставляется без соглашения об уровне обслуживания и не должна использоваться для производственных рабочих нагрузок. Некоторые функции могут не поддерживаться, иметь ограничения и быть доступными не во всех расположениях Azure. См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 


Перед началом настройки необходимо проверить следующие требования.

* Установите последнюю версию Azure CLI. См. дополнительные сведения об [установке Azure CLI](/cli/azure/install-azure-cli) и [началу работы с Azure CLI](/cli/azure/get-started-with-azure-cli).
* Ознакомьтесь с [рабочими процессами](expressroute-workflows.md) подготовки канала ExpressRoute.
* Убедитесь, что каналы ExpressRoute подготовлены.
* Убедитесь, что частный пиринг Azure настроен для каналов ExpressRoute.  

### <a name="log-into-your-azure-account"></a>Вход в учетную запись Azure
Чтобы приступить к настройке, необходимо войти в учетную запись Azure. Эта команда откроет ваш браузер по умолчанию и запросит учетные данные входа в вашу учетную запись Azure.  

```azurecli
az login
```

Если у вас есть несколько подписок Azure, проверьте подписки для этой учетной записи.

```azurecli
az account list
```

Укажите подписку, которую нужно использовать.

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Определение каналов ExpressRoute для конфигурации
Вы можете включить ExpressRoute Global Reach между любыми двумя каналами ExpressRoute, если они находятся в поддерживаемых странах и созданы в разных расположениях пиринга. Если подписке принадлежат оба канала, вы можете выбрать канал для выполнения конфигурации в следующих разделах. Если два канала находятся в разных подписках Azure, может потребоваться выполнить авторизацию из одной подписки Azure и передать ключ авторизации при выполнении команды конфигурации в другой подписке Azure.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Настройка подключения между локальными сетями

Запустите следующий интерфейс командной строки для подключения двух каналов ExpressRoute.

> [!NOTE]
> *peer-circuit* должен представлять полный идентификатор ресурса, например
> ```
> */subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}*
> ```
> 

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

> [!IMPORTANT]
> *-AddressPrefix* должен быть подсетью IPv4/29, например "10.0.0.0/29". Мы будем использовать IP-адреса в этой подсети, чтобы установить подключение между двумя каналами ExpressRoute. Не следует использовать адреса в этой подсети в виртуальных сетях Azure или в локальных сетях.
> 

Выходные данные CLI будут выглядеть примерно так:

```azurecli
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

После завершения предыдущей операции должно появиться подключение между локальными сетями с обеих сторон через два канала ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Каналы ExpressRoute в разных подписках Azure

Если два канала находятся в разных подписках Azure, вам потребуется выполнить авторизацию. В следующей конфигурации авторизация создается в подписке канала 2, а ключ авторизации передается в канал 1.

Создайте ключ авторизации. 
```azurecli
az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
```

Выходные данные CLI будут выглядеть примерно так:

```azurecli
{
  "authorizationKey": "<authorizationKey>",
  "authorizationUseStatus": "Available",
  "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
  "name": "<AuthorizationName>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<Circuit2ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/authorizations"
}
```

Запишите идентификатор ресурса канала 2, а также ключ авторизации.

Запустите следующую команду на канале 1. Передайте идентификатор ресурса канала 2, а также ключ авторизации. 
```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
```

После завершения предыдущей операции должно появиться подключение между локальными сетями с обеих сторон через два канала ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Получение и проверка конфигурации

Используйте следующую команду для проверки конфигурации на канале, на котором они созданы, например в приведенном выше примере канал 1.

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

В выходных данных CLI вы увидите *CircuitConnectionStatus*. Так вы узнаете, установлено ли подключение между двумя каналами — Connected (Подключено) или Disconnected (Отключено). 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Отключение подключения между локальными сетями

Чтобы отключить подключение, выполните команды на канале, на котором создана конфигурация, например в приведенном выше примере это канал 1.

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Для проверки состояния можно выполнить команду show cli. 

После завершения предыдущей операции вы потеряете подключение к локальной сети через каналы ExpressRoute. 


## <a name="next-steps"></a>Дополнительная информация
* [Получение дополнительных сведений об ExpressRoute Global Reach](expressroute-global-reach.md).
* [Проверка подключения ExpressRoute](expressroute-troubleshooting-expressroute-overview.md).
* [Связывание виртуальной сети Azure с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md).


