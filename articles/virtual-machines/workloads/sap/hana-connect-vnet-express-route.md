---
title: Подключение, настроенное из виртуальной сети в SAP HANA на Azure (большие экземпляры) Документы Майкрософт
description: Связь, созданная из виртуальной сети для использования SAP HANA на Azure (большие экземпляры).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f7ac8e69c4e149fdd0f365e19f7a0282a547af43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617188"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Подключение виртуальной сети к крупным экземплярам HANA

После создания виртуальной сети Azure, вы можете подключить сеть к SAP HANA в крупных экземплярах Azure. Создайте шлюз Azure ExpressRoute в виртуальной сети. Этот шлюз позволяет связать виртуальную сеть с схемой ExpressRoute, которая подключается к клиенту-арендатору на марке HANA Large Instance.

> [!NOTE] 
> Этот шаг может занять до 30 минут, так как при этом создается шлюз в обозначенной подписке Azure, который затем подключается к указанной виртуальной сети Azure.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Если шлюз уже существует, проверьте, является ли он шлюзом ExpressRoute. Если это не шлюз ExpressRoute, удалите шлюз и воссоздайте его в качестве шлюза ExpressRoute. Если шлюз ExpressRoute уже установлен, обратитесь к разделу данной статьи "Связывание виртуальных сетей". 

- Используйте [портал Azure](https://portal.azure.com/) или PowerShell, чтобы создать VPN-шлюз ExpressRoute, подключенный к виртуальной сети.
  - Если вы используете портал Azure, добавьте новый **виртуальный сетевой шлюз,** а затем выберите **ExpressRoute** в качестве типа шлюза.
  - Если вы используете PowerShell, сначала загрузите и используйте последнюю версию [Пакета SDK для Azure PowerShell](https://azure.microsoft.com/downloads/). 
 
Следующие команды создают шлюз ExpressRoute. Тексты, которым _$_ предшествует, являются пользовательскими переменными, которые должны быть обновлены с вашей конкретной информацией.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

В этом примере использовался номер SKU шлюза HighPerformance. Существуют следующие варианты: HighPerformance или UltraPerformance. Это единственные номера SKU шлюза, которые поддерживает SAP HANA в Azure (крупные экземпляры).

> [!IMPORTANT]
> Для крупных экземпляров HANA с номерами SKU класса 2 вы должны использовать SKU "UltraPerformance Gateway".

## <a name="link-virtual-networks"></a>Связывание виртуальных сетей

Теперь, когда для виртуальной сети Azure создан шлюз ExpressRoute, Используйте информацию о авторизации, предоставленную корпорацией Майкрософт, для подключения шлюза ExpressRoute к схеме SAP HANA Large Instances ExpressRoute. Вы можете подключиться с помощью портала Azure или PowerShell. Инструкции PowerShell следующие. 

Выполнить следующие команды для каждого шлюза ExpressRoute, используя различные AuthGUID для каждого соединения. Первые две записи, приведенные в сценарии ниже, получены из сведений, предоставленных корпорацией Майкрософт. Кроме того, каждый AuthGUID связан с конкретной виртуальной сетью и шлюзом. Если вы хотите добавить другую виртуальную сеть Azure, вам необходимо получить еще один AuthID для вашей схемы ExpressRoute, которая соединяет большие экземпляры HANA в Azure от Microsoft. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> Последний параметр в команде New-AzVirtualNetworkGatewayConnection, **ExpressRouteGatewayBypass** является новым параметром, который позволяет ExpressRoute Быстрый путь. Функциональность, которая уменьшает задержку сети между подразделениями HANA Large Instance и Azure VMs. Функциональность была добавлена в мае 2019 года. Для получения более подробной информации, проверить статью [SAP HANA (Большие инстанции) сетевой архитектуры](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture). Перед запуском команд убедитесь, что вы работаете с последней версией cmdlets PowerShell.

Возможно, вам потребуется сделать это несколько раз, чтобы подключить шлюз к нескольким каналам ExpressRoute, связанным с вашей подпиской. Например, скорее всего, вам потребуется подключить тот же шлюз виртуальной сети к каналу ExpressRoute, с помощью которого виртуальная сеть подключается к локальной сети.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Применение скоростного маршрута ExpressRoute к существующим схемам HANA Large Instance ExpressRoute
В документации до сих пор объяснялось, как подключить новую схему ExpressRoute, созданную с развертыванием HANA Large Instance, к шлюзу Azure ExpressRoute одной из виртуальных сетей Azure. Но многие клиенты уже имеют свои схемы ExpressRoute настройки уже и их виртуальные сети подключены к HANA Больших инстанций уже. Поскольку новый скоростной путь ExpressRoute сокращает задержку сети, рекомендуется применить изменение для использования этой функциональности. Команды для подключения новой цепи ExpreesRoute и изменения существующей схемы ExpressRoute одинаковы. В результате необходимо запустить эту последовательность команд PowerShell, чтобы изменить существующую схему для использования 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

Важно, чтобы вы добавили последний параметр, как показано выше, чтобы включить функциональность ExpressRoute Fast Path


## <a name="expressroute-global-reach"></a>Global Reach ExpressRoute
Как вы хотите включить Глобальный охват для одного или обоих из двух сценариев:

 - Репликация системы HANA без каких-либо дополнительных прокси или брандмауэров
 - Копирование резервных копий между подразделениями HANA Large Instance в двух разных регионах для выполнения системных копий или обновления системы

Вам нужно учитывать, что:

- Вам необходимо предоставить диапазон адресного пространства в /29 адресного пространства. Этот диапазон адресов может не совпадать с любыми другими диапазонами пространства адреса, которые вы использовали до сих пор, соединяя HANA Large Instances к Azure, и не может перекрываться ни с одной из диапазонов IP-адресов, которые вы использовали где-то еще в Azure или в помещениях.
- Существует ограничение на ASNs (Автономный номер системы), которые могут быть использованы для рекламы ваших маршрутов в помещениях в HANA Large Instances. Ваши помещения не должны рекламировать какие-либо маршруты с частными ИНН в диапазоне 65000 - 65020 или 65515. 
- Для сценария подключения к антеповому прямому доступу к экземплярам HANA Large необходимо рассчитать плату за схему, которая соединяет вас с Azure. Для цен, проверить цены на [Global Reach Add-On](https://azure.microsoft.com/pricing/details/expressroute/).

Чтобы получить один или оба сценария, применяемых к развертыванию, откройте сообщение поддержки с Azure, как описано в [Open a support request for HANA large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)

Необходимые данные и ключевые слова, необходимые для microsoft, чтобы иметь возможность маршрутизать и выполнять по вашему запросу, выглядит следующим образом:

- Обслуживание: SAP HANA Большой экземпляр
- Тип проблемы: Конфигурация и настройка
- Подтип проблемы: Моя проблема не указана выше
- Тема 'Изменить мою сеть - добавить Глобальный охват'
- Подробности: «Добавьте Global Reach в HANA Large Instance к арендатору HANA Large Instance или 'Добавить Global Reach в помещение для арендатора HANA Large Instance.
- Дополнительные сведения для случая с арендатором HANA Large Instance для арендаторов HANA Large Instance: Необходимо определить **два региона Azure,** где расположены два клиента для подключения **и** вам нужно представить **диапазон IP-адресов /29**
- Дополнительные сведения для локальных кейсов для арендаторов HANA Large Instance: Необходимо определить **регион Azure,** где находится арендатор HANA Large Instance, к которому нужно подключиться напрямую. Кроме того, необходимо предоставить **идентификатор** **Auth GUID** и Circuit Peer ID, полученный при установлении схемы ExpressRoute между помещениями и Azure. Кроме того, вам нужно назвать свой **ASN.** Последним результатом является **диапазон IP-адресов /29** для ExpressRoute Global Reach.

> [!NOTE]
> Если вы хотите, чтобы оба случая обрабатываются, вам нужно предоставить два различных /29 диапазонов IP-адресов, которые не пересекаются с любым другим диапазоном IP-адресов, используемых до сих пор. 




## <a name="next-steps"></a>Дальнейшие действия

- [Дополнительные требования к сети для крупных экземпляров HANA](hana-additional-network-requirements.md)
