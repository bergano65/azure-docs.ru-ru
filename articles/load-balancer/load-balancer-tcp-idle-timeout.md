---
title: Настройка баланса загрузки TCP холостого хода в Azure
titleSuffix: Azure Load Balancer
description: В этой статье узнайте, как настроить тайм-аут Azure Load Balancer TCP.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456834"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Настройка параметров времени ожидания простоя TCP для Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Чтобы установить и использовать PowerShell локально, для работы с этой статьей вам понадобится модуль Azure PowerShell 5.4.1 или более поздней версии. Выполните командлет `Get-Module -ListAvailable Az`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-Az-ps). При использовании PowerShell на локальном компьютере также нужно запустить `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="tcp-idle-timeout"></a>Тайм-аут TCP без дельталета
Azure Load Balancer имеет настройку времени простоя от 4 минут до 30 минут. По умолчанию он устанавливается на 4 минуты. Если период бездействия превышает значение времени ожидания, нет никакой гарантии, что сеанс TCP или HTTP между клиентом и облачной службой возобновится.

При закрытии подключения клиентское приложение может получить такое сообщение об ошибке: "Базовое соединение закрыто. Соединение, которое должно было работать, было разорвано сервером".

Распространенной практикой является проверка активности TCP. Такой подход позволяет поддерживать подключения активными в течение более длительного периода. Дополнительные сведения вы найдете в следующих [примерах для .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Когда проверка активности включена, пакеты отправляются в периоды простоя подключений. Пакеты Keep-alive гарантируют, что значение времени простоя не будет достигнуто, и подключение поддерживается в течение длительного периода времени.

Настройка работает только для входящих соединений. Чтобы избежать потери соединения, настроите TCP keep-alive с интервалом меньше, чем настройка времени простоя или увеличьте значение тайм-аута простоя. Для поддержки этих сценариев была добавлена поддержка настраиваемого тайм-аута простоя.

TCP keep-alive работает для сценариев, где время автономной работы не является ограничением. Он не рекомендуется для мобильных приложений. Использование этого метода в мобильном приложении может привести к ускоренной разрядке аккумулятора устройства.

![Время ожидания TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

В следующих разделах описывается, как изменить настройки тайм-аута простоя для общедоступных ресурсов ip и балансоровых нагрузок.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Настройка для времени ожидания TCP, равного 15 минутам, для общедоступного IP-адреса уровня экземпляра

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

Аргумент `IdleTimeoutInMinutes` является необязательным. Если он не задан, время ожидания по умолчанию будет составлять 4 минуты. Допустимый диапазон времени ожидания — от 4 до 30 минут.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>Установите тайм-аут TCP на правило, сбалансированное для нагрузки, до 15 минут

Для установки тайм-аута для балансивизатора нагрузки на правило «IdleTimeoutInMinutes» устанавливается на правило, сбалансированное для загрузки. Пример:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>Дальнейшие действия

[Обзор внутренней подсистемы балансировки нагрузки](load-balancer-internal-overview.md)

[Приступая к настройке балансировщика нагрузки для Интернета](quickstart-create-standard-load-balancer-powershell.md)

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)
