---
title: Перенос виртуальных сетей из классической модели развертывания в модель развертывания с помощью Resource Manager, используя ExpressRoute и Azure PowerShell | Документация Майкрософт
description: В этой статье описывается, как перенести виртуальные сети, связанные с ExpressRoute, в Resource Manager после перемещения канала.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: ganesr;cherylmc
ms.custom: seodec18
ms.openlocfilehash: 2e33454ac0ee97385386043706f4b8b73090f57a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60363858"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Перенос виртуальных сетей, связанных с ExpressRoute, из классической модели в модель развертывания с помощью Resource Manager

В этой статье описывается, как после перемещения канала ExpressRoute перенести связанные с ним виртуальные сети из классической модели развертывания в модель развертывания с помощью Azure Resource Manager. 

## <a name="before-you-begin"></a>Перед началом работы

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Убедитесь в наличии последней версии модулей Azure PowerShell. Подробнее: [Установка и настройка Azure PowerShell](/powershell/azure/overview).
* Не забудьте изучить [предварительные требования](expressroute-prerequisites.md), [требования к маршрутизации](expressroute-routing.md) и [рабочие процессы](expressroute-workflows.md), прежде чем приступать к настройке.
* Просмотрите сведения в статье [Перемещение каналов ExpressRoute из классической модели развертывания в модель развертывания с помощью Resource Manager](expressroute-move.md). Убедитесь, что вам полностью понятны пределы и ограничения.
* Убедитесь, что канал полноценно работает в классической модели развертывания.
* Убедитесь в наличии группы ресурсов, созданной в модели развертывания Resource Manager.
* Ознакомьтесь со следующими статьями о переносе ресурсов:

    * [Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Техническое руководство по поддерживаемому платформой переносу из классической модели в модель Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [Часто задаваемые вопросы. Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Common errors during Classic to Azure Resource Manager migration](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Распространенные ошибки при переносе из классической модели на модель Azure Resource Manager)

## <a name="supported-and-unsupported-scenarios"></a>Поддерживаемые и неподдерживаемые сценарии

* Канал ExpressRoute можно перенести из классической среды в среду Resource Manager без простоев. Вы можете переместить любой канал ExpressRoute из классической среды в среду Resource Manager без простоев. Следуйте инструкциям в статье [Перемещение каналов ExpressRoute из классической модели развертывания в модель Resource Manager с помощью PowerShell](expressroute-howto-move-arm.md). Перемещение ресурсов, подключенных к виртуальной сети, является обязательным условием.
* Виртуальные сети, шлюзы и связанные развертывания в виртуальной сети, подключенные к каналу ExpressRoute в одной подписке, можно перенести в среду Resource Manager без простоев. Выполните приведенные ниже действия, чтобы перенести ресурсы, такие как виртуальные сети, шлюзы и виртуальные машины, развернутые в виртуальной сети. Прежде чем приступать к переносу виртуальных сетей, необходимо убедиться, что они настроены правильно. 
* Для переноса виртуальных сетей, шлюзов и связанных развертываний в виртуальной сети, которые принадлежат не к той подписке, где находится канал ExpressRoute, требуется некоторое время простоя. В последнем разделе статьи описываются шаги, которые необходимо выполнить при переносе ресурсов.
* Виртуальную сеть с обоими шлюзами (ExpressRoute и VPN-шлюз) нельзя переместить.
* Перенос канала ExpressRoute в другие подписки не поддерживается. Дополнительные сведения см. в разделе [Службы, которые невозможно переместить](../azure-resource-manager/resource-group-move-resources.md#services-that-cannot-be-moved).

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
* [Часто задаваемые вопросы. Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Common errors during Classic to Azure Resource Manager migration](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Распространенные ошибки при переносе из классической модели на модель Azure Resource Manager)
