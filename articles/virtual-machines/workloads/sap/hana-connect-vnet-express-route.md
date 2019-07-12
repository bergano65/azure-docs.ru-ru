---
title: Подключение установить из виртуальной сети к SAP HANA в Azure (крупные экземпляры) | Документация Майкрософт
description: Подключение Настройка из виртуальной сети для использования SAP HANA в Azure (крупные экземпляры).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ebe303b24c497fe8ac52ac90a236a23c279ea2e9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709673"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Подключение виртуальной сети к крупным экземплярам HANA

После создания виртуальной сети Azure, вы можете подключить сеть к SAP HANA в крупных экземплярах Azure. Создайте шлюз Azure ExpressRoute в виртуальной сети. Этот шлюз позволяет связать виртуальную сеть к каналу ExpressRoute, который подключается к клиенту пользователя по стека крупных экземпляров HANA.

> [!NOTE] 
> Этот шаг может занять до 30 минут, так как при этом создается шлюз в обозначенной подписке Azure, который затем подключается к указанной виртуальной сети Azure.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Если шлюз уже существует, проверьте, является ли он шлюзом ExpressRoute. Если это не шлюз ExpressRoute, удалить шлюз и заново создать его как шлюз ExpressRoute. Если шлюз ExpressRoute уже установлен, обратитесь к разделу данной статьи "Связывание виртуальных сетей". 

- Используйте [портал Azure](https://portal.azure.com/) или PowerShell, чтобы создать VPN-шлюз ExpressRoute, подключенный к виртуальной сети.
  - Если вы используете портал Azure, добавьте новый **шлюз виртуальной сети**, а затем, в качестве типа шлюза выберите **ExpressRoute**.
  - Если вы используете PowerShell, сначала загрузите и используйте последнюю версию [Пакета SDK для Azure PowerShell](https://azure.microsoft.com/downloads/). 
 
Следующие команды создают шлюз ExpressRoute. Тексты предшествует _$_ — это определяемые пользователем переменные, которые должны обновляться с соответствующей информацией.

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

Теперь, когда для виртуальной сети Azure создан шлюз ExpressRoute, Используйте сведения об авторизации, предоставляемые корпорацией Майкрософт для подключения шлюза ExpressRoute к каналу ExpressRoute крупных экземпляров SAP HANA. Вы можете подключиться с помощью портала Azure или PowerShell. Ниже приведены инструкции для PowerShell. 

Выполните следующие команды для каждого шлюза ExpressRoute, используя разные AuthGUID для каждого подключения. Первые две записи, приведенные в сценарии ниже, получены из сведений, предоставленных корпорацией Майкрософт. Кроме того, каждый AuthGUID связан с конкретной виртуальной сетью и шлюзом. Если вы хотите добавить другой виртуальной сети Azure, необходимо получить другой AuthID для канала ExpressRoute, соединяет крупные экземпляры HANA в Azure от корпорации Майкрософт. 

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
> Последний параметр в команде New-AzVirtualNetworkGatewayConnection **ExpressRouteGatewayBypass** добавлен новый параметр, позволяющий быстро путь ExpressRoute. Эта функциональная возможность сокращает сетевую задержку между единиц крупных экземпляров HANA и виртуальных машин Azure. Функциональные возможности добавлены в мая 2019 г. Дополнительные сведения см. в статье [сетевой архитектуры SAP HANA (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture). Убедитесь в том, что вы используете последнюю версию командлетов PowerShell перед выполнением следующих команд.

Возможно, вам потребуется сделать это несколько раз, чтобы подключить шлюз к нескольким каналам ExpressRoute, связанным с вашей подпиской. Например, скорее всего, вам потребуется подключить тот же шлюз виртуальной сети к каналу ExpressRoute, с помощью которого виртуальная сеть подключается к локальной сети.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Применение ExpressRoute быстрый путь к существующей цепи ExpressRoute крупных экземпляров HANA
Документация в данный момент было рассмотрено, как для подключения канала ExpressRoute, созданный с помощью развертывания крупных экземпляров HANA, чтобы шлюз Azure ExpressRoute одного из виртуальным сетям Azure. Но многие клиенты уже уже их настройке каналов ExpressRoute и своих виртуальных сетях подключились к крупным экземплярам HANA уже. Новый ExpressRoute быстрый путь Уменьшение задержки в сети, рекомендуется применить изменение для использования этой функции. Команды для подключения нового канала ExpreesRoute и для изменения существующей цепи ExpressRoute одинаковы. В результате необходимо выполнить следующую последовательность команд PowerShell для изменения существующей цепи для использования 

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

Очень важно добавить последний параметр, показанной выше для включения функции ExpressRoute быстрый путь


## <a name="expressroute-global-reach"></a>ExpressRoute в мире
Как вы хотите включить глобальным доступом для одного или двух случаях:

 - Репликация системы HANA без дополнительные прокси-серверы или брандмауэры
 - Копирование резервных копий между единицами крупных экземпляров HANA в двух разных регионах для выполнения копии системы или обновления системы

Вам требуется внимание на следующее.

- Необходимо указать пространство диапазоном адресов/29 адресное пространство. Что диапазон адресов не могут перекрываться с любой из других диапазонов пространства адресов, которые вы использовали, пока подключение крупных экземпляров HANA в Azure и не могут перекрываться с любым из диапазонов IP-адрес можно использовать другое место в Azure или на предприятии.
- Нет ограничений на ASN (номер автономной системы), которые могут использоваться для объявления локальных маршрутов к крупным экземплярам HANA. В локальной не должны объявлять все маршруты с частные ASN в диапазоне от 65000 — 65020 или 65515. 
- Сценарии подключения локальной прямой доступ к крупных экземпляров HANA необходимо рассчитать сбор для канала, который позволяет подключиться к Azure. См. цены, цены на [глобальных надстроек достичь](https://azure.microsoft.com/pricing/details/expressroute/).

Чтобы получить одно или несколько сценариев применения для развертывания, откройте сообщение службы поддержки с помощью Azure, как описано в [откройте запрос в службу поддержки для крупных экземпляров HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)

Данные, которые необходимы и ключевые слова, которые необходимо использовать корпорации Майкрософт, чтобы иметь возможность маршрутизации и выполнения запроса, выглядит так:

- Служба: Крупный экземпляр SAP HANA
- Тип проблемы: Настройка и установка
- Подтип проблемы: Моя проблема отсутствует в списке.
- Тема «Изменение сети - добавить глобальным доступом»
- Сведения. "Добавить глобальным доступом к крупным экземплярам HANA в крупных экземпляров HANA клиент или" Добавить глобальным доступом к локальным для клиента крупных экземпляров HANA.
- Дополнительные сведения для крупного экземпляра HANA в крупных экземпляров HANA клиента регистр: Необходимо определить **двух регионах Azure** где находятся два клиентам возможность подключать **AND** необходимо отправить   **/29 диапазон IP-адресов**
- Дополнительные сведения о локальной в регистр клиента крупных экземпляров HANA: Необходимо определить **регионе Azure** там, где развертывается клиента крупных экземпляров HANA, который вы хотите подключиться напрямую. Кроме того, вам нужно предоставить **Auth GUID** и **канала идентификатор однорангового узла** ему о получении при установлении канала ExpressRoute между локальной сетью и Azure. Кроме того, необходимо присвоить имя вашей **ASN**. Последний результат — это **/29 диапазон IP-адресов** для ExpressRoute глобальным доступом.

> [!NOTE]
> Если требуется, чтобы обоих случаях обработано, необходимо предоставить два разных/29 диапазоны IP-адресов, которые не пересекаются с другими IP-адрес, диапазон адресов, используемых в данный момент. 




## <a name="next-steps"></a>Следующие шаги

- [Дополнительные требования к сети для крупных экземпляров HANA](hana-additional-network-requirements.md)
