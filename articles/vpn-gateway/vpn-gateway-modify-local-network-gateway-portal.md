---
title: Изменение префиксов IP-адресов шлюза локальной сети и IP-адреса VPN-шлюза | Azure | Портал | Документация Майкрософт
description: Из этой статьи вы узнаете, как изменять префиксы IP-адресов для шлюза локальной сети с помощью портала Azure.
services: vpn-gateway
documentationcenter: na
author: WenJason
manager: digimobile
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 06/19/2017
ms.date: 12/10/2018
ms.author: v-jay
ms.openlocfilehash: 12f1f8bbcb103d0882059cadc12bc1a8b9d40bdb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419614"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Изменение параметров шлюза локальной сети с помощью портала Azure

Иногда такие параметры шлюза локальной сети, как AddressPrefix или GatewayIPAddress, могут изменяться. В этой статье описывается, как изменить параметры шлюза локальной сети. Эти параметры можно изменить с использованием другого метода, выбрав вариант из следующего списка:

Прежде чем удалить подключение, можно скачать конфигурацию для подключения устройств, чтобы получить определенный ключ PSK. Таким образом, не потребуется переопределять его на другой стороне.

> [!div class="op_single_selector"]
> * [портал Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Интерфейс командной строки Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="ipaddprefix"></a>Изменение префиксов IP-адресов

При изменении префиксов IP-адресов выполняемые действия зависят от того, имеет ли шлюз локальной сети подключение.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="gwip"></a>Изменение IP-адреса шлюза

Если общедоступный IP-адрес VPN-устройства, к которому вы хотите подключиться, изменился, измените шлюз локальной сети в соответствии с изменениями. При изменении общедоступного IP-адреса выполняемые действия зависят от того, имеет ли шлюз локальной сети подключение.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Дальнейшие действия

Проверьте подключение шлюза. См. статью [Проверка подключения шлюза](vpn-gateway-verify-connection-resource-manager.md).