---
title: Настройка OpenVPN для VPN-шлюза Azure — PowerShell | Документация Майкрософт
description: Узнайте, как настроить OpenVPN для VPN-шлюза Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: cherylmc
ms.openlocfilehash: 1dad960b0877cddf3be9afc01e3e687ebe4702c0
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54357833"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>Настройка OpenVPN для подключения "точка — сеть" VPN-шлюза Azure (предварительная версия)

Эта статья поможет вам настроить OpenVPN для VPN-шлюза Azure. В этой статье предполагается наличие рабочей среды "точка — сеть". В противном случае см. инструкции на шаге 1 по созданию VPN-подключения "точка — сайт".

> [!IMPORTANT]
> Общедоступная предварительная версия предоставляется без соглашения об уровне обслуживания и не должна использоваться для производственных рабочих нагрузок. Некоторые функции могут не поддерживаться, иметь ограничения и быть доступными не во всех расположениях Azure. См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register"></a>Регистрация этой возможности

Нажмите кнопку **Попробовать** и выполните указанные действия, чтобы зарегистрировать эту возможность с помощью Azure Cloud Shell.

>[!NOTE]
>Если не зарегистрировать эту возможность, вы не сможете ею пользоваться.
>

После нажатия кнопки **Попробовать**, чтобы открыть Azure Cloud Shell, скопируйте и вставьте следующие команды:

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```
 
```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

Когда возможность отобразится как зарегистрированная, перерегистрируйте подписку на пространство имен Microsoft.Network.

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1. Создание VPN-подключения "— сеть"

Если у вас нет работающей среды "точка — сеть", следуйте инструкциям, чтобы создать ее. См. дополнительные сведения о [создании и настройке VPN-подключения "точка — сеть" с использованием аутентификации на основе собственного сертификата Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md). Обратите внимание на то, что SKU уровня " Базовый" не поддерживает подключения типа "точка — сеть" по протоколу IKEv2.

## <a name="cmdlets"></a>2. Установка командлетов PowerShell

Установите последнюю версию командлетов PowerShell для Resource Manager. Дополнительные сведения об установке командлетов PowerShell см. в статье [Overview of Azure PowerShell](/powershell/azure/overview) (Обзор Azure PowerShell). Это важно, так как более ранние версии командлетов не содержат текущие значения, необходимые в этом сценарии.

## <a name="enable"></a>3. Включение OpenVPN в шлюзе

Включите OpenVPN в шлюзе. Убедитесь, что шлюз уже настроен для подключения "точка — сеть" (IKEv2 или SSTP), прежде чем выполнять следующие команды:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Дополнительная информация

См. дополнительные сведения о [настройке клиентов OpenVPN](vpn-gateway-howto-openvpn-clients.md).
