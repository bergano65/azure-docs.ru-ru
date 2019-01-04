---
title: 'Сведения о сбросе канала ExpressRoute после сбоя с помощью PowerShell: Azure | Документация Майкрософт'
description: Эта статья поможет вам сбросить канал ExpressRoute, который находится в неисправном состоянии.
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: article
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: 7b88ba6e00cbec05263fe5bc8e795cda95beee04
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53093681"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Сброс канала ExpressRoute после сбоя

Если операция на канале ExpressRoute не выполнена, канал может перейти в состояние сбоя. Эта статья поможет вам сбросить канал Azure ExpressRoute после сбоя.

## <a name="reset-a-circuit"></a>Сброс канала

1. Установите последнюю версию командлетов PowerShell для Azure Resource Manager. Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/install-azurerm-ps).

2. Откройте консоль PowerShell с повышенными привилегиями и подключитесь к своей учетной записи. Для подключения используйте следующий пример кода:

  ```azurepowershell-interactive
  Connect-AzureRmAccount
  ```
3. Если у вас есть несколько подписок Azure, проверьте подписки для этой учетной записи.

  ```azurepowershell-interactive
  Get-AzureRmSubscription
  ```
4. Укажите подписку, которую нужно использовать.

  ```azurepowershell-interactive
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
5. Чтобы сбросить канал, который находится в состоянии сбоя, выполните следующие команды:

  ```azurepowershell-interactive
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

Теперь канал должен быть работоспособным. Откройте запрос в [службу поддержки Майкрософт](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), если канал по-прежнему находится в состоянии сбоя.

## <a name="next-steps"></a>Дополнительная информация

Отправьте запрос в [службу поддержки Майкрософт](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , если у вас по-прежнему возникают проблемы.
