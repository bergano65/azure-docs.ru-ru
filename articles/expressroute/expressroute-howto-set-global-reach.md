---
title: 'Настройка Global Reach — ExpressRoute: Azure | Документация Майкрософт'
description: В этой статье содержатся сведения о том, как связать каналы ExpressRoute, чтобы настроить частную сеть между локальными сетями и включить Global Reach.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: ab1098ca65ad92cffdbe1dfb24fd43fcc8f10eae
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54431684"
---
# <a name="configure-expressroute-global-reach-preview"></a>Настройка ExpressRoute Global Reach (предварительная версия)
Эта статья поможет вам настроить ExpressRoute Global Reach с помощью PowerShell. Дополнительные сведения см. в разделе [Связывание каналов ExpressRoute для включения ExpressRoute Global Reach (предварительная версия)](expressroute-global-reach.md).
 
## <a name="before-you-begin"></a>Перед началом работы
> [!IMPORTANT]
> Эта общедоступная предварительная версия предоставляется без соглашения об уровне обслуживания и не должна использоваться для производственных рабочих нагрузок. Некоторые функции могут не поддерживаться, иметь ограничения и быть доступными не во всех расположениях Azure. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 


Прежде чем начать настройку, проверьте следующее:

* Установлена ли последняя версия Azure PowerShell. Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
* Ознакомьтесь с [рабочими процессами](expressroute-workflows.md) подготовки канала ExpressRoute.
* Подготовлены ли каналы ExpressRoute.
* Настроен ли частный пиринг Azure для каналов ExpressRoute.  

### <a name="sign-in-to-your-azure-account"></a>Вход в учетную запись Azure
Чтобы начать настройку, войдите в свою учетную запись Azure. 

Откройте консоль PowerShell с повышенными привилегиями и подключитесь к своей учетной записи. Команда запрашивает учетные данные входа для вашей учетной записи Azure.  

```powershell
Connect-AzureRmAccount
```

Если у вас есть несколько подписок Azure, проверьте подписки для этой учетной записи.

```powershell
Get-AzureRmSubscription
```

Укажите подписку, которую нужно использовать.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Определение каналов ExpressRoute для конфигурации
Вы можете включить ExpressRoute Global Reach между любыми двумя каналами ExpressRoute, если они находятся в поддерживаемых странах и созданы в разных расположениях пиринга. Если подписке принадлежат оба канала, вы можете выбрать канал для выполнения конфигурации в следующих разделах. 

Если два канала находятся в разных подписках Azure, может потребоваться выполнить авторизацию из одной подписки Azure. Затем необходимо передать ключ авторизации при выполнении команды конфигурации в другой подписке Azure.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Настройка подключения между локальными сетями

Используйте следующие команды, чтобы получить каналы 1 и 2. Эти два канала содержатся в одной подписке.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

Выполните следующую команду для канала 1 и введите идентификатор частного пиринга канала 2. При выполнении команды обратите внимание на следующее:

* ИД частного пиринга должен быть аналогичным приведенному ниже: 

  ```
  /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
  ```
* *-AddressPrefix* должен быть подсетью IPv4 /29, например 10.0.0.0/29. Мы используем IP-адреса в этой подсети, чтобы установить подключение между двумя каналами ExpressRoute. Не следует использовать адреса в этой подсети в виртуальных сетях Azure или в локальной сети.

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

Сохраните конфигурацию в канале 1 следующим образом:
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

После завершения предыдущей операции должно появиться подключение между локальными сетями с обеих сторон через два канала ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Каналы ExpressRoute в разных подписках Azure

Если два канала находятся в разных подписках Azure, вам потребуется выполнить авторизацию. В следующей конфигурации авторизация создается в подписке канала 2, а ключ авторизации передается в канал 1.

Создайте ключ авторизации. 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
Запишите идентификатор частного пиринга канала 2, а также ключ авторизации.

Запустите следующую команду на канале 1. Введите идентификатор частного пиринга канала 2, а также ключ авторизации.
```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

Сохраните конфигурацию в канале 1.
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

После завершения предыдущей операции должно появиться подключение между локальными сетями с обеих сторон через два канала ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Получение и проверка конфигурации

Используйте следующую команду для проверки конфигурации на канале, на котором они созданы (например, в предыдущем примере это канал 1).

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Если вы просто выполняете *$ckt1* в PowerShell, в выходных данных отобразится *CircuitConnectionStatus*. Таким образом вы узнаете, установлено ли подключение (состояние "Подключено" или "Нет подключения"). 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Отключение подключения между локальными сетями

Чтобы отключить подключение, выполните команды на канале, на котором создана конфигурация (например, в предыдущем примере это канал 1).

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Вы можете выполнить операцию Get для проверки состояния. 

После завершения предыдущей операции вы потеряете подключение к локальной сети через каналы ExpressRoute. 


## <a name="next-steps"></a>Дополнительная информация
1. [Получение дополнительных сведений об ExpressRoute Global Reach](expressroute-global-reach.md).
2. [Проверка подключения ExpressRoute](expressroute-troubleshooting-expressroute-overview.md).
3. [Подключение виртуальной сети к каналу ExpressRoute](expressroute-howto-linkvnet-arm.md).


