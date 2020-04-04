---
title: 'Azure ExpressRoute: Настройка глобального охвата'
description: В этой статье содержатся сведения о том, как связать каналы ExpressRoute, чтобы настроить частную сеть между локальными сетями и включить Global Reach.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.openlocfilehash: 587a17659a412d6f894faf5a744a7d9c444935c8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656745"
---
# <a name="configure-expressroute-global-reach"></a>Настройка ExpressRoute Global Reach

Эта статья поможет вам настроить ExpressRoute Global Reach с помощью PowerShell. Дополнительные сведения см. в разделе [Связывание каналов ExpressRoute для включения ExpressRoute Global Reach (предварительная версия)](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Подготовка к работе

Прежде чем начать настройку, проверьте следующее:

* Вы понимаете, ExpressRoute цепи подготовки [рабочих процессов](expressroute-workflows.md).
* Ваши схемы ExpressRoute находятся в подготовленном состоянии.
* Частное пиринг Azure настроен на схемах ExpressRoute.
* Если вы хотите запустить PowerShell локально, убедитесь, что последняя версия Azure PowerShell установлена на вашем компьютере.

### <a name="working-with-azure-powershell"></a>Работа с Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>Определение схем

1. Чтобы запустить конфигурацию, вопийте в учетную запись Azure и выберите подписку, которую вы хотите использовать.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Определите схемы ExpressRoute, которые вы хотите использовать. Вы можете включить ExpressRoute Global Reach между частным пирингом любых двух схем ExpressRoute, если они расположены в поддерживаемых странах/регионах и были созданы в разных местах для пиринга. 

   * Если подписке принадлежат оба канала, вы можете выбрать канал для выполнения конфигурации в следующих разделах.
   * Если два канала находятся в разных подписках Azure, может потребоваться выполнить авторизацию из одной подписки Azure. Затем необходимо передать ключ авторизации при выполнении команды конфигурации в другой подписке Azure.

## <a name="enable-connectivity"></a>Включение соединения

Включите подключение между вашими сетей. Существуют отдельные наборы инструкций для схем, которые находятся в той же подписке Azure, и схемы, которые являются различными подписками.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>Схемы ExpressRoute в той же подписке Azure

1. Используйте следующие команды, чтобы получить каналы 1 и 2. Эти два канала содержатся в одной подписке.

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. Выполните следующую команду для канала 1 и введите идентификатор частного пиринга канала 2. При выполнении команды обратите внимание на следующее:

   * ИД частного пиринга должен быть аналогичным приведенному ниже: 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-AddressPrefix* должен быть подсетью IPv4 /29, например 10.0.0.0/29. Мы используем IP-адреса в этой подсети, чтобы установить подключение между двумя каналами ExpressRoute. Не следует использовать адреса в этой подсети в виртуальных сетях Azure или в локальной сети.

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. Сохраните конфигурацию в канале 1 следующим образом:

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Когда предыдущая операция завершится, у вас будет подключение между вашими сетей с обеих сторон через две схемы ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Каналы ExpressRoute в разных подписках Azure

Если два канала находятся в разных подписках Azure, вам потребуется выполнить авторизацию. В следующей конфигурации авторизация создается в подписке канала 2, а ключ авторизации передается в канал 1.

1. Создайте ключ авторизации.

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   Запишите идентификатор частного пиринга канала 2, а также ключ авторизации.
2. Запустите следующую команду на канале 1. Введите идентификатор частного пиринга канала 2, а также ключ авторизации.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. Сохраните конфигурацию в канале 1.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Когда предыдущая операция завершится, у вас будет подключение между вашими сетей с обеих сторон через две схемы ExpressRoute.

## <a name="verify-the-configuration"></a>Проверка конфигурации

Используйте следующую команду для проверки конфигурации на канале, на котором они созданы (например, в предыдущем примере это канал 1).
```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Если вы просто выполняете *$ckt1* в PowerShell, в выходных данных отобразится *CircuitConnectionStatus*. Таким образом вы узнаете, установлено ли подключение (состояние "Подключено" или "Нет подключения"). 

## <a name="disable-connectivity"></a>Отключение возможности подключения

Чтобы отключить подключение между вашими сетей, запустите команды по схеме, где была сделана конфигурация (например, схема 1 в предыдущем примере).

```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Вы можете выполнить операцию Get для проверки состояния.

После завершения предыдущей операции вы потеряете подключение к локальной сети через каналы ExpressRoute.

## <a name="next-steps"></a>Дальнейшие действия
1. [Получение дополнительных сведений об ExpressRoute Global Reach](expressroute-global-reach.md).
2. [Проверить подключение ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Подключение виртуальной сети к каналу ExpressRoute](expressroute-howto-linkvnet-arm.md).
