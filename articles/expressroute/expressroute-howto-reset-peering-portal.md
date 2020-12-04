---
title: 'Azure ExpressRoute: сброс пиринга цепи с помощью портал Azure'
description: Узнайте, как отключить и включить пирингы канала Azure ExpressRoute с помощью портал Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: d4d6b0b0cce4f5304f7c5790ef2bda05633be52f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582989"
---
# <a name="reset-expressroute-circuit-peerings-use-the-azure-portal"></a>Сброс пиринга канала ExpressRoute используйте портал Azure

В этой статье описывается, как отключить и включить пирингы канала ExpressRoute с помощью портал Azure. При отключении пиринга сеанс BGP для основного и дополнительного подключения канала ExpressRoute будет закрыт. Связь с этим пирингом с корпорацией Майкрософт будет потеряна. При включении пиринга будет выведен сеанс BGP как на основном, так и в дополнительном подключении канала ExpressRoute. Вы снова получите возможность подключения через этот пиринг к Майкрософт. В цепи ExpressRoute можно индивидуально отключать и включать как пиринг Майкрософт, так и частный пиринг Azure. При первой настройке пиринга в канале ExpressRoute пиринг по умолчанию включен.

Существует несколько сценариев, в которых сброс пиринга для ExpressRoute может оказаться полезным.
* Протестируйте проектирование и реализацию аварийного восстановления. Например, существует две цепи ExpressRoute. Пиринг в первой цепи можно отключить и принудительно перевести весь сетевой трафик на вторую цепь.
* Включите обнаружение двунаправленной переадресации (БФД) в частном пиринга Azure или пиринга Майкрософт для канала ExpressRoute. БФД по умолчанию включается в частном пиринга Azure, если канал ExpressRoute создается после 1 2018 августа и пиринга Майкрософт. Если канал ExpressRoute создается после 10 2020 января. Если цепь была создана ранее приведенной даты, BFD включено не было. BFD можно включить путем отключения и повторного включения пиринга. 

### <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

В браузере откройте [портал Azure](https://portal.azure.com) и выполните вход с помощью учетной записи Azure.

## <a name="reset-a-peering"></a>Сброс пиринга

1. Выберите канал, для которого необходимо внести изменения в конфигурацию пиринга.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="Список каналов ExpressRoute":::

1. Выберите конфигурацию пиринга, которую необходимо включить или отключить.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="Обзор канала ExpressRoute":::

1. Снимите флажок **включить пиринг** и нажмите кнопку **сохранить** , чтобы отключить конфигурацию пиринга.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="Отключить частный пиринг":::

1. Вы можете снова включить пиринг, установив флажок **включить пиринг** и нажав кнопку **сохранить**.

## <a name="next-steps"></a>Дальнейшие действия
Если вам нужна помощь с устранением неполадок ExpressRoute, ознакомьтесь со следующими статьями:
* [Проверка подключения ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Устранение проблем с производительностью сети](expressroute-troubleshooting-network-performance.md)
