---
title: Настройка Azure ExpressRoute Direct в Azure | Документация Майкрософт
description: Эта страница служит для настройки ExpressRoute Direct.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 0fec7234d18659051c61fda593b1ba0fb846c220
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65964262"
---
# <a name="how-to-configure-expressroute-direct"></a>Настройка ExpressRoute Direct

ExpressRoute Direct дает возможность подключения непосредственно в глобальной сети корпорации Майкрософт в стратегически распределенных по всему миру расположениях пиринга. Дополнительные сведения см. в разделе [About ExpressRoute Direct](expressroute-erdirect-about.md) (Общие сведения о подключении ExpressRoute Direct).

## <a name="resources"></a>Создание ресурса

1. Войдите в Azure и выберите подписку. Ресурс ExpressRoute Direct и каналы ExpressRoute должны находиться в одной подписке.

   ```powershell
   Connect-AzAccount 

   Select-AzSubscription -Subscription “<SubscriptionID or SubscriptionName>”
   ```
2. Выведите список всех расположений, где поддерживается ExpressRoute Direct.
  
   ```powershell
   Get-AzExpressRoutePortsLocation
   ```

   **Пример выходных данных**
  
   ```powershell
   Name                : Equinix-Ashburn-DC2
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-D
                        C2
   ProvisioningState   : Succeeded
   Address             : 21715 Filigree Court, DC2, Building F, Ashburn, VA 20147
   Contact             : support@equinix.com
   AvailableBandwidths : []

   Name                : Equinix-Dallas-DA3
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA
                        3
   ProvisioningState   : Succeeded
   Address             : 1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207
   Contact             : support@equinix.com
   AvailableBandwidths : []

   Name                : Equinix-San-Jose-SV1
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
   ProvisioningState   : Succeeded
   Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
   Contact             : support@equinix.com
   AvailableBandwidths : []
   ```
3. Определение, имеет ли место в приведенном выше списке доступную пропускную способность

   ```powershell
   Get-AzExpressRoutePortsLocation -LocationName "Equinix-San-Jose-SV1"
   ```

   **Пример выходных данных**

   ```powershell
   Name                : Equinix-San-Jose-SV1
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
   ProvisioningState   : Succeeded
   Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
   Contact             : support@equinix.com
   AvailableBandwidths : [
                          {
                            "OfferName": "100 Gbps",
                            "ValueInGbps": 100
                          }
                        ]
   ```
4. Создание ресурса ExpressRoute Direct на основе выбранного выше расположения

   ExpressRoute Direct поддерживает инкапсуляцию как QinQ, так и Dot1Q. Если выбран QinQ, каждому каналу ExpressRoute динамически назначается S-тег и каналы будут уникальными в пределах ресурса ExpressRoute Direct. Каждый C-тег в канале должен быть уникальным в пределах канала, но не для всего ExpressRoute Direct.  

   Если выбрана инкапсуляция Dot1Q, необходимо достичь уникальности C-тега (VLAN) в пределах всего ресурса ExpressRoute Direct.  

   > [!IMPORTANT]
   > ExpressRoute Direct может иметь только один тип инкапсуляции. После создания ExpressRoute Direct изменить инкапсуляцию невозможно.
   > 
 
   ```powershell 
   $ERDirect = New-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName -PeeringLocation $PeeringLocationName -BandwidthInGbps 100.0 -Encapsulation QinQ | Dot1Q -Location $AzureRegion
   ```

   > [!NOTE]
   > Атрибуту инкапсуляции также можно присвоить значение Dot1Q. 
   >

   **Пример выходных данных**

   ```powershell
   Name                       : Contoso-Direct
   ResourceGroupName          : Contoso-Direct-rg
   Location                   : westcentralus
   Id                         : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                               ressRoutePorts/Contoso-Direct
   Etag                       : W/"<etagnumber> "
   ResourceGuid               : <number>
   ProvisioningState          : Succeeded
   PeeringLocation            : Equinix-Seattle-SE2
   BandwidthInGbps            : 100
   ProvisionedBandwidthInGbps : 0
   Encapsulation              : QinQ
   Mtu                        : 1500
   EtherType                  : 0x8100
   AllocationDate             : Saturday, September 1, 2018
   Links                      : [
                                 {
                                   "Name": "link1",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link1",
                                   "RouterName": "tst-09xgmr-cis-1",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 },
                                 {
                                   "Name": "link2",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link2",
                                   "RouterName": "tst-09xgmr-cis-2",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]
   Circuits                   : []
   ```

## <a name="state"></a>Изменение параметра AdminState для ссылок

  Этот процесс следует использовать для проведения тестирования уровня 1, что в свою очередь обеспечит правильное исправление перекрестных подключений в каждом маршрутизаторе для основного и дополнительного подключения.
1. Получите подробные сведения об ExpressRoute Direct.

   ```powershell
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   ```
2. Установите параметр ссылки как Enabled (включена). Повторите этот шаг, чтобы установить состояние каждой ссылки как Enabled (включена).

   Ссылки [0] — это основной порт, а ссылки [1] — это дополнительный порт.

   ```powershell
   $ERDirect.Links[0].AdminState = “Enabled”
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   $ERDirect.Links[1].AdminState = “Enabled”
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   ```
   **Пример выходных данных**

   ```powershell
   Name                       : Contoso-Direct
   ResourceGroupName          : Contoso-Direct-rg
   Location                   : westcentralus
   Id                         : /subscriptions/<number>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                             ressRoutePorts/Contoso-Direct
   Etag                       : W/"<etagnumber> "
   ResourceGuid               : <number>
   ProvisioningState          : Succeeded
   PeeringLocation            : Equinix-Seattle-SE2
   BandwidthInGbps            : 100
   ProvisionedBandwidthInGbps : 0
   Encapsulation              : QinQ
   Mtu                        : 1500
   EtherType                  : 0x8100
   AllocationDate             : Saturday, September 1, 2018
   Links                      : [
                               {
                                 "Name": "link1",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link1",
                                 "RouterName": "tst-09xgmr-cis-1",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               },
                               {
                                 "Name": "link2",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link2",
                                 "RouterName": "tst-09xgmr-cis-2",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
   Circuits                   : []
   ```

   Используйте эту же процедуру с `AdminState = “Disabled”`, чтобы выключить порты.

## <a name="circuit"></a>Создание канала

По умолчанию можно создать до 10 каналов в подписке, в которой есть ресурс ExpressRoute Direct. Служба поддержки может увеличить это значение. Вы несете ответственность за отслеживание как подготовленной, так и используемой пропускной способности. Подготовленная пропускная способность — это сумма пропускных способностей всех каналов ресурса ExpressRoute Direct, а использованная пропускная способность — это физическое использование базовых физических интерфейсов.

Существуют дополнительные пропускные способности каналов, которые могут использоваться на ExpressRoute Direct только для поддержки сценариев, описанных выше. Эти особые значения приведены ниже. 40 Гбит/с и 100 Гбит/с.

**SkuTier** может быть локальной "," стандартный "или" уровня "премиум".

**SkuFamily** должно быть MeteredData только как неограниченное на ExpressRoute Direct не поддерживается.

Создайте канал в ресурсе ExpressRoute Direct.

  ```powershell
  New-AzExpressRouteCircuit -Name $Name -ResourceGroupName $ResourceGroupName -ExpressRoutePort $ERDirect -BandwidthinGbps 100.0  -Location $AzureRegion -SkuTier Premium -SkuFamily MeteredData 
  ```

  Другие значения пропускной способности: 5.0, 10.0 и 40.

  **Пример выходных данных**

  ```powershell
  Name                             : ExpressRoute-Direct-ckt
  ResourceGroupName                : Contoso-Direct-rg
  Location                         : westcentralus
  Id                               : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Netwo
                                   rk/expressRouteCircuits/ExpressRoute-Direct-ckt
  Etag                             : W/"<etagnumber>"
  ProvisioningState                : Succeeded
  Sku                              : {
                                     "Name": "Premium_MeteredData",
                                     "Tier": "Premium",
                                     "Family": "MeteredData"
                                   }
  CircuitProvisioningState         : Enabled
  ServiceProviderProvisioningState : Provisioned
  ServiceProviderNotes             : 
    ServiceProviderProperties        : null
  ExpressRoutePort                 : {
                                     "Id": "/subscriptions/<subscriptionID>n/resourceGroups/Contoso-Direct-rg/providers/Micros
                                   oft.Network/expressRoutePorts/Contoso-Direct"
                                   }
  BandwidthInGbps                  : 10
  Stag                             : 2
  ServiceKey                       : <number>
  Peerings                         : []
  Authorizations                   : []
  AllowClassicOperations           : False
  GatewayManagerEtag     
  ```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в разделе [About ExpressRoute Direct](expressroute-erdirect-about.md) (Общие сведения о подключении ExpressRoute Direct).
