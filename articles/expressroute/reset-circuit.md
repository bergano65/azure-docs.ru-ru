---
title: 'Сброс канала с ошибками — ExpressRoute: PowerShell: Azure | Документация Майкрософт'
description: Эта статья поможет вам сбросить канал ExpressRoute, который находится в неисправном состоянии.
services: expressroute
author: kumudD
ms.service: expressroute
ms.topic: how-to
ms.date: 11/28/2018
ms.author: kumud
ms.custom: seodec18
ms.openlocfilehash: e0f79ce0959e7b7dccc20e46493f34e1963df70e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537111"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Сброс канала ExpressRoute после сбоя

Если операция на канале ExpressRoute не выполнена, канал может перейти в состояние сбоя. Эта статья поможет вам сбросить канал Azure ExpressRoute после сбоя.

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="reset-a-circuit"></a>Сброс канала

1. Установите последнюю версию командлетов PowerShell для Azure Resource Manager. Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/install-az-ps).

2. Откройте консоль PowerShell с повышенными привилегиями и подключитесь к своей учетной записи. Для подключения используйте следующий пример кода:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
3. Если у вас есть несколько подписок Azure, проверьте подписки для этой учетной записи.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
4. Укажите подписку, которую нужно использовать.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
5. Чтобы сбросить канал, который находится в состоянии сбоя, выполните следующие команды:

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

Теперь канал должен быть работоспособным. Откройте запрос в [службу поддержки Майкрософт](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), если канал по-прежнему находится в состоянии сбоя.

## <a name="next-steps"></a>Дальнейшие действия

Отправьте запрос в [службу поддержки Майкрософт](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , если у вас по-прежнему возникают проблемы.
