---
title: 'Azure VPN Gateway: Реклама пользовательских маршрутов для клиентов P2S VPN'
description: Шаги для рекламы пользовательских маршрутов для ваших клиентов точки к сайту
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/11/2019
ms.author: cherylmc
ms.openlocfilehash: 3588755e2aab1c84d443e917eca8c7fca280b49a
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756894"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>Реклама пользовательских маршрутов для клиентов P2S VPN

Вы можете рекламировать пользовательские маршруты для всех ваших клиентов VPN от точки к сайту. Например, если вы включили конечные точки хранения в VNet и хотите, чтобы удаленные пользователи могли получить доступ к этим учетным записям хранения через VPN-соединение. Вы можете рекламировать IP-адрес конечной точки хранилища для всех ваших удаленных пользователей, так что трафик на учетную запись хранения идет через туннель VPN, а не общедоступный Интернет.

![Пример многосайтового подключения через VPN-шлюз Azure](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>Для рекламы пользовательских маршрутов

Для рекламы пользовательских `Set-AzVirtualNetworkGateway cmdlet`маршрутов, используйте . Ниже приводится следующий пример, как рекламировать IP для [таблиц хранения Contoso.](https://contoso.table.core.windows.net)

1. Пинг *contoso.table.core.windows.net* и отметить IP-адрес. Пример:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. Выполните следующие команды PowerShell:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Чтобы добавить несколько пользовательских маршрутов, используйте запятую и пробелы, чтобы отделить адреса. Пример:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>Для просмотра пользовательских маршрутов

Используйте следующий пример для просмотра пользовательских маршрутов:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="to-delete-custom-routes"></a>Удаление пользовательских маршрутов

Используйте следующий пример для удаления пользовательских маршрутов:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>Дальнейшие действия

Дополнительную информацию о размывании P2S [см.](vpn-gateway-about-point-to-site-routing.md)
