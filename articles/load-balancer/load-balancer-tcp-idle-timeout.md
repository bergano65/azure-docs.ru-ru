---
title: Настройка времени ожидания простоя TCP в подсистеме балансировки нагрузки в Azure
titleSuffix: Azure Load Balancer
description: Из этой статьи вы узнаете, как настроить Azure Load Balancer время ожидания простоя TCP.
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
ms.openlocfilehash: d0bb73b58aa23e5f7eb784772acf37b05df463ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79456834"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Настройка параметров времени ожидания простоя TCP для Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Чтобы установить и использовать PowerShell локально, для работы с этой статьей вам понадобится модуль Azure PowerShell 5.4.1 или более поздней версии. Выполните командлет `Get-Module -ListAvailable Az`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-Az-ps). При использовании PowerShell на локальном компьютере также нужно запустить `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="tcp-idle-timeout"></a>Время ожидания простоя TCP
Azure Load Balancer имеет значение времени ожидания простоя от 4 минут до 30 минут. Значение по умолчанию — 4 минуты. Если период бездействия превышает значение времени ожидания, нет никакой гарантии, что сеанс TCP или HTTP между клиентом и облачной службой возобновится.

При закрытии подключения клиентское приложение может получить такое сообщение об ошибке: "Базовое соединение закрыто. Соединение, которое должно было работать, было разорвано сервером".

Распространенной практикой является проверка активности TCP. Такой подход позволяет поддерживать подключения активными в течение более длительного периода. Дополнительные сведения вы найдете в следующих [примерах для .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Когда проверка активности включена, пакеты отправляются в периоды простоя подключений. Пакеты поддержания активности гарантируют, что время ожидания простоя не достигается, а подключение сохраняется в течение длительного периода времени.

Этот параметр работает только для входящих подключений. Чтобы избежать потери соединения, настройте TCP-активность в течение интервала, меньшего, чем значение времени ожидания простоя или увеличьте время ожидания простоя. Для поддержки этих сценариев была добавлена поддержка настраиваемого времени ожидания простоя.

Проверка активности TCP выполняется для сценариев, в которых время работы батареи не ограничено. Это не рекомендуется для мобильных приложений. Использование этого метода в мобильном приложении может привести к ускоренной разрядке аккумулятора устройства.

![Время ожидания TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

В следующих разделах описывается изменение параметров времени ожидания простоя для общедоступных IP-адресов и ресурсов подсистемы балансировки нагрузки.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Настройка для времени ожидания TCP, равного 15 минутам, для общедоступного IP-адреса уровня экземпляра

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

Аргумент `IdleTimeoutInMinutes` является необязательным. Если он не задан, время ожидания по умолчанию будет составлять 4 минуты. Допустимый диапазон времени ожидания — от 4 до 30 минут.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>Установите время ожидания TCP для правила балансировки нагрузки 15 минут.

Чтобы установить время ожидания простоя для балансировщика нагрузки, в правиле балансировки нагрузки задается значение "Идлетимеаутинминутес". Пример:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>Дальнейшие действия

[Обзор внутренней подсистемы балансировки нагрузки](load-balancer-internal-overview.md)

[Приступая к настройке балансировщика нагрузки для Интернета](quickstart-create-standard-load-balancer-powershell.md)

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)
