---
title: Создание и управление публичным пирингом Azure ExpressRoute
description: Узнайте о публичных пирингах Azure и управлении им
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: cherylmc
ms.openlocfilehash: 8c1afac834fb9abb2cbf82f16f046a1624b251f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481139"
---
# <a name="create-and-manage-expressroute-public-peering"></a>Создание и управление общественным пирингом ExpressRoute

> [!div class="op_single_selector"]
> * [Статья - Публичный пиринг](about-public-peering.md)
> * [Видео — общедоступный пиринг](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Статья - Microsoft пиринг](expressroute-circuit-peerings.md#microsoftpeering)
>

Эта статья помогает создавать и управлять общедоступной конфигурацией маршрутирования для схемы ExpressRoute. Вы также можете проверить состояние, обновить или удалить и дезавуировать пиринги. Эта статья относится к схемам управления ресурсами, которые были созданы до того, как было уничено публичное пиринг. Если у вас есть ранее существующая схема (созданная до того, как уанклаемые публичные пиринги), вы можете управлять/настроить публичное пиринг с помощью [Azure PowerShell,](#powershell) [Azure CLI](#cli)и [портала Azure.](#portal)

>[!NOTE]
>Публичное пиринг обесценивается. Вы не можете создать публичное пиринг на новых схемах ExpressRoute. Если у вас есть новая схема ExpressRoute, вместо этого используйте [внештатные microsoft](expressroute-circuit-peerings.md#microsoftpeering) для служб Azure.
>

## <a name="connectivity"></a>Соединение

Подключение всегда инициируется из глобальной сети к службам Microsoft Azure. Службы Microsoft Azure не будут иметь возможность инициировать подключения к вашей сети через этот домен маршрутизации. Если схема ExpressRoute включена для публичного пиринга Azure, вы можете получить доступ к [общедоступным диапазонам IP, используемым в Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) по схеме.

После включения общедоступных пирингов можно подключиться к большинству служб Azure. Мы не позволяем выбирать службы, для которых объявляются маршруты.

* Такие услуги, как Хранилище Azure, базы данных S'L и веб-сайты, предлагаются на общедоступных IP-адресах.
* Через общедоступный домен для вглядывающихся в систему можно в частном порядке подключиться к службам, размещенным на общедоступных IP-адресах, включая VIP-персоны ваших облачных сервисов.
* Вы можете подключить домен общедоступного пиринга к своей сети периметра и подключаться ко всем службам Azure по их общедоступным IP-адресам из своей глобальной сети без необходимости подключения через Интернет.

## <a name="services"></a><a name="services"></a>Службы

В этом разделе показаны услуги, доступные по сравнению с публичным пирингом. Поскольку публичные пиринги не утихли, нет никакого плана добавления новых или дополнительных услуг для публичного пиринга. Если вы используете общедоступные пиринги и служба, которая требуется использовать, поддерживается только через microsoft, вы должны переключиться на microsoft peering. Ознакомьтесь со списком поддерживаемых служб [корпорации Майкрософт.](expressroute-faqs.md#microsoft-peering)

**Поддерживается:**

* Power BI
* Большинство служб Azure поддерживается. Проверьте непосредственно с службой, которую вы хотите использовать для проверки поддержки.

**Не поддерживается:**
  * CDN
  * Azure Front Door
  * Многофакторный сервер аутентификации (наследие)
  * Диспетчер трафика

Чтобы проверить доступность для конкретной службы, можно проверить документацию для этой службы, чтобы увидеть, есть ли зарезервированный диапазон, опубликованный для этой службы. Затем вы можете посмотреть диапазоны IP целевой службы и сравнить с диапазонами, перечисленными в [диапазонах IP-адресов Azure и тегах служб - Public Cloud XML file.](https://www.microsoft.com/download/details.aspx?id=56519) Кроме того, вы можете открыть билет поддержки для службы, о котором идет речь для уточнения.

## <a name="peering-comparison"></a><a name="compare"></a>Сравнение пирингов

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

> [!NOTE]
> В общедоступном пиринге Azure есть 1 IP-адрес NAT, связанный с каждой сессией BGP. Для более чем 2 IP-адресов NAT перейдите на пиринг Microsoft. Microsoft peering позволяет настроить собственные распределения NAT, а также использовать фильтры маршрутов для выборочной рекламы префикса. Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/expressroute/how-to-move-peering)
>

## <a name="custom-route-filters"></a>Пользовательские фильтры маршрута

Вы можете задавать в своей сети настраиваемые фильтры маршрутов, чтобы использовать только нужные маршруты. Подробные сведения о настройке маршрутизации см. на странице [Маршрутизация](expressroute-routing.md).

## <a name="azure-powershell-steps"></a><a name="powershell"></a>Шаги Azure PowerShell


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

Поскольку публичные пиринги унижаются, вы не можете настроить публичное пиринг на новой схеме ExpressRoute.

1. Убедитесь, что у вас есть схема ExpressRoute, которая подготовлена, а также включена. Используйте следующий пример:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   Ответ будет выглядеть примерно так:

   ```
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
2. Настройте для канала общедоступный пиринг Azure. Прежде чем продолжить, убедитесь, что у вас есть следующие сведения:

   * Подсеть /30 для основной ссылки. Это должен быть допустимый префикс общедоступного адреса IPv4.
   * Подсеть /30 для дополнительной ссылки. Это должен быть допустимый префикс общедоступного адреса IPv4.
   * Действительный идентификатор виртуальной локальной сети для установки пиринга. Идентификатор не должен использоваться ни одним другим пирингом в канале.
   * Номер AS для пиринга. Можно использовать 2-байтовые и 4-байтовые номера AS.
   * Необязательное действие:
   * Хэш MD5, если вы решите его использовать.

   Чтобы настроить для канала общедоступный пиринг Azure, выполните код из примера ниже

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Если вы решили использовать MD5-хэш, используйте следующий пример:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > Номер AS должен быть указан в качестве ASN пиринга, а не ASN клиента.
   > 
   >

### <a name="to-get-azure-public-peering-details"></a><a name="getpublic"></a>Получение сведений об общедоступном пиринге Azure

Для получения сведений о конфигурации можно использовать следующий командлет.

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Обновление конфигурации общедоступного пиринга Azure

С помощью следующего примера можно обновить любую часть конфигурации. В приведенном ниже примере значение идентификатора виртуальной локальной сети для канала изменяется с 200 на 600.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Удаление общедоступного пиринга Azure

Для удаления конфигурации пиринга выполните следующий пример кода:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-cli-steps"></a><a name="cli"></a>Шаги Azure CLI


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

1. Убедитесь, что канал ExpressRoute подготовлен и включен. Используйте следующий пример:

   ```azurecli-interactive
   az network express-route list
   ```

   Ответ будет выглядеть примерно так:

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

2. Настройте для канала общедоступный пиринг Azure. Прежде чем продолжить, убедитесь, что у вас есть следующие сведения:

   * Подсеть /30 для основной ссылки. Это должен быть допустимый префикс общедоступного адреса IPv4.
   * Подсеть /30 для дополнительной ссылки. Это должен быть допустимый префикс общедоступного адреса IPv4.
   * Действительный идентификатор виртуальной локальной сети для установки пиринга. Идентификатор не должен использоваться ни одним другим пирингом в канале.
   * Номер AS для пиринга. Можно использовать 2-байтовые и 4-байтовые номера AS.
   * **Необязательно.** Хэш MD5, если вы решите его использовать.

   Чтобы настроить для канала общедоступный пиринг Azure, выполните код из следующего примера:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   Если вы решили использовать MD5-хэш, используйте следующий пример:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Номер AS должен быть указан в качестве ASN пиринга, а не ASN клиента.

### <a name="to-view-azure-public-peering-details"></a><a name="getpublic"></a>Просмотр сведений об общедоступном пиринге Azure

Для получения сведений о конфигурации можно использовать следующий пример:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Обновление конфигурации общедоступного пиринга Azure

С помощью следующего примера можно обновить любую часть конфигурации. В приведенном ниже примере значение идентификатора виртуальной локальной сети для канала изменяется с 200 на 600.

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Удаление общедоступного пиринга Azure

Для удаления конфигурации пиринга выполните следующий пример кода:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="azure-portal-steps"></a><a name="portal"></a>Инструкции для портала Azure

Для настройки пиринга используйте шаги PowerShell или CLI, содержащиеся в этой статье. Для управления внимательством можно использовать разделы ниже. Для справки эти шаги похожи на управление [вглядывающимся в портал Microsoft.](expressroute-howto-routing-portal-resource-manager.md#msft)

### <a name="to-view-azure-public-peering-details"></a><a name="get"></a>Просмотр сведений об общедоступном пиринге Azure

Просмотр свойств общедоступного обзора Azure, выбрав пиринг в портале.

### <a name="to-update-azure-public-peering-configuration"></a><a name="update"></a>Обновление конфигурации общедоступного пиринга Azure

Выберите строку для пиринга, а затем измените свойства вpeering.

### <a name="to-delete-azure-public-peering"></a><a name="delete"></a>Удаление общедоступного пиринга Azure

Удалите конфигурацию peering, выбрав значок удаления.

## <a name="next-steps"></a>Дальнейшие действия

Следующий шаг, [Свяжите виртуальную сеть к цепи ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Дополнительную информацию о рабочих процессах ExpressRoute см. в статье [Процедуры ExpressRoute для подготовки каналов и состояний каналов](expressroute-workflows.md).
* Дополнительную информацию о пиринге канала см. в статье [Каналы ExpressRoute и домены маршрутизации](expressroute-circuit-peerings.md).
* Подробнее о работе с виртуальными сетями см. в статье [Обзор виртуальных сетей](../virtual-network/virtual-networks-overview.md).