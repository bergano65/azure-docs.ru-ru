---
title: Обновление виртуального WAN Azure с базового на стандартный - портал Azure Документы Майкрософт
description: Вы можете обновить виртуальный тип WAN для большей функциональности.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 83fd5bafb5496908403c50dc0e000fd33a836c95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515815"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>Обновление виртуального WAN от базового к стандартному

Эта статья поможет вам обновить базовый WAN до стандартного WAN. Тип WAN 'Basic' создает все концентраторы внутри него в качестве базовых концентраторов SKU. В базовом концентраторе вы ограничены только функциональностью VPN от сайта. Тип WAN 'Standard' создает все концентраторы внутри него в качестве стандартных узлов SKU. При использовании стандартных концентраторов можно включить VPN ExpressRoute, User (Point-to-site), полный концентратор сетки и транзит VNet-to-VNet через концентраторы Azure.

В следующей таблице показаны конфигурации, доступные для каждого типа WAN:

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>Изменение виртуального типа WAN

1. На странице для виртуального WAN выберите **Конфигурацию,** чтобы открыть страницу Конфигурации.

   ![Схема Виртуальной глобальной сети](./media/upgrade-virtual-wan/1.png)
2. Для виртуального типа WAN выберите **стандарт** из выпадения.

   ![Схема Виртуальной глобальной сети](./media/upgrade-virtual-wan/2.png)
3. Поймите, что если вы перейти на стандартный виртуальный WAN, вы не можете вернуться к базовому виртуальному WAN. Выберите **Подтвердите,** если вы хотите обновить.

   ![Схема Виртуальной глобальной сети](./media/upgrade-virtual-wan/4.png)
4. После того, как изменение было сохранено, ваша виртуальная страница WAN выглядит похоже йогамина на этот пример.

   ![Схема Виртуальной глобальной сети](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Виртуальной глобальной сети см. в [этой статье](virtual-wan-about.md).