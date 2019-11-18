---
title: 'VPN-шлюз Azure: объявление настраиваемых маршрутов для VPN-клиентов P2S'
description: Инструкции по объявлению пользовательских маршрутов для клиентов "точка — сеть"
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/11/2019
ms.author: cherylmc
ms.openlocfilehash: 7a904857b8aa0ed2aa18fc2a1b81fe31541e6f9e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151904"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>Объявление пользовательских маршрутов для VPN-клиентов P2S

Вам может потребоваться объявить пользовательские маршруты всем клиентам VPN типа "точка — сеть". Например, если вы включили конечные точки хранилища в виртуальной сети и хотите, чтобы удаленные пользователи имели доступ к этим учетным записям хранения через VPN-подключение. Вы можете объявить IP-адрес конечной точки хранилища всем удаленным пользователям, чтобы трафик в учетной записи хранения перенаправлялся через туннель VPN, а не в общедоступный Интернет.

![Пример многосайтового подключения через VPN-шлюз Azure](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>Объявление пользовательских маршрутов

Чтобы объявить пользовательские маршруты, используйте `Set-AzVirtualNetworkGateway cmdlet`. В следующем примере показано, как объявить IP-адрес для [таблиц учетной записи хранения Contoso](https://contoso.table.core.windows.net).

1. Проверьте связь с *contoso.Table.Core.Windows.NET* и запишите IP-адрес. Например,

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. Выполните следующие команды PowerShell:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Чтобы добавить несколько настраиваемых маршрутов, используйте запятыми и пробелы для разделения адресов. Например,

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>Просмотр настраиваемых маршрутов

Для просмотра настраиваемых маршрутов используйте следующий пример:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="to-delete-custom-routes"></a>Удаление настраиваемых маршрутов

Для удаления настраиваемых маршрутов используйте следующий пример:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о маршрутизации P2S см. в статье [о маршрутизации "точка — сеть](vpn-gateway-about-point-to-site-routing.md)".
