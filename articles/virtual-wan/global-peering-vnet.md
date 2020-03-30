---
title: Направляй глобальную внедренную систему VNet для виртуального WAN Azure (ru) Документы Майкрософт
description: Подключите VNet в другом регионе к виртуальному концентратору WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 340472f84d2dd2c4f46d180992745a57e8ad1884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73588230"
---
# <a name="configure-global-vnet-peering-cross-region-vnet-for-virtual-wan"></a>Нанастройка глобального внедрального внебрачного (кросс-региона VNet) для виртуального WAN

Вы можете подключить VNet в другом регионе к виртуальному концентратору WAN.

## <a name="before-you-begin"></a>Перед началом

Убедитесь, что вы выполнили следующие критерии:

* Кросс-регион VNet (смена) не подключен к другому виртуальному концентратору WAN. Спина может быть подключена только к одному виртуальному узлу.
* VNet (говорит) не содержит виртуального сетевого шлюза (например, azure VPN Gateway или виртуальный сетевой шлюз ExpressRoute). Если VNet содержит виртуальный сетевой шлюз, необходимо удалить шлюз перед подключением спица VNet к концентратору.

## <a name="register-this-feature"></a><a name="register"></a>Регистрация этой возможности

Вы можете зарегистрироваться для этой функции с помощью PowerShell. Если вы выберете "Try It" из приведенного ниже примера, Azure Cloud-Shell откроется, и вам не нужно будет устанавливать cmdlets PowerShell локально на ваш компьютер. При необходимости можно изменить подписку с помощью cmdlet 'Select-AzSubscription-SubscriptionId'. <subid>

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Network'
```

## <a name="verify-registration"></a><a name="verify"></a>Проверка регистрации

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
```

## <a name="connect-a-vnet-to-the-hub"></a><a name="hub"></a>Подключение VNet к концентратору

На этом этапе создается вpeering соединение между концентратором и кросс-регионом VNet. Повторите эти шаги для каждой виртуальной сети, которую вы хотите подключить.

1. На странице своей глобальной сети щелкните **Подключения к виртуальной сети**.
2. На странице подключения к виртуальной сети щелкните **+Добавить подключение**.
3. На странице **Добавление подключения** заполните следующие поля.

    * **Имя подключения**. Укажите имя своего подключения.
    * **Концентраторы**. Выберите концентратор, который нужно связать с этим подключением.
    * **Подписка**. Проверьте подписку.
    * **Виртуальная сеть**. Выберите виртуальную сеть, которую вы хотите подключить к этому концентратору. В виртуальной сети не должно быть шлюза виртуальной сети.
4. Нажмите кнопку **ОК**, чтобы создать пиринговое подключение.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о виртуальном WAN, смотрите [виртуальный обзор WAN](virtual-wan-about.md).