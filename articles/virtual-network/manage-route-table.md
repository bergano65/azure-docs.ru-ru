---
title: Создание, изменение или удаление таблицы маршрутов Azure
titlesuffix: Azure Virtual Network
description: Сведения о создании, изменении и удалении таблицы маршрутов.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: a39d9f9c5a138ece5d40cc5afe1d1dcdd8e7a41a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65849807"
---
# <a name="create-change-or-delete-a-route-table"></a>Создание, изменение и удаление таблицы маршрутов

Azure автоматически направляет трафик между подсетями Azure, виртуальными и локальными сетями. Если нужно изменить какой-либо из стандартных маршрутов Azure, это можно сделать с помощью создания таблицы маршрутов. Если у вас нет опыта маршрутизации в виртуальных сетях, вы можете получить дополнительные сведения об этом в [обзоре маршрутизации](virtual-networks-udr-overview.md) или изучить [руководство](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Перед началом работы

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Перед выполнением действий, описанных в любом разделе этой статьи, выполните следующие задачи.

* Если у вас нет учетной записи Azure, зарегистрируйтесь для получения [бесплатной пробной учетной записи](https://azure.microsoft.com/free).<br>
* Если с помощью портала, откройте https://portal.azure.com и войдите с помощью учетной записи Azure.<br>
* При использовании команд PowerShell для работы с этой статьей выполняйте их в [Azure Cloud Shell](https://shell.azure.com/powershell) или в PowerShell на своем компьютере. Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные распространенные инструменты Azure и настроена для использования с вашей учетной записью. Для работы с этим руководством требуется модуль Azure PowerShell версии 1.0.0 и выше. Выполните командлет `Get-Module -ListAvailable Az`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.<br>
* При использовании команд интерфейса командной строки Azure (CLI) для работы с этой статьей выполняйте их в [Azure Cloud Shell](https://shell.azure.com/bash) или в интерфейсе командной строки на своем компьютере. Для этого руководства требуется Azure CLI 2.0.31 или более поздней версии. Выполните командлет `az --version`, чтобы узнать установленную версию. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). Если Azure CLI запущен локально, необходимо также выполнить командлет `az login`, чтобы создать подключение к Azure.

Учетная запись входа в, или подключиться к Azure, должны быть назначены [участник сетей](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) роли или [пользовательской роли](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , назначенный соответствующие действия, перечисленные в [разрешения ](#permissions).

## <a name="create-a-route-table"></a>Создание таблицы маршрутов

Количество таблиц маршрутов, которые можно создать в одном расположении и одной подписке Azure, ограничено. Дополнительные сведения см. в разделе [Ограничения сети](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Выберите **+ Create a resource** (+ Создать ресурс) в левом верхнем углу портала.
1. Выберите **Сетевые подключения**, а затем — **Таблица маршрутов**.
1. Введите **имя** таблицы маршрутов, выберите свою **подписку**, создайте **группу ресурсов** или выберите имеющуюся, щелкните **расположение**, а затем выберите **Создать**. Если планируется связать таблицу маршрутов с подсетью в виртуальной сети, подключенной к локальной сети через VPN-шлюза и отключить **распространение маршрутов шлюза виртуальной сети**, ваши локальные маршруты не распространяются на сетевые интерфейсы в подсети.

### <a name="create-route-table---commands"></a>Создание таблицы маршрутов - команды

* Azure CLI: [az network route-table create](/cli/azure/network/route-table/route)<br>
* PowerShell: [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)

## <a name="view-route-tables"></a>Просмотр таблиц маршрутов

В поле поиска в верхней части портала введите *таблицы маршрутов*. Когда пункт **Таблицы маршрутов** появится в результатах поиска, выберите его. В списке будут содержаться таблицы маршрутов, имеющиеся в вашей подписке.

### <a name="view-route-table---commands"></a>Просмотр таблицы маршрутов - команды

* Azure CLI: [az network route-table list](/cli/azure/network/route-table/route)<br>
* PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="view-details-of-a-route-table"></a>Просмотр сведений о таблице маршрутов

1. В поле поиска в верхней части портала введите *таблицы маршрутов*. Когда пункт **Таблицы маршрутов** появится в результатах поиска, выберите его.
1. Выберите в списке таблицу маршрутов, о которой нужно просмотреть сведения. В разделе **Параметры** можно просмотреть **маршруты** в таблице маршрутов и **подсети**, с которыми связана таблица маршрутов.
1. Дополнительные сведения об общих параметрах Azure см. ниже:

    * [Журнал действий](../azure-monitor/platform/activity-logs-overview.md)<br>
    * [Управление доступом (IAM)](../role-based-access-control/overview.md)<br>
    * [Теги](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Блокировки](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Сценарий автоматизации](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)

### <a name="view-details-of-route-table---commands"></a>Просмотреть сведения о таблице маршрутов — команды

* Azure CLI: [az network route-table show](/cli/azure/network/route-table/route)<br>
* PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="change-a-route-table"></a>Изменение таблицы маршрутов

1. В поле поиска в верхней части портала введите *таблицы маршрутов*. Когда пункт **Таблицы маршрутов** появится в результатах поиска, выберите его.
1. Выберите таблицу маршрутов, которую нужно изменить. Наиболее распространенными видами изменений являются [добавление](#create-a-route) и [удаление](#delete-a-route) маршрутов, [связывание](#associate-a-route-table-to-a-subnet) или [отмена связи](#dissociate-a-route-table-from-a-subnet) таблиц маршрутов с подсетями.

### <a name="change-a-route-table---commands"></a>Изменение таблицы маршрутов - команды

* Azure CLI: [az network route-table update](/cli/azure/network/route-table/route)<br>
* PowerShell: [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Связывание таблицы маршрутов с подсетью

Подсеть может иметь не более одной таблицы маршрутов, связанной с ней. Таблица маршрутов может быть не связана с подсетями или связана с несколькими. Так как таблицы маршрутов не связаны с виртуальными сетями, вам необходимо связать таблицу маршрутов с каждой соответствующей подсетью. Весь трафик из подсети маршрутизируется на основе маршрутов, созданных в таблицах маршрутов, [маршруты по умолчанию](virtual-networks-udr-overview.md#default), и маршрутах, распространяемых из локальной сети, если виртуальная сеть подключена к (шлюз виртуальной сети Azure ExpressRoute или VPN). Вы можете связать таблицы маршрутов только с теми подсетями в виртуальной сети, которые находятся в том же расположении и той же подписке Azure, что и таблица маршрутов.

1. В поле поиска в верхней части портала введите *виртуальные сети*. Когда в результатах поиска появится пункт **Виртуальные сети**, выберите его.
1. В списке виртуальных сетей выберите сеть, содержащую подсеть, с которой нужно связать таблицу маршрутов.
1. В разделе **параметров** выберите **Подсети**.
1. Выберите подсеть, с которой нужно связать таблицу маршрутов.
1. Выберите **таблицу маршрутов**, с которой нужно связать подсеть, а затем нажмите кнопку **Сохранить**.

Если виртуальная сеть подключена к шлюзу Azure VPN, не связывайте таблицу маршрутов с [подсетью шлюза](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub), включающей маршрут с назначением 0.0.0.0/0. Это может привести к неправильной работе шлюза. Дополнительные сведения об использовании префикса 0.0.0.0/0 в маршруте см. в разделе [Префикс адреса 0.0.0.0/0](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Связывание таблицы маршрутов - команды

* Azure CLI: [az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell: [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Отмена связи таблицы маршрутов с подсетью

При отмене связи таблицы маршрутов с подсетью Azure направляет трафик на основе своих [стандартных маршрутов](virtual-networks-udr-overview.md#default).

1. В поле поиска в верхней части портала введите *виртуальные сети*. Когда в результатах поиска появится пункт **Виртуальные сети**, выберите его.
1. Выберите виртуальную сеть, содержащую подсеть, с которой нужно отменить связь.
1. В разделе **параметров** выберите **Подсети**.
1. Выберите подсеть, с которой нужно отменить связь.
1. Выберите **таблицу маршрутов**, затем — **Нет** и нажмите кнопку **Сохранить**.

### <a name="dissociate-a-route-table---commands"></a>Отменить связь - команды

* Azure CLI: [az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell: [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-route-table"></a>Удаление таблицы маршрутов

Таблицу маршрутов нельзя удалить, если она связана с какими-либо подсетями. [Отмените связь](#dissociate-a-route-table-from-a-subnet) таблицы маршрутов со всеми подсетями, а затем попытайтесь удалить ее.

1. В поле поиска в верхней части портала введите *таблицы маршрутов*. Когда пункт **Таблицы маршрутов** появится в результатах поиска, выберите его.
1. Выберите **...** в правой области таблицы маршрутов, которую нужно удалить.
1. Выберите **Удалить**, а затем — **Да**.

### <a name="delete-a-route-table---commands"></a>Удаление таблицы маршрутов - команды

* Azure CLI: [az network route-table delete](/cli/azure/network/route-table/route)<br>
* PowerShell: [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable)

## <a name="create-a-route"></a>Создание маршрута

Количество маршрутов, которые можно создать для одной таблицы маршрутов в одном расположении и одной подписке Azure, ограничено. Дополнительные сведения см. в разделе [Ограничения сети](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. В поле поиска в верхней части портала введите *таблицы маршрутов*. Когда пункт **Таблицы маршрутов** появится в результатах поиска, выберите его.
1. Выберите в списке таблицу маршрутов, в которую нужно добавить маршрут.
1. В разделе **параметров** выберите **Маршруты**.
1. Выберите **+ добавить**.
1. Введите уникальное **имя** маршрута в таблице маршрутов.
1. Введите **префикс адреса** в нотации CIDR, куда нужно направить трафик. Префикс не может повторяться в нескольких маршрутах в таблице маршрутов, хотя может содержаться в другом префиксе. Например, если вы определили 10.0.0.0/16 как префикс в одном маршруте, вы по-прежнему можете определить другой маршрут с префиксом адреса 10.0.0.0/24. Azure выбирает маршрут трафика на основе совпадения с самым длинным префиксом. Дополнительные сведения о том, как Azure выбирает маршруты, см. в разделе [Как Azure выбирает маршрут](virtual-networks-udr-overview.md#how-azure-selects-a-route).
1. Выберите **Тип следующего прыжка**. Подробное описание всех типов следующих прыжков см. в разделе [Как Azure выбирает маршрут](virtual-networks-udr-overview.md).
1. Введите IP-адрес для **адреса следующего прыжка**. Ввести адрес можно, только если для параметра **Тип следующего прыжка** выбран *Виртуальный модуль*.
1. Нажмите кнопку **ОК**.

### <a name="create-a-route---commands"></a>Создайте маршрут - команды

* Azure CLI: [az network route-table route create](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)

## <a name="view-routes"></a>Просмотр маршрутов

Таблица маршрутов содержит несколько маршрутов или не содержит их вообще. Узнать больше о сведениях при просмотре маршрутов можно с помощью статьи [Маршрутизация трафика в виртуальной сети](virtual-networks-udr-overview.md).

1. В поле поиска в верхней части портала введите *таблицы маршрутов*. Когда пункт **Таблицы маршрутов** появится в результатах поиска, выберите его.
1. Выберите в списке таблицу маршрутов, для которой нужно просмотреть маршруты.
1. В разделе **параметров** выберите **Маршруты**.

### <a name="view-routes---commands"></a>Просмотр маршрутов - команды

* Azure CLI: [az network route-table route list](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="view-details-of-a-route"></a>Просмотр сведений о маршруте

1. В поле поиска в верхней части портала введите *таблицы маршрутов*. Когда пункт **Таблицы маршрутов** появится в результатах поиска, выберите его.
1. Выберите таблицу маршрутов, в которой нужно просмотреть сведения о маршруте.
1. Выберите **Маршруты**.
1. Выберите маршрут, сведения о котором нужно просмотреть.

### <a name="view-details-of-a-route---commands"></a>Просмотр сведений о маршруте - команды

* Azure CLI: [az network route-table route show](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="change-a-route"></a>Изменение маршрута

1. В поле поиска в верхней части портала введите *таблицы маршрутов*. Когда пункт **Таблицы маршрутов** появится в результатах поиска, выберите его.
1. Выберите таблицу маршрутов, для которой нужно изменить маршрут.
1. Выберите **Маршруты**.
1. Выберите маршрут, который нужно изменить.
1. Измените имеющиеся параметры на новые, а затем нажмите кнопку **Сохранить**.

### <a name="change-a-route---commands"></a>Изменение маршрута - команды

* Azure CLI: [az network route-table route update](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig)

## <a name="delete-a-route"></a>Удаление маршрута

1. В поле поиска в верхней части портала введите *таблицы маршрутов*. Когда пункт **Таблицы маршрутов** появится в результатах поиска, выберите его.
1. Выберите таблицу маршрутов, для которой нужно удалить маршрут.
1. Выберите **Маршруты**.
1. В списке маршрутов выберите **...** в правой области маршрута, который нужно удалить.
1. Выберите **Удалить**, а затем — **Да**.

### <a name="delete-a-route---commands"></a>Удаление маршрута - команды

* Azure CLI: [az network route-table route delete](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig)

## <a name="view-effective-routes"></a>Просмотр действующих маршрутов

Действующие маршруты для каждого сетевого интерфейса, подключенного к виртуальной машине, представляют собой комбинацию созданных таблиц маршрутов, стандартных маршрутов Azure и всех маршрутов, распространенных из локальных сетей с помощью BGP через шлюз виртуальной сети Azure. Понимание принципа работы действующих маршрутов для сетевого интерфейса необходимо для устранения неполадок, связанных с маршрутизацией. Вы можете просмотреть действующие маршруты для любого сетевого интерфейса, подключенного к работающей виртуальной машине.

1. В поле поиска в верхней области портала введите имя виртуальной машины, для которой нужно просмотреть действующие маршруты. Если вы не знаете ее имя, введите в поле поиска *виртуальные машины*. Когда пункт **Виртуальные машины** появится в результатах поиска, щелкните его и выберите из списка виртуальную машину.
1. В разделе **параметров** выберите **Сеть**.
1. Выберите имя сетевого интерфейса.
1. В разделе **Поддержка и устранение неполадок** выберите **Фактические маршруты**.
1. Просмотрите список действующих маршрутов, чтобы определить, есть ли соответствующий маршрут, по которому нужно направить трафик. Дополнительные сведения о типах следующих прыжков в этом списке см. в статье [Маршрутизация трафика в виртуальной сети](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Просмотр эффективных маршрутов - команды

* Azure CLI: [az network nic show-effective-route-table](/cli/azure/network/nic?view=azure-cli-latest)<br>
* PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="validate-routing-between-two-endpoints"></a>Проверка маршрутизации между двумя конечными точками

Вы можете определить тип следующего прыжка между виртуальной машиной и IP-адресом другого ресурса Azure, локальным ресурсом или ресурсом в Интернете. Определение маршрутизации Azure эффективно при устранении неполадок, связанных с маршрутизацией. Для проверки маршрутизации у вас должен быть установлен Наблюдатель за сетями. Если у вас его нет, создайте его, выполнив задачи, описанные в статье [Создание экземпляра Наблюдателя за сетями Azure](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. В поле поиска в верхней области портала введите *наблюдатель за сетями*. Когда в результатах поиска появится **Наблюдатель за сетями**, выберите его.
1. В разделе **Средства диагностики сети** выберите **Следующий прыжок**.
1. Выберите свою **подписку** и **группу ресурсов** исходной виртуальной машины, исходящий трафик которой нужно проверить.
1. Выберите **виртуальную машину**, **сетевой интерфейс**, подключенный к виртуальной машине, и **исходный IP-адрес**, назначенный сетевому интерфейсу, исходящий трафик которого нужно проверить.
1. Введите **конечный IP-адрес**, куда направляется трафик, который нужно проверить.
1. Выберите **Следующий прыжок**.
1. После короткого периода ожидания вернутся сведения, из которых вы узнаете тип следующего прыжка и идентификатор маршрута, который направляет трафик. Дополнительную информацию о типах следующих прыжков, возвращенных в сведениях, см. в статье [Маршрутизация трафика в виртуальной сети](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Проверка маршрутизации между двумя конечными точками - команды

* Azure CLI: [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest)<br>
* PowerShell: [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop)

## <a name="permissions"></a>Разрешения

Для выполнения задач в таблицах маршрутов и маршрутах учетной записи должна быть назначена одна из ролей: [Участник сетей](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) или [пользовательская](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) роль, которой назначены соответствующие действия, перечисленные в следующей таблице.

| Действие                                                          |   ИМЯ                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Чтение таблицы маршрутов.                                    |
| Microsoft.Network/routeTables/write                             |   Создание или обновление таблицы маршрутов.                        |
| Microsoft.Network/routeTables/delete                            |   Удаление таблицы маршрутов                                  |
| Microsoft.Network/routeTables/join/action                       |   Связывание таблицы маршрутов с подсетью                   |
| Microsoft.Network/routeTables/routes/read                       |   Чтение маршрута.                                          |
| Microsoft.Network/routeTables/routes/write                      |   Создание или обновление маршрута.                              |
| Microsoft.Network/routeTables/routes/delete                     |   Удаление маршрута                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Получение действующей таблицы маршрутов для сетевого интерфейса. |
| Microsoft.Network/networkWatchers/nextHop/action                |   Получить следующий прыжок из виртуальной машины                           |

## <a name="next-steps"></a>Дальнейшие действия

* Создайте таблицу маршрутов с помощью примеров сценариев [PowerShell](powershell-samples.md) или [Azure CLI](cli-samples.md) либо на основе [шаблонов Azure Resource Manager](template-samples.md).<br>
* Создайте и примените [политику Azure](policy-samples.md) для виртуальных сетей.
