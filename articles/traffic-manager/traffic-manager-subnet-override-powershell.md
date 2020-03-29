---
title: Подсеть Менеджера трафика Azure переопределить с помощью Azure PowerShell (ru) Документы Майкрософт
description: Эта статья поможет вам понять, как переопределение подсети Traffic Manager используется для переопределения метода направления профиля менеджера трафика для направления трафика на конечную точку, основанную на IP-адресе конечного пользователя через предопределенный диапазон IP-адреса до конечных карт с помощью Azure Powershell.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 323093ec78a9486d19496b0ee90e37cb42eea341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938422"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>Переопределение подсети traffic Manager с помощью Azure Powershell

Переопределение подсети Traffic Manager позволяет изменить метод реуктора профиля.  Добавление переопределения будет направлять трафик на основе IP-адреса конечного пользователя с предопределенным диапазоном IP-адреса для отображения конечной точки. 

## <a name="how-subnet-override-works"></a>Как работает переопределение подсети

Когда переопределения подсети добавляются в профиль менеджера трафика, диспетчер трафика сначала проверяет, есть ли переопределение подсети для IP-адреса конечномго пользователя. Если один из них найден, dNS-запрос пользователя будет направлен в соответствующую конечную точку.  Если отображение не найдено, диспетчер трафика вернется к исходной методу конечной вымысла профиля. 

Диапазоны IP-адресов могут быть указаны как диапазоны CIDR (например, 1.2.3.0/24), либо как диапазоны адресов (например, 1.2.3.4-5.6.8). Диапазоны IP, связанные с каждой конечной точкой, должны быть уникальными для этой конечных точек. Любое перекрытие диапазонов IP между различными конечными точками приведет к отклонению профиля менеджером трафика.

Существует два типа профилей реанивных профилей, поддерживающих переопределения подсети:

* **Географический** - Если диспетчер трафика находит переопределение подсети для IP-адреса запроса DNS, он будет направлять запрос в конечную точку независимо от работы конечной точки.
* **Производительность** - Если диспетчер трафика находит переопределение подсети для IP-адреса запроса DNS, он будет направлять трафик только в конечную точку, если он здоров.  Диспетчер трафика вернется к эвристическому движению, если конечная точка переопределения подсети не является здоровой.

## <a name="create-a-traffic-manager-subnet-override"></a>Создание переопределения подсети менеджера трафика

Для создания переопределения подсети менеджера трафика можно использовать Azure PowerShell для добавления подсетей для переопределения в конечную точку диспетчера трафика.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Вы можете выполнить приведенные ниже команды в [Azure Cloud Shell](https://shell.azure.com/powershell) или с помощью PowerShell на своем компьютере. Azure Cloud Shell — это бесплатная интерактивная оболочка. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. Если вы решили запустить PowerShell на своем компьютере, вам потребуется модуль Azure PowerShell 1.0.0 или более поздней версии. Выполните командлет `Get-Module -ListAvailable Az`, чтобы узнать установленную версию. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzAccount`, чтобы войти в Azure.


1. **Retrieve диспетчер трафика конечная точка:**

    Чтобы включить переопределение подсети, извлеките конечную точку, к которая вы хотите добавить переопределение, и храните ее в переменной с помощью [Get-AzTrafficManagerEndpoint.](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0)

    Замените имя, имя профиля и Name ResourceGroupName значениями изменяемых конечных точек.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Добавьте диапазон IP-адресов в конечную точку:**
    
    Чтобы добавить диапазон IP-адресов к конечной точке, вы будете использовать [Add-AzTrafficManagerIpAddressRange,](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange?view=azps-2.5.0&viewFallbackFrom=azps-2.4.0) чтобы добавить диапазон.

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    После добавления диапазонов используйте [Set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) для обновления конечных точек.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
Удаление диапазона IP-адресов может быть завершено с помощью [Remove-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange?view=azps-2.5.0).

1.  **Retrieve диспетчер трафика конечная точка:**

    Чтобы включить переопределение подсети, извлеките конечную точку, к которая вы хотите добавить переопределение, и храните ее в переменной с помощью [Get-AzTrafficManagerEndpoint.](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0)

    Замените имя, имя профиля и Name ResourceGroupName значениями изменяемых конечных точек.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Удалите диапазон IP-адресов из конечных точек:**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     После удаления диапазонов используйте [Set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) для обновления конечных точек.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>Дальнейшие действия
См. дополнительные сведения в статье [Методы маршрутизации трафика диспетчером трафика](traffic-manager-routing-methods.md).

Узнайте о [методе реуктовирования трафика Subnet](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)
