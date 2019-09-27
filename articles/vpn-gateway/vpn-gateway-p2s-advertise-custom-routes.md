---
title: Объявление пользовательских маршрутов для VPN-клиентов типа "точка — сеть" | Azure | Документация Майкрософт
description: Инструкции по объявлению пользовательских маршрутов для клиентов "точка — сеть"
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/26/2019
ms.author: cherylmc
ms.openlocfilehash: 38250d1cd9853013ba9721ece0201a8df6dd1b4a
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71336293"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>Объявление пользовательских маршрутов для VPN-клиентов P2S

Вам может потребоваться объявить пользовательские маршруты всем клиентам VPN типа "точка — сеть". Например, если вы включили конечные точки хранилища в виртуальной сети и хотите, чтобы удаленные пользователи имели доступ к этим учетным записям хранения через VPN-подключение. Вы можете объявить IP-адрес конечной точки хранилища всем удаленным пользователям, чтобы трафик в учетной записи хранения перенаправлялся через туннель VPN, а не в общедоступный Интернет.

![Пример многосайтового подключения через VPN-шлюз Azure](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>Объявление пользовательских маршрутов

Чтобы объявить пользовательские маршруты, используйте `Set-AzVirtualNetworkGateway cmdlet`. В следующем примере показано, как объявить IP-адрес для [таблиц учетной записи хранения Contoso](https://contoso.table.core.windows.net).

1. Проверьте связь с *contoso.Table.Core.Windows.NET* и запишите IP-адрес. Пример:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. Выполните следующие команды PowerShell:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Чтобы добавить несколько настраиваемых маршрутов, используйте запятыми и пробелы для разделения адресов. Пример:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>Просмотр настраиваемых маршрутов

Для просмотра настраиваемых маршрутов используйте следующий пример:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о маршрутизации P2S см. в статье [о маршрутизации "точка — сеть](vpn-gateway-about-point-to-site-routing.md)".
