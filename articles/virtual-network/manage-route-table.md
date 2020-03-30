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
ms.date: 03/19/2020
ms.author: kumud
ms.openlocfilehash: 79310ddf121d6ada10755b198b515fdc9c1114d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247067"
---
# <a name="create-change-or-delete-a-route-table"></a>Создание, изменение и удаление таблицы маршрутов

Azure автоматически направляет трафик между подсетями Azure, виртуальными и локальными сетями. Если нужно изменить какой-либо из стандартных маршрутов Azure, это можно сделать с помощью создания таблицы маршрутов. Если вы новичок в области реуктора в виртуальных сетях, вы можете узнать больше об этом в [виртуальной сети трафика разгрома](virtual-networks-udr-overview.md) или, заполнив [учебник](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Перед началом

Если у вас его нет, навяжните учетную запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) бесплатно. Затем выполните одну из этих задач перед запуском шагов в любом разделе этой статьи:

- **Пользователи портала**: Войти на [портал Azure](https://portal.azure.com) с вашей учетной записью Azure.

- **Пользователи PowerShell:** либо запускать команды в [облачной оболочке Azure,](https://shell.azure.com/powershell)либо запускать PowerShell с компьютера. Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. Во вкладке браузера Azure Cloud Shell найдите список выпадающих **средств для поиска среды Select,** а затем выберите **PowerShell,** если он еще не выбран.

    Если вы работаете локально PowerShell, используйте версию модуля Azure PowerShell 1.0.0 или позже. Выполните командлет `Get-Module -ListAvailable Az.Network`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Также `Connect-AzAccount` запускайте для создания соединения с Azure.

- **Пользователи интерфейса Командной строки Azure (CLI)**: либо запускают команды в [оболочке Azure Cloud Shell,](https://shell.azure.com/bash)либо запускают CLI с компьютера. Используйте версию Azure CLI 2.0.31 или позже, если вы используете Azure CLI локально. Выполните командлет `az --version`, чтобы узнать установленную версию. Если вам нужно установить или обновить, [см.](/cli/azure/install-azure-cli) Также `az login` запускайте для создания соединения с Azure.

Учетная запись, в которую вы входите или подключаетесь к Azure, должна быть назначена [роли вкладчика Сети](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) или [пользовательской роли,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) которая присвоила соответствующие действия, перечисленные в [Разрешении.](#permissions)

## <a name="create-a-route-table"></a>Создание таблицы маршрутов

Количество таблиц маршрутов, которые можно создать в azure, ограничено. Для получения подробной информации [см.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

1. В меню [портала Azure](https://portal.azure.com) или на **главной** странице выберите **«Создайте ресурс».**

1. В поле поиска введите *Таблица маршрутов*. Когда элемент **Таблица маршрутов** появится в результатах поиска, выберите его.

1. На странице **Таблица маршрутов** щелкните **Создать**.

1. В диалоговом поле **таблицы маршрутов:**

    1. Введите **название** для таблицы маршрутов.
    1. Выберите **подписку**.
    1. Выберите существующую **группу ресурсов** или **создайте новую** для создания новой группы ресурсов.
    1. Выберите **расположение**.
    1. Если вы планируете связать таблицу маршрутов с подсетью в виртуальной сети, подключенной к вашей предприимчивой сети через VPN шлюз, и вы не хотите распространять свои маршруты на основе сетевых интерфейсов в подсети, установите **распространение маршрута виртуальной сети шлюзов** для **инвалидов.**

1. Выберите **Создать** для создания новой таблицы маршрутов.

### <a name="create-route-table---commands"></a>Создание таблицы маршрутов - команды

| Инструмент | Команда |
| ---- | ------- |
| Azure CLI | [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>Просмотр таблиц маршрутов

Перейдите на [портал Azure](https://portal.azure.com) для управления виртуальной сетью. Поиск и выбор **таблиц маршрута.** В списке будут содержаться таблицы маршрутов, имеющиеся в вашей подписке.

### <a name="view-route-table---commands"></a>Просмотр таблицы маршрутов - команды

| Инструмент | Команда |
| ---- | ------- |
| Azure CLI | [az список сетевых маршрутов-таблицы](/cli/azure/network/route-table#az-network-route-table-list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>Просмотр сведений о таблице маршрутов

1. Перейдите на [портал Azure](https://portal.azure.com) для управления виртуальной сетью. Поиск и выбор **таблиц маршрута.**

1. В таблице маршрутов выберите таблицу маршрутов, для просмотра чего можно ознакомиться.

1. На странице **таблицы маршрутов**под настройками просматривайте **маршруты** в таблице маршрутов или **подсети,** к которому связана таблица маршрутов.

Дополнительные сведения об общих параметрах Azure см. ниже:

- [Журнал активности](../azure-monitor/platform/platform-logs-overview.md)
- [Контроль доступа (IAM)](../role-based-access-control/overview.md)
- [Теги](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Замки](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Сценарий автоматизации](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Просмотр сведений о таблице маршрутов - команды

| Инструмент | Команда |
| ---- | ------- |
| Azure CLI | [az сетевой маршрут-таблица шоу](/cli/azure/network/route-table#az-network-route-table-show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>Изменение таблицы маршрутов

1. Перейдите на [портал Azure](https://portal.azure.com) для управления виртуальной сетью. Поиск и выбор **таблиц маршрута.**

1. В таблице маршрутов выберите таблицу маршрутов, которую вы хотите изменить.

Наиболее распространенными изменениями являются [добавление](#create-a-route) маршрутов, [удаление](#delete-a-route) маршрутов, [ассоциированное](#associate-a-route-table-to-a-subnet) направление таблиц с подсетями или [отмежение](#dissociate-a-route-table-from-a-subnet) таблиц маршрутов из подсетей.

### <a name="change-a-route-table---commands"></a>Изменение таблицы маршрутов - команды

| Инструмент | Команда |
| ---- | ------- |
| Azure CLI | [обновление сетевого маршрута-таблицы](/cli/azure/network/route-table#az-network-route-table-update) |
| PowerShell | [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>Связывание таблицы маршрутов с подсетью

Можно дополнительно связать таблицу маршрутов с подсетью. Таблица маршрутов может быть связана с нулем или более подсетями. Поскольку таблицы маршрутов не связаны с виртуальными сетями, необходимо связать таблицу маршрутов с каждой подсетью, к которому вы хотите присоединиться к таблице маршрутов. Azure маршрутирует весь трафик, оставляя подсеть на основе маршрутов, созданных в таблицах маршрутов, [маршрутов по умолчанию](virtual-networks-udr-overview.md#default)и маршрутов, распространяемых из предварительной сети, если виртуальная сеть подключена к виртуальному сетевому шлюзу Azure (ExpressRoute или VPN). Вы можете связать таблицы маршрутов только с теми подсетями в виртуальной сети, которые находятся в том же расположении и той же подписке Azure, что и таблица маршрутов.

1. Перейдите на [портал Azure](https://portal.azure.com) для управления виртуальной сетью. Поиск и выбор **виртуальных сетей.**

1. В виртуальном сетевом списке выберите виртуальную сеть, содержащую подсеть, с которой вы хотите связать таблицу маршрутов.

1. В виртуальном сетевом меню-баре выберите **подсети.**

1. Выберите подсеть, с которой нужно связать таблицу маршрутов.

1. В **таблице Route**выберите таблицу маршрутов, которая необходимо связать с подсетью.

1. Нажмите кнопку **Сохранить**.

Если ваша виртуальная сеть подключена к VPN-шлюзу Azure, не связывайте таблицу маршрутов с [подсетью шлюза,](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) которая включает маршрут с пунктом назначения *0.0.0.0/0.* Это может привести к неправильной работе шлюза. Для получения дополнительной информации об использовании *0.0.0.0/0* в маршруте, [см.](virtual-networks-udr-overview.md#default-route)

### <a name="associate-a-route-table---commands"></a>Ассоциировать таблицу маршрутов - команды

| Инструмент | Команда |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>Отмена связи таблицы маршрутов с подсетью

При отмене связи таблицы маршрутов с подсетью Azure направляет трафик на основе своих [стандартных маршрутов](virtual-networks-udr-overview.md#default).

1. Перейдите на [портал Azure](https://portal.azure.com) для управления виртуальной сетью. Поиск и выбор **виртуальных сетей.**

1. В виртуальном сетевом списке выберите виртуальную сеть, содержащую подсеть, от которой вы хотите отмежевать таблицу маршрутов.

1. В виртуальном сетевом меню-баре выберите **подсети.**

1. Выберите подсеть, с которой нужно отменить связь.

1. В **таблице маршрута**выберите **Нет**.

1. Нажмите кнопку **Сохранить**.

### <a name="dissociate-a-route-table---commands"></a>Разъединить таблицу маршрутов - команды

| Инструмент | Команда |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>Удаление таблицы маршрутов

Нельзя удалять таблицу маршрутов, связанную с любыми подсетями. [Отмените связь](#dissociate-a-route-table-from-a-subnet) таблицы маршрутов со всеми подсетями, а затем попытайтесь удалить ее.

1. Перейдите на [портал Azure,](https://portal.azure.com) чтобы управлять таблицами маршрутов. Поиск и выбор **таблиц маршрута.**

1. В таблице маршрутов выберите таблицу маршрутов, которая необходимо удалить.

1. Выберите **Удалить,** а затем выбрать **Да** в поле диалога подтверждения.

### <a name="delete-a-route-table---commands"></a>Удаление таблицы маршрутов - команды

| Инструмент | Команда |
| ---- | ------- |
| Azure CLI | [аз сетевой маршрут-таблица удалить](/cli/azure/network/route-table#az-network-route-table-delete) |
| PowerShell | [Удалить-AzRouteTable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>Создание маршрута

Существует ограничение на количество маршрутов в таблице маршрутов, которые можно создать в местоположении и подписке Azure. Для получения подробной информации [см.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

1. Перейдите на [портал Azure,](https://portal.azure.com) чтобы управлять таблицами маршрутов. Поиск и выбор **таблиц маршрута.**

1. В таблице маршрутов выберите таблицу маршрутов, к которому вы хотите добавить маршрут.

1. Из бара меню таблицы маршрута выберите **Маршруты** > **Добавить**.

1. Введите уникальное **название маршрута** для маршрута в таблице маршрутов.

1. Введите **префикс Адреса,** в classless Inter-Domain Маршрутирования (CIDR) обозначение, что вы хотите, чтобы маршрут трафика. Префикс не может быть дублирован в более чем одном маршруте в таблице маршрутов, хотя приставка может быть внутри другой префикс. Например, если вы определили *10.0.0.0/16* в качестве префикса в одном маршруте, вы все равно можете определить другой маршрут с приставкой *адреса 10.0.0.0/22.* Azure выбирает маршрут трафика на основе совпадения с самым длинным префиксом. Чтобы узнать больше, [посмотрите, как Azure выбирает маршрут.](virtual-networks-udr-overview.md#how-azure-selects-a-route)

1. Выберите **следующий тип хмеля.** Чтобы узнать больше о следующих типах перехода, [см.](virtual-networks-udr-overview.md)

1. Если вы выбрали **следующий тип хмеля** **виртуального устройства,** введите IP-адрес для **следующего хмеля**адрес.

1. Нажмите кнопку **ОК**.

### <a name="create-a-route---commands"></a>Создание маршрута - команды

| Инструмент | Команда |
| ---- | ------- |
| Azure CLI | [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>Просмотр маршрутов

Таблица маршрутов содержит несколько маршрутов или не содержит их вообще. Чтобы узнать больше об информации, указанной при просмотре маршрутов, [см.](virtual-networks-udr-overview.md)

1. Перейдите на [портал Azure,](https://portal.azure.com) чтобы управлять таблицами маршрутов. Поиск и выбор **таблиц маршрута.**

1. В таблице маршрутов выберите таблицу маршрутов, для просмотра маршрутов — это выхотите.

1. В таблице меню таблицы маршрутов выберите **маршруты,** чтобы увидеть список маршрутов.

### <a name="view-routes---commands"></a>Просмотр маршрутов - команды

| Инструмент | Команда |
| ---- | ------- |
| Azure CLI | [az список маршрутов и таблиц аз-сети](/cli/azure/network/route-table/route#az-network-route-table-route-list) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>Просмотр сведений о маршруте

1. Перейдите на [портал Azure,](https://portal.azure.com) чтобы управлять таблицами маршрутов. Поиск и выбор **таблиц маршрута.**

1. В таблице маршрутов выберите таблицу маршрутов, содержащую маршрут, для просмотра маршрута.

1. В таблице меню таблицы маршрутов выберите **маршруты,** чтобы увидеть список маршрутов.

1. Выберите маршрут, сведения о котором нужно просмотреть.

### <a name="view-details-of-a-route---commands"></a>Просмотр деталей маршрута - команды

| Инструмент | Команда |
| ---- | ------- |
| Azure CLI | [az сетевой маршрут-таблица маршрута](/cli/azure/network/route-table/route#az-network-route-table-route-show) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>Изменение маршрута

1. Перейдите на [портал Azure,](https://portal.azure.com) чтобы управлять таблицами маршрутов. Поиск и выбор **таблиц маршрута.**

1. В таблице маршрутов выберите таблицу маршрутов, содержащую маршрут, который вы хотите изменить.

1. В таблице меню таблицы маршрутов выберите **маршруты,** чтобы увидеть список маршрутов.

1. Выберите маршрут, который вы хотите изменить.

1. Измените имеющиеся параметры на новые, а затем нажмите кнопку **Сохранить**.

### <a name="change-a-route---commands"></a>Изменение маршрута - команды

| Инструмент | Команда |
| ---- | ------- |
| Azure CLI | [обновление маршрута маршрутов и таблиц аз сети](/cli/azure/network/route-table/route#az-network-route-table-route-update) |
| PowerShell | [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>Удаление маршрута

1. Перейдите на [портал Azure,](https://portal.azure.com) чтобы управлять таблицами маршрутов. Поиск и выбор **таблиц маршрута.**

1. В таблице маршрутов выберите таблицу маршрутов, содержащую маршрут, который вы хотите удалить.

1. В таблице меню таблицы маршрутов выберите **маршруты,** чтобы увидеть список маршрутов.

1. Выберите маршрут, который вы хотите удалить.

1. Выберите **Удалить,** затем выберите **Да** в поле диалога подтверждения.

### <a name="delete-a-route---commands"></a>Удаление маршрута - команды

| Инструмент | Команда |
| ---- | ------- |
| Azure CLI | [az сетевой маршрут-таблица маршрута удалить](/cli/azure/network/route-table/route#az-network-route-table-route-delete) |
| PowerShell | [Удалить-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>Просмотр действующих маршрутов

Эффективные маршруты для каждого сетевого интерфейса, подключенного к VM, представляют собой сочетание созданных вами маршрутных таблиц, маршрутов по умолчанию Azure и любых маршрутов, распространяемых из закрытых сетей через протокол пограничного шлюза (BGP) через виртуальную сеть Azure Шлюза. Понимание принципа работы действующих маршрутов для сетевого интерфейса необходимо для устранения неполадок, связанных с маршрутизацией. Вы можете просмотреть эффективные маршруты для любого сетевого интерфейса, который подключен к бегущему VM.

1. Перейдите на [портал Azure](https://portal.azure.com) для управления ввозами. Поиск и выбор **виртуальных машин.**

1. В списке виртуальных машин выберите VM, для чего вы хотите просматривать эффективные маршруты.

1. В баре меню VM выберите **Сеть**.

1. Выберите имя сетевого интерфейса.

1. В панели меню сетевого интерфейса выберите **эффективные маршруты.**

1. Просмотрите список эффективных маршрутов, чтобы узнать, существует ли правильный маршрут для того, куда вы хотите направить трафик. Узнайте больше о следующих типах перехода, которые вы видите в этом списке в [виртуальной сети трафика разгрома](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Просмотр эффективных маршрутов - команды

| Инструмент | Команда |
| ---- | ------- |
| Azure CLI | [az сети nic шоу-эффективный маршрут-таблица](/cli/azure/network/nic#az-network-nic-show-effective-route-table) |
| PowerShell | [Get-AzEffectiveRouteTable.](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>Проверка маршрутизации между двумя конечными точками

Вы можете определить тип следующего прыжка между виртуальной машиной и IP-адресом другого ресурса Azure, локальным ресурсом или ресурсом в Интернете. Определение маршрутизации Azure эффективно при устранении неполадок, связанных с маршрутизацией. Для выполнения этой задачи необходимо иметь существующий сетевой наблюдатель. Если у вас нет существующего сетевого наблюдателя, создайте его, выполнив шаги в [экземпляре «Создай сетевой наблюдатель».](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

1. Перейдите на [портал Azure,](https://portal.azure.com) чтобы управлять сетевыми наблюдателами. Поиск и выберите **Network Watcher**.

1. В сетевом баре меню просмотра, выберите **Следующий хоп**.

1. В **сети Наблюдатель (ru) Следующая страница хопа:**

    1. Выберите **подписку** и **группу ресурсов** источника VM, из чего вы хотите проверить разгром.

    1. Выберите **виртуальную машину** и **интерфейс сети,** который прилагается к VM.
    
    1. Введите **IP-адрес Source,** назначенный сетевому интерфейсу, с которого необходимо проверить разгром.

    1. Введите **IP-адрес destination,** на который вы хотите проверить направление.

1. Выберите **Следующий прыжок**.

После короткого ожидания Azure сообщает вам следующий тип прыжка и идентификатор маршрута, который направлял трафик. Узнайте больше о следующих типах перехода, которые вы видите, возвращенных в [виртуальной сети трафика разгрома](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Проверка разгрома между двумя конечными точками - командами

| Инструмент | Команда |
| ---- | ------- |
| Azure CLI | [az сети наблюдатель шоу-следующий-хоп](/cli/azure/network/watcher#az-network-watcher-show-next-hop) |
| PowerShell | [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>Разрешения

Для выполнения задач в таблицах маршрутов и маршрутах ваша учетная запись должна быть назначена [роли вкладчика Сети](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) или [пользовательской роли,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) которая присваивает соответствующие действия, перечисленные в следующей таблице:

| Действие                                                          |   name                                                  |
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

- Создание таблицы маршрутов с помощью шаблонов [PowerShell](powershell-samples.md) или [Azure CLI](cli-samples.md) или [шаблонов](template-samples.md) azure Resource Manager
- Создайте и примените [политику Azure](policy-samples.md) для виртуальных сетей.
