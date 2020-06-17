---
title: Настройка времени ожидания простоя TCP для подсистемы балансировки нагрузки Azure
titleSuffix: Azure Load Balancer
description: Из этой статьи вы узнаете, как настроить время ожидания простоя TCP для Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: 09d15877088fb6356419a9d31f8bef3164e76029
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780602"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Настройка параметров времени ожидания простоя TCP для Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Чтобы установить и использовать PowerShell локально, для работы с этой статьей вам понадобится модуль Azure PowerShell 5.4.1 или более поздней версии. Выполните командлет `Get-Module -ListAvailable Az`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-Az-ps). При использовании PowerShell на локальном компьютере также нужно запустить `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="tcp-idle-timeout"></a>Время ожидания простоя TCP
Время ожидания простоя для Azure Load Balancer допускает значения от 4 до 30 минут. По умолчанию установлено значение 4 минуты. Если период бездействия превышает значение времени ожидания, нет никакой гарантии, что сеанс TCP или HTTP между клиентом и облачной службой возобновится.

При закрытии подключения клиентское приложение может получить такое сообщение об ошибке: "Базовое соединение закрыто. Соединение, которое должно было работать, было разорвано сервером".

Распространенной практикой является проверка активности TCP. Такой подход позволяет поддерживать подключения активными в течение более длительного периода. Дополнительные сведения вы найдете в следующих [примерах для .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Когда проверка активности включена, пакеты отправляются в периоды простоя подключений. Благодаря пакетам проверки активности значение времени ожидания простоя не достигается и подключение сохраняется в течение длительного времени.

Этот параметр применяется только для входящих подключений. Чтобы избежать потери подключения, настройте пакеты проверки активности для TCP с интервалом меньше, чем настроенное время ожидания простоя. Можно также увеличить значение времени ожидания простоя. Для поддержки таких сценариев добавлена возможность настройки времени ожидания простоя.

Проверки активности TCP хорошо подходят для сценариев, в которых не нужно беспокоиться о расходе заряда аккумулятора. Мы не рекомендуем использовать этот вариант для мобильных приложений. Использование этого метода в мобильном приложении может привести к ускоренной разрядке аккумулятора устройства.

![Время ожидания TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

В следующих разделах показано, как изменить параметры времени ожидания простоя для ресурсов общедоступного IP-адреса и подсистемы балансировки нагрузки.

>[!NOTE]
> Время ожидания простоя TCP не влияет на правила балансировки нагрузки по протоколу UDP.


## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Настройка для времени ожидания TCP, равного 15 минутам, для общедоступного IP-адреса уровня экземпляра

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

Аргумент `IdleTimeoutInMinutes` является необязательным. Если он не задан, время ожидания по умолчанию будет составлять 4 минуты. Допустимый диапазон времени ожидания — от 4 до 30 минут.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>Настройка значения 15 минут для времени ожидания TCP в правиле с балансировкой нагрузки

Чтобы установить время ожидания простоя для подсистемы балансировки нагрузки, в правиле с балансировкой нагрузки задается значение "IdleTimeoutInMinutes". Пример:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>Дальнейшие действия

[Обзор внутренней подсистемы балансировки нагрузки](load-balancer-internal-overview.md)

[Приступая к настройке балансировщика нагрузки для Интернета](quickstart-create-standard-load-balancer-powershell.md)

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)
