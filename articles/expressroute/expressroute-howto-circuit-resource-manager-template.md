---
title: 'Создание канала ExpressRoute - шаблон Resource Manager: Azure | Документация Майкрософт'
description: Создание, подготовка, удалить и отозвать канал ExpressRoute.
services: expressroute;azure-resource-manager
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 07/05/2019
ms.author: cherylmc;ganesr
ms.openlocfilehash: bf56145d0a8cd3b01d0d74fcaf3348c1916cee5a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659688"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Создание канала ExpressRoute с помощью шаблона Azure Resource Manager

> [!div class="op_single_selector"]
> * [портал Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Интерфейс командной строки Azure](howto-circuit-cli.md)
> * [Шаблон Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Видео — портал Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (классическая модель)](expressroute-howto-circuit-classic.md)
>

Узнайте, как создать канал ExpressRoute, развернув шаблон диспетчера ресурсов Azure с помощью Azure PowerShell. Дополнительные сведения о разработке шаблонов Resource Manager см. в разделе [Документация по Azure Resource Manager](/azure/azure-resource-manager/) и в [справочнике по шаблонам](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Перед началом работы

* Изучите [предварительные требования](expressroute-prerequisites.md) и [рабочие процессы](expressroute-workflows.md), прежде чем приступить к настройке.
* Убедитесь в том, что у вас есть разрешения на создание сетевых ресурсов. Если у вас нет нужных разрешений, обратитесь к администратору учетной записи.
* Вы можете [просмотреть видео](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit), прежде чем начать, чтобы лучше понять выполняемые действия.

## <a name="create"></a>Создание и подготовка канала ExpressRoute

[Шаблоны быстрого запуска Azure](https://azure.microsoft.com/resources/templates/) есть хороший набор шаблона Resource Manager. Используйте один из [существующие шаблоны](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) для создания канала ExpressRoute.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Чтобы просмотреть другие шаблоны, выберите [здесь](https://azure.microsoft.com/resources/templates/?term=expressroute).

Чтобы создать канал ExpressRoute, развернув шаблон:

1. Выберите **попробовать** из следующих блок кода и следуйте инструкциям для входа в Azure Cloud shell.

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

   * **Уровень** определяет, какого уровня надстройка включена — ExpressRoute "Стандартный" или ExpressRoute "Премиум". Укажите **Standard**, чтобы получить SKU Standard, или **Premium**, чтобы получить надстройку Premium.

   * **Расположение пиринга** — физическое расположение пиринга с корпорацией Майкрософт.

     > [!IMPORTANT]
     > Параметр "Расположение пиринга" определяет [физическое расположение](expressroute-locations.md) пиринга с корпорацией Майкрософт. Оно **не** связано со свойством Location, которое ссылается на географический регион, в котором находится поставщик сетевых ресурсов Azure. Хотя они не связаны, рекомендуется выбрать поставщик сетевых ресурсов, находящийся недалеко от расположения пиринга канала.

    Имя группы ресурсов — это имя пространства имен служебной шины с **rg** добавляется.

2. Нажмите кнопку **Копировать**, чтобы скопировать сценарий PowerShell.
3. Щелкните правой кнопкой мыши в консоли оболочки, а затем выберите **вставить**.

Занимает несколько секунд, чтобы создать концентратор событий.

Чтобы развернуть шаблон в этом руководстве используется Azure PowerShell. Другие методы развертывания для шаблонов см. в разделе:

* [С помощью портала Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).
* [С помощью Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md).
* [С помощью REST API](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="delete"></a>Отзыв и удаление канала ExpressRoute

Канал ExpressRoute можно удалить, щелкнув значок **Удалить** . Обратите внимание на следующие сведения:

* Связь между ExpressRoute и всеми виртуальными сетями необходимо разорвать. Если операция завершится ошибкой, проверьте, не привязаны ли к каналу какие-либо виртуальные сети.
* Если подготовка поставщика услуг канала ExpressRoute находится в состоянии **Идет подготовка** или **Подготовлено** то свяжитесь с поставщиком услуг, чтобы отозвать канал с его стороны. Мы будем резервировать ресурсы и выставлять вам счета до тех пор, пока поставщик услуг не завершит отзыв канала и не отправит нам соответствующее уведомление.
* Если поставщик услуг отзовет канал (состояние подготовки поставщика услуг изменится на **Не подготовлено**), вы можете удалить такой канал. Это приостанавливает выставление счетов для канала.

Цепь ExpressRoute можно удалить, выполнив следующую команду PowerShell:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Следующие шаги

Создав канал, выполните задачи, описанные в следующих статьях:

* [Создание и изменение маршрутизации для канала ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md)
