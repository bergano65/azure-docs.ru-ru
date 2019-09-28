---
title: Переопределение подсети диспетчера трафика Azure с помощью Azure PowerShell | Документация Майкрософт
description: Эта статья поможет вам понять, как переопределение подсети диспетчера трафика переопределяет метод маршрутизации профиля диспетчера трафика, чтобы направить трафик в конечную точку на основе IP-адреса конечного пользователя через предопределенный диапазон IP-адресов для сопоставления конечных точек с помощью Azure. Оболочк.
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: 93362c0e1b359583e30886172f8ccb155c5ffaf4
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348888"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>Переопределение подсети диспетчера трафика с помощью Azure PowerShell

Переопределение подсети диспетчера трафика позволяет изменить метод маршрутизации профиля.  Добавление переопределения направляет трафик, основанный на IP-адресе конечного пользователя, с предопределенным диапазоном IP-адресов для сопоставления конечных точек. 

## <a name="how-subnet-override-works"></a>Как работает переопределение подсети

Когда переопределение подсети добавляется в профиль диспетчера трафика, диспетчер трафика сначала проверяет, есть ли переопределение подсети для IP-адреса конечного пользователя. Если он найден, запрос DNS пользователя будет перенаправлен в соответствующую конечную точку.  Если сопоставление не найдено, диспетчер трафика будет возвращаться к исходному методу маршрутизации профиля. 

Диапазоны IP-адресов могут быть указаны либо в виде диапазонов CIDR (например, 1.2.3.0/24), либо в качестве диапазонов адресов (например, 1.2.3.4-5.6.7.8). Диапазоны IP-адресов, связанные с каждой конечной точкой, должны быть уникальными для этой конечной точки. Любое перекрытие диапазонов IP-адресов между разными конечными точками приведет к тому, что профиль будет отклонен диспетчером трафика.

Существуют два типа профилей маршрутизации, которые поддерживают переопределения подсетей.

* **География** . Если диспетчер трафика находит переопределение подсети для IP-адреса DNS-запроса, он направляет запрос в конечную точку, независимо от работоспособности конечной точки.
* **Производительность** . Если диспетчер трафика находит переопределение подсети для IP-адреса DNS-запроса, он будет направлять трафик в конечную точку только в том случае, если он исправен.  Диспетчер трафика будет передаваться на эвристику маршрутизации производительности, если конечная точка переопределения подсети находится в неработоспособном состоянии.

## <a name="create-a-traffic-manager-subnet-override"></a>Создание переопределения подсети диспетчера трафика

Чтобы создать переопределение подсети диспетчера трафика, можно использовать Azure PowerShell, чтобы добавить подсети для переопределения в конечную точку диспетчера трафика.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Вы можете выполнить приведенные ниже команды в [Azure Cloud Shell](https://shell.azure.com/powershell) или с помощью PowerShell на своем компьютере. Azure Cloud Shell — это бесплатная интерактивная оболочка. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. Если вы решили запустить PowerShell на своем компьютере, вам потребуется модуль Azure PowerShell 1.0.0 или более поздней версии. Выполните командлет `Get-Module -ListAvailable Az`, чтобы узнать установленную версию. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzAccount`, чтобы войти в Azure.


1. **Получите конечную точку диспетчера трафика.**

    Чтобы включить переопределение подсети, извлеките конечную точку, в которую нужно добавить переопределение, и сохраните ее в переменной с помощью командлета [Get-азтраффикманажерендпоинт](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Замените Name, имя_профиля и ResourceGroupName значениями изменяемой конечной точки.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Добавьте диапазон IP-адресов в конечную точку.**
    
    Чтобы добавить диапазон IP-адресов в конечную точку, используйте [Add-азтраффикманажерипаддрессранже](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange?view=azps-2.5.0&viewFallbackFrom=azps-2.4.0) , чтобы добавить диапазон.

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    После добавления диапазонов используйте [Set-азтраффикманажерендпоинт](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) , чтобы обновить конечную точку.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
Удаление диапазона IP-адресов может быть выполнено с помощью [Remove-азтраффикманажерипаддрессранже](https://docs.microsoft.com/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange?view=azps-2.5.0).

1.  **Получите конечную точку диспетчера трафика.**

    Чтобы включить переопределение подсети, извлеките конечную точку, в которую нужно добавить переопределение, и сохраните ее в переменной с помощью командлета [Get-азтраффикманажерендпоинт](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Замените Name, имя_профиля и ResourceGroupName значениями изменяемой конечной точки.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Удалите диапазон IP-адресов из конечной точки:**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     После удаления диапазонов используйте [Set-азтраффикманажерендпоинт](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) , чтобы обновить конечную точку.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>Следующие шаги
См. дополнительные сведения в статье [Методы маршрутизации трафика диспетчером трафика](traffic-manager-routing-methods.md).

Сведения о [методе маршрутизации трафика подсети](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)
