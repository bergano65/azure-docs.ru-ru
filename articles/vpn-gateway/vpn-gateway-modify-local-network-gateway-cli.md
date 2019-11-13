---
title: 'VPN-шлюз: изменение параметров IP-адреса шлюза: Azure CLI'
description: Из этой статьи вы узнаете, как изменять префиксы IP-адресов для шлюза локальной сети с помощью Azure CLI.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: 26462f73680d35371c6a03c3224742626e8f2090
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014969"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Изменение параметров шлюза локальной сети с помощью Azure CLI

Иногда такие параметры шлюза локальной сети, как AddressPrefix или GatewayIPAddress, могут изменяться. В этой статье описывается, как изменить параметры шлюза локальной сети. Эти параметры можно изменить с использованием другого метода, выбрав вариант из следующего списка:

> [!div class="op_single_selector"]
> * [портал Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Интерфейс командной строки Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Перед началом работы

Установите последнюю версию команд интерфейса командной строки (версии 2.0 или более позднюю). Сведения об установке команд CLI см. в руководстве по [установке Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="ipaddprefix"></a>Изменение префиксов IP-адресов

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="gwip"></a>Изменение IP-адреса шлюза

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Дополнительная информация

Проверьте подключение шлюза. См. статью [Проверка подключения шлюза](vpn-gateway-verify-connection-resource-manager.md).

