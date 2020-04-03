---
title: 'Шаблон Azure ExpressRoute: Создание схемы ExpressRoute'
description: Создание, предоставление, удаление и дезавуианите схему ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 11/13/2019
ms.author: charwen
ms.openlocfilehash: ac2fb2f884feb0fbe6fbb1731ebe7c8bdad5114b
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616501"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Создайте схему ExpressRoute с помощью шаблона менеджера ресурсов Azure

> [!div class="op_single_selector"]
> * [Портал Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Шаблон менеджера ресурсов Azure](expressroute-howto-circuit-resource-manager-template.md)
> * [Видео - Портал Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (классический)](expressroute-howto-circuit-classic.md)
>

Узнайте, как создать схему ExpressRoute, развернув шаблон менеджера ресурсов Azure с помощью Azure PowerShell. Дополнительные сведения о разработке шаблонов Resource Manager см. в разделе [Документация по Azure Resource Manager](/azure/azure-resource-manager/) и в [справочнике по шаблонам](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Перед началом

* Изучите [предварительные требования](expressroute-prerequisites.md) и [рабочие процессы](expressroute-workflows.md), прежде чем приступить к настройке.
* Убедитесь в том, что у вас есть разрешения на создание сетевых ресурсов. Если у вас нет нужных разрешений, обратитесь к администратору учетной записи.
* Вы можете [просмотреть видео](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit), прежде чем начать, чтобы лучше понять выполняемые действия.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Создание и предоставление канала ExpressRoute

[Шаблоны Azure quickstart](https://azure.microsoft.com/resources/templates/) имеет хорошую коллекцию шаблонов Resource Manager. Для создания схемы ExpressRoute используется один из [существующих шаблонов.](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/)

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Чтобы увидеть больше связанных шаблонов, выберите [здесь](https://azure.microsoft.com/resources/templates/?term=expressroute).

Для создания схемы ExpressRoute путем развертывания шаблона:

1. Выберите **Попробовать** в следующем блоке кода и следуйте инструкциям, чтобы войти в Azure Cloud Shell.

    ```azurepowershell-interactive
    $circuitName = Read-Host -Prompt "Enter a circuit name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${circuitName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-circuit-create/azuredeploy.json"

    $serviceProviderName = "Equinix"
    $peeringLocation = "Silicon Valley"
    $bandwidthInMbps = 500
    $sku_tier = "Premium"
    $sku_family = "MeteredData"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -circuitName $circuitName -serviceProviderName $serviceProviderName -peeringLocation $peeringLocation -bandwidthInMbps $bandwidthInMbps -sku_tier $sku_tier -sku_family $sku_family

    Write-Host "Press [ENTER] to continue ..."
    ```

   * **Уровень SKU** определяет, является ли схема ExpressRoute [локальной,](expressroute-faqs.md#expressroute-local)стандартной или [премиум-](expressroute-faqs.md#expressroute-premium) Вы можете указать *местные*, стандартные или *премиум*.
   * **Семья SKU** определяет тип выставления счетов. Можно указать *Metereddata* для дозированного плана данных и *Unlimiteddata* для неограниченного плана данных. Тип выставления счетов можно изменить с *Metereddata* на *Unlimiteddata* но не наоборот. *Unlimiteddata* для *Metereddata* *Локальная* схема только *неограниченная.*
   * **Расположение пиринга** — физическое расположение пиринга с корпорацией Майкрософт.

     > [!IMPORTANT]
     > Параметр "Расположение пиринга" определяет [физическое расположение](expressroute-locations.md) пиринга с корпорацией Майкрософт. Оно **не** связано со свойством Location, которое ссылается на географический регион, в котором находится поставщик сетевых ресурсов Azure. Хотя они не связаны, рекомендуется выбрать поставщик сетевых ресурсов, находящийся недалеко от расположения пиринга канала.

    Имя группы ресурсов — это имя пространства имен службы с придативаемым **rg.**

2. Нажмите кнопку **Копировать**, чтобы скопировать сценарий PowerShell.
3. Щелкните правой кнопкой в консоли оболочки и выберите **Вставить**.

Создание концентратора событий занимает несколько секунд.

Azure PowerShell используется для развертывания шаблона в этом учебнике. Для других методов развертывания шаблонов см.:

* [С помощью портала Azure](../azure-resource-manager/templates/deploy-portal.md).
* [С помощью Azure CLI](../azure-resource-manager/templates/deploy-cli.md).
* [С помощью REST API](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>отзыве и удалении канала ExpressRoute

Вы можете удалить схему ExpressRoute, выбрав значок **удаления.** Обратите внимание на следующие сведения:

* Связь между ExpressRoute и всеми виртуальными сетями необходимо разорвать. Если операция завершится ошибкой, проверьте, не привязаны ли к каналу какие-либо виртуальные сети.
* Если поставщик услуг по предоставлению услуг по кольцевым услугам ExpressRoute **предоставляет** или **предоставляет,** вы должны работать с поставщиком услуг, чтобы дезавуировать схему на их стороне. Мы будем резервировать ресурсы и выставлять вам счета до тех пор, пока поставщик услуг не завершит отзыв канала и не отправит нам соответствующее уведомление.
* Если поставщик услуг дезавуировал схему (состояние подготовки поставщика услуг **установлено не предусмотрено),** вы можете удалить схему. Это приостанавливает выставление счетов для канала.

Вы можете удалить схему ExpressRoute, запустив следующую команду PowerShell:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Следующие шаги

Создав канал, выполните задачи, описанные в следующих статьях:

* [Создание и изменение маршрутизации для канала ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md)
