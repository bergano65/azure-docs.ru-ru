---
title: 'Azure ExpressRoute: миграция классических виртуальных сетей в диспетчер ресурсов'
description: В этой статье описывается, как перенести виртуальные сети, связанные с ExpressRoute, в Resource Manager после перемещения канала.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: cherylmc
ms.openlocfilehash: b24400b2a6a2050fa74b23f936253046f96a9028
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436892"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Перенос виртуальных сетей, связанных с ExpressRoute, из классической модели в модель развертывания с помощью Resource Manager

В этой статье описывается, как после перемещения канала ExpressRoute перенести связанные с ним виртуальные сети из классической модели развертывания в модель развертывания с помощью Azure Resource Manager. 

## <a name="before-you-begin"></a>Перед началом работы

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Убедитесь, что у вас установлены последние версии модулей Azure PowerShell. Подробнее: [Установка и настройка Azure PowerShell](/powershell/azure/overview). Сведения об установке модуля управления службами PowerShell (который необходим для классической модели развертывания) см. в разделе [Установка модуля управления Azure PowerShell служб](/powershell/azure/servicemanagement/install-azure-ps).
* Не забудьте изучить [предварительные требования](expressroute-prerequisites.md), [требования к маршрутизации](expressroute-routing.md) и [рабочие процессы](expressroute-workflows.md), прежде чем приступать к настройке.
* Просмотрите сведения в статье [Перемещение каналов ExpressRoute из классической модели развертывания в модель развертывания с помощью Resource Manager](expressroute-move.md). Убедитесь, что вам полностью понятны пределы и ограничения.
* Убедитесь, что канал полноценно работает в классической модели развертывания.
* Убедитесь в наличии группы ресурсов, созданной в модели развертывания Resource Manager.
* Ознакомьтесь со следующими статьями о переносе ресурсов:

    * [Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Техническое руководство по поддерживаемому платформой переносу из классической модели в модель Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [Platform-supported migration of IaaS resources from classic to Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md) (Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager)
    * [Common errors during Classic to Azure Resource Manager migration](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Распространенные ошибки при переносе из классической модели на модель Azure Resource Manager)

## <a name="supported-and-unsupported-scenarios"></a>Поддерживаемые и неподдерживаемые сценарии

* Канал ExpressRoute можно перенести из классической среды в среду Resource Manager без простоев. Вы можете переместить любой канал ExpressRoute из классической среды в среду Resource Manager без простоев. Следуйте инструкциям в статье [Перемещение каналов ExpressRoute из классической модели развертывания в модель Resource Manager с помощью PowerShell](expressroute-howto-move-arm.md). Перемещение ресурсов, подключенных к виртуальной сети, является обязательным условием.
* Виртуальные сети, шлюзы и связанные развертывания в виртуальной сети, подключенные к каналу ExpressRoute в одной подписке, можно перенести в среду Resource Manager без простоев. Выполните приведенные ниже действия, чтобы перенести ресурсы, такие как виртуальные сети, шлюзы и виртуальные машины, развернутые в виртуальной сети. Прежде чем приступать к переносу виртуальных сетей, необходимо убедиться, что они настроены правильно. 
* Для переноса виртуальных сетей, шлюзов и связанных развертываний в виртуальной сети, которые принадлежат не к той подписке, где находится канал ExpressRoute, требуется некоторое время простоя. В последнем разделе статьи описываются шаги, которые необходимо выполнить при переносе ресурсов.
* Виртуальную сеть с обоими шлюзами (ExpressRoute и VPN-шлюз) нельзя переместить.
* Перенос канала ExpressRoute в другие подписки не поддерживается. Дополнительные сведения см. в разделе [Поддержка перемещения Microsoft. Network](../azure-resource-manager/management/move-support-resources.md#microsoftnetwork).

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Перемещение канала ExpressRoute из классической модели развертывания в модель развертывания с помощью Resource Manager
Прежде чем приступить к переносу ресурсов, подключенных к каналу ExpressRoute, этот канал необходимо переместить из классической среды в среду Resource Manager. Для этого используйте приведенные ниже ссылки:

* Просмотрите сведения в статье [Перемещение каналов ExpressRoute из классической модели развертывания в модель развертывания с помощью Resource Manager](expressroute-move.md).
* [Перемещение каналов ExpressRoute из классической модели развертывания в модель Resource Manager с помощью PowerShell](expressroute-howto-move-arm.md).
* Используя портал управления службами Azure, выполните рабочий процесс для [создания канала ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) и выберите параметр импорта. 

При этой операции прости не возникают. Вы можете продолжить перемещение данных между локальной средой и Майкрософт во время выполнения миграции.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Перенос виртуальных сетей, шлюзов и связанных развертываний

Шаги, выполняемые для переноса, зависят от того, где находятся ресурсы: в одной подписке, в разных подписках, или оба варианта.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Перенос виртуальных сетей, шлюзов и связанных развертываний, которые находятся в той же подписке, что и канал ExpressRoute
В этом разделе описываются шаги, которые необходимо выполнить для переноса виртуальной сети, шлюза и связанных развертываний, находящихся в той же подписке, что и канал ExpressRoute. При таком переносе простои не возникают. Во время миграции можно продолжать использовать все ресурсы, однако плоскость управления заблокирована. 

1. Убедитесь, что канал ExpressRoute перемещен из классической среды в среду Resource Manager.
2. Убедитесь, что виртуальная сеть подготовлена для переноса.
3. Зарегистрируйте подписку для переноса ресурсов. Для этого используйте следующий фрагмент кода PowerShell:

   ```powershell 
   Select-AzSubscription -SubscriptionName <Your Subscription Name>
   Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   ```
4. Выполните проверку, подготовку и миграцию. Чтобы переместить виртуальную сеть, используйте приведенный ниже фрагмент кода PowerShell.

   ```powershell
   Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
   ```

   Вы также можете прервать миграцию, выполнив следующий командлет PowerShell:

   ```powershell
   Move-AzureVirtualNetwork -Abort $vnetName
   ```

## <a name="next-steps"></a>Дальнейшие действия
* [Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Техническое руководство по поддерживаемому платформой переносу из классической модели в модель Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [Platform-supported migration of IaaS resources from classic to Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md) (Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager)
* [Common errors during Classic to Azure Resource Manager migration](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Распространенные ошибки при переносе из классической модели на модель Azure Resource Manager)
