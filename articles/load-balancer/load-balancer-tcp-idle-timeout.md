---
title: Настройка TCP-сброса и времени ожидания простоя подсистемы балансировки нагрузки в Azure
titleSuffix: Azure Load Balancer
description: Из этой статьи вы узнаете, как настроить время ожидания простоя TCP для Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/09/2020
ms.author: allensu
ms.openlocfilehash: 26c4c01aaf6abe6b9c9ac6daf6836d7b660ba21e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91649870"
---
# <a name="configure-tcp-idle-timeout-for-azure-load-balancer"></a>Настройка времени ожидания простоя TCP для Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Чтобы установить и использовать PowerShell локально, для работы с этой статьей вам понадобится модуль Azure PowerShell 5.4.1 или более поздней версии. Выполните командлет `Get-Module -ListAvailable Az`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-Az-ps). При использовании PowerShell на локальном компьютере также нужно запустить `Connect-AzAccount`, чтобы создать подключение к Azure.

Azure Load Balancer имеет значение времени ожидания простоя от 4 минут до 120 минут. По умолчанию установлено значение 4 минуты. Если период бездействия превышает значение времени ожидания, нет никакой гарантии, что сеанс TCP или HTTP между клиентом и облачной службой возобновится. Дополнительные сведения о [времени ожидания простоя TCP](load-balancer-tcp-reset.md).

В следующих разделах показано, как изменить параметры времени ожидания простоя для ресурсов общедоступного IP-адреса и подсистемы балансировки нагрузки.


## <a name="configure-the-tcp-idle-timeout-for-your-public-ip"></a>Настройка времени ожидания простоя TCP для общедоступного IP-адреса

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

Аргумент `IdleTimeoutInMinutes` является необязательным. Если он не задан, время ожидания по умолчанию будет составлять 4 минуты. Допустимый диапазон времени ожидания составляет от 4 до 120 минут.

## <a name="set-the-tcp-idle-timeout-on-rules"></a>Установка времени ожидания простоя TCP для правил

Чтобы установить время ожидания простоя для подсистемы балансировки нагрузки, в правиле с балансировкой нагрузки задается значение "IdleTimeoutInMinutes". Пример:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```

## <a name="next-steps"></a>Дальнейшие действия

[Обзор внутренней подсистемы балансировки нагрузки](load-balancer-internal-overview.md)

[Приступая к настройке балансировщика нагрузки для Интернета](quickstart-load-balancer-standard-public-powershell.md)

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)
