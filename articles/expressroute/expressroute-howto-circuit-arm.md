---
title: Краткое руководство. Создание и изменение канала с помощью ExpressRoute-Azure PowerShell
description: Создание, подготовка, проверка, обновление, удаление и отзыв канала ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: duau
ms.openlocfilehash: b5ac53c44429e23e2d22a934a9dc71bd485ec4cd
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761912"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit-using-azure-powershell"></a>Краткое руководство. Создание и изменение канала ExpressRoute с помощью Azure PowerShell

В этом кратком руководстве показано, как создать канал ExpressRoute с помощью командлетов PowerShell и модели развертывания Azure Resource Manager. Вы также сможете проверять состояние каналов, обновлять, удалять или отзывать их.

## <a name="prerequisites"></a>предварительные требования

* Изучите [предварительные требования](expressroute-prerequisites.md) и [рабочие процессы](expressroute-workflows.md), прежде чем приступить к настройке.
* Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.
* Локальная установка Azure PowerShell или Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Создание и предоставление канала ExpressRoute
### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Войдите в учетную запись Azure и выберите подписку.

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="get-the-list-of-supported-providers-locations-and-bandwidths"></a>Получение списка поддерживаемых поставщиков, расположений и значений пропускной способности
Перед созданием канала ExpressRoute потребуется список поддерживаемых поставщиков услуг подключения, расположений и вариантов пропускной способности.

Командлет PowerShell **Get-азекспрессраутесервицепровидер** возвращает эти сведения, которые будут использоваться в последующих шагах:

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

Проверьте, указан ли в списке поставщик услуг подключения. Запишите следующие сведения, которые потребуются позднее при создании канала:

* Имя
* PeeringLocations
* BandwidthsOffered

Теперь все готово к созданию канала ExpressRoute.

### <a name="create-an-expressroute-circuit"></a>Создание канала ExpressRoute
Перед созданием канала ExpressRoute необходимо создать группу ресурсов (если вы этого еще не сделали) с помощью следующей команды:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

В приведенном ниже примере показано, как создать канал ExpressRoute со скоростью 200 Мбит/с каналом через Equinix в Кремниевой долине. Если вы используете другой поставщик и другие параметры, замените эти сведения при выполнении запроса. Для запроса нового ключа службы воспользуйтесь следующим примером:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Убедитесь, что указаны правильный уровень SKU и семейство SKU:

* Уровень SKU определяет, является ли канал ExpressRoute [локальным](expressroute-faqs.md#expressroute-local), стандартным или [Premium](expressroute-faqs.md#expressroute-premium). Можно указать *Local*, * Standard или *Premium*. Нельзя изменить номер SKU с *Standard/Premium* на *локальный*.
* Семейство SKU определяет тип выставления счетов. Вы можете указать *MeteredData* для тарифного тарифного плана и *UnlimitedData* для неограниченного тарифного плана данных. Тип выставления счетов можно изменить с *MeteredData* на *UnlimitedData*, но нельзя изменить тип с *UnlimitedData* на *MeteredData*. *Локальная* цепь всегда *UnlimitedData*.

> [!IMPORTANT]
> Выставление счетов за использование ExpressRoute начинается после получения ключа службы. Обязательно выполните эту операцию, как только поставщик услуг подключения будет готов предоставить канал.
>

Ответ будет содержать ключ службы. Подробное описание всех параметров можно получить, выполнив следующую команду.

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="list-all-expressroute-circuits"></a>Получение списка всех каналов ExpressRoute
Чтобы получить список всех созданных каналов ExpressRoute, выполните команду **Get-азекспрессраутеЦиркуит** :

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Ответ выглядит примерно так:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_MeteredData",
                                    "Tier": "Standard",
                                    "Family": "MeteredData"
                                    }
CircuitProvisioningState          : Enabled
ServiceProviderProvisioningState  : NotProvisioned
ServiceProviderNotes              :
ServiceProviderProperties         : {
                                    "ServiceProviderName": "Equinix",
                                    "PeeringLocation": "Silicon Valley",
                                    "BandwidthInMbps": 200
                                    }
ServiceKey                        : **************************************
Peerings                          : []
```

Вы можете получить эти сведения в любое время с помощью командлета `Get-AzExpressRouteCircuit` . Если командлет вызывается без параметров, выводится список всех каналов. Ваш ключ службы показан в поле *ServiceKey*:

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```

Ответ выглядит примерно так:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_MeteredData",
                                    "Tier": "Standard",
                                    "Family": "MeteredData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : NotProvisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                    "ServiceProviderName": "Equinix",
                                    "PeeringLocation": "Silicon Valley",
                                    "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Отправка ключа службы поставщику услуг подключения для подготовки
*Параметр serviceproviderprovisioningstate* предоставляет сведения о текущем состоянии подготовки на стороне поставщика услуг. Состояние предоставляет состояние на стороне Майкрософт. Дополнительные сведения о состояниях подготовки канала см. в разделе о [рабочих процессах](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Вновь созданный канал ExpressRoute будет имеет следующее состояние:

```azurepowershell
ServiceProviderProvisioningState : NotProvisioned
CircuitProvisioningState         : Enabled
```

Канал переходит в следующее состояние, когда поставщик услуг подключения в данный момент включает его автоматически:

```azurepowershell
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

Чтобы использовать канал ExpressRoute, он должен находиться в следующем состоянии:

```azurepowershell
ServiceProviderProvisioningState : Provisioned
CircuitProvisioningState         : Enabled
```

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Периодическая проверка состояния и статуса ключа канала
Проверка состояния и состояния ключа службы позволит вам получить сведения о том, когда поставщик предоставил ваш канал. После настройки канала значение параметра *ServiceProviderProvisioningState* изменится на *Provisioned*, как показано в примере ниже.

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Ответ выглядит примерно так:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_MeteredData",
                                    "Tier": "Standard",
                                    "Family": "MeteredData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                    "ServiceProviderName": "Equinix",
                                    "PeeringLocation": "Silicon Valley",
                                    "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

### <a name="create-your-routing-configuration"></a>Создание конфигурации маршрутизации
Пошаговые инструкции по созданию и изменению пиринга каналов см. в статье [Создание и изменение маршрутизации для канала ExpressRoute](expressroute-howto-routing-arm.md).

> [!IMPORTANT]
> Эти инструкции распространяются только на каналы от поставщиков, предоставляющих услуги подключения второго уровня. Если ваш поставщик услуг подключения предлагает услуги третьего уровня (обычно это IPVPN, например MPLS), то он возьмет на себя настройку маршрутизации и управление ею.
>

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Связывание виртуальной сети с каналом ExpressRoute
Теперь свяжите виртуальную сеть с каналом ExpressRoute. При работе с моделью развертывания Resource Manager пользуйтесь статьей [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md) .

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Получение состояния канала ExpressRoute
Эти сведения можно получить в любое время с помощью командлета **Get-азекспрессраутеЦиркуит** . Если командлет вызывается без параметров, выводится список всех каналов.

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```

Ответ будет выглядеть примерно так:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_MeteredData",
                                    "Tier": "Standard",
                                    "Family": "MeteredData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                        "ServiceProviderName": "Equinix",
                                        "PeeringLocation": "Silicon Valley",
                                        "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

Для получения сведений об определенном канале ExpressRoute передайте имя группы ресурсов и имя канала как параметр вызова.

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Ответ выглядит примерно так:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                        "Name": "Standard_MeteredData",
                                        "Tier": "Standard",
                                        "Family": "MeteredData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                        "ServiceProviderName": "Equinix",
                                        "PeeringLocation": "Silicon Valley",
                                        "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

Подробное описание всех параметров можно получить, выполнив следующую команду.

```azurepowershell-interactive
get-help Get-AzExpressRouteCircuit -detailed
```

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Изменение канала ExpressRoute
Некоторые свойства канала ExpressRoute можно изменить, не повлияв на подключение.

Вы можете выполнять следующие задачи без простоя:

* включать и отключать надстройку ExpressRoute "Премиум" для канала ExpressRoute; Изменение номера SKU с *Standard/Premium* на *Local* не поддерживается.
* Увеличьте пропускную способность канала ExpressRoute, если имеется доступная емкость порта. Понижение пропускной способности канала не поддерживается.
* Перейдите с тарифного плана с оплатой за трафик на безлимитный тарифный план. Изменение плана отслеживания использования неограниченных данных на данные, отслеживаемые по расслеживанию, не поддерживается.
* Параметр *Allow Classic Operations*(Разрешить классические операции) можно включать и отключать.

Дополнительные сведения об ограничениях см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Включение надстройки ExpressRoute "Премиум"
Вы можете включить надстройку ExpressRoute "Премиум" для существующего канала с помощью следующего фрагмента кода PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Теперь для канала включены функции надстройки ExpressRoute "Премиум". С момента успешного выполнения команды мы начнем выставлять счета по тарифам надстройки уровня "Премиум".

### <a name="to-disable-the-expressroute-premium-add-on"></a>Отключение надстройки ExpressRoute "Премиум"
> [!IMPORTANT]
> Если использовать больше ресурсов, чем разрешено для канала "Стандартный", операция может завершиться ошибкой.
>

Обратите внимание на следующие сведения:

* Прежде чем переходить с канала "Премиум" на "Стандартный", убедитесь, что к каналу привязано менее 10 виртуальных сетей. Если этого не сделать, запрос на обновление завершится ошибкой и мы будем выставлять вам счета по тарифам ценовой категории "Премиум".
* Все виртуальные сети в других регионах геоадминистративной связи должны быть первыми несвязанными. Если вы не удалите эту ссылку, запрос на обновление завершится ошибкой, и мы продолжим выставлять счета по тарифам Premium.
* Для частного пиринга таблица маршрутов должна содержать менее 4000 маршрутов. Если размер таблицы маршрутов превышает 4 000 маршрутов, то сеанс BGP будет удален. Сеанс BGP не будет повторно включен, пока число объявленных префиксов не станет меньше 4 000.

Вы можете отключить надстройку ExpressRoute "Премиум" для существующего канала с помощью следующего командлета PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Обновление пропускной способности канала ExpressRoute
Параметры пропускной способности, поддерживаемые для вашего поставщика, приведены в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md). Можно выбрать любой размер, больший, чем размер существующего канала.

> [!IMPORTANT]
> Может потребоваться заново создать канал ExpressRoute, если существующий порт не обеспечивает достаточную емкость. Канал невозможно обновить, если в его расположении нет доступной дополнительной емкости.
>
> Уменьшить пропускную способность канала ExpressRoute без прерывания его работы нельзя. Для снижения пропускной способности нужно будет отозвать канал ExpressRoute и повторно подготовить новый канал ExpressRoute.
>

Выберите необходимый размер и используйте следующую команду для изменения размера канала:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Ваш канал будет обновлен на стороне Майкрософт. Свяжитесь с поставщиком услуг подключения и попросите обновить конфигурации с его стороны в соответствии с этим изменением. После отправки этого уведомления мы начнем выставлять счета за обновленный вариант пропускной способности.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Перевод SKU с измеряемых на неограниченные данные
Изменить SKU канала ExpressRoute можно, используя следующий фрагмент кода PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Управление доступом к классической среде и среде диспетчера ресурсов
Инструкции см. в статье [Перемещение каналов ExpressRoute из классической модели развертывания в модель Resource Manager](expressroute-howto-move-arm.md).

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>Отменяется инициализация канала ExpressRoute
Обратите внимание на следующие сведения:

* Все виртуальные сети должны быть разотроены из канала ExpressRoute. Если операция завершится ошибкой, проверьте, не привязаны ли к каналу какие-либо виртуальные сети.
* Если подготовка поставщика услуг канала ExpressRoute находится в состоянии **Идет подготовка** или **Подготовлено** то свяжитесь с поставщиком услуг, чтобы отозвать канал с его стороны. Мы будем резервировать ресурсы и выставлять вам счета до тех пор, пока поставщик услуг не завершит отзыв канала и не отправит нам соответствующее уведомление.
* Если поставщик услуг отменяет подготовку канала, что означает, что состояние подготовки поставщика услуг **не подготовлено**, можно удалить цепь. После этого выставление счетов для канала будет прервано.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Очистка ресурсов

Для удаления канала ExpressRoute выполните следующую команду:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Дальнейшие действия

После создания канала и подготовки его к работе с поставщиком перейдите к следующему шагу, чтобы настроить пиринг:

> [!div class="nextstepaction"]
> [Создание и изменение маршрутизации для канала ExpressRoute](expressroute-howto-routing-arm.md)
