---
title: Об обновлении пропускной способности канала | Azure ExpressRoute
description: В этой статье приведены рекомендации по обновлению пропускной способности канала ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: cherylmc
ms.openlocfilehash: 9f44cbd9b735839640702970a65de239b36b3f30
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209808"
---
# <a name="about-upgrading-expressroute-circuit-bandwidth"></a>Об обновлении пропускной способности канала ExpressRoute

ExpressRoute обеспечивает выделенное и частное подключение к глобальной сети корпорации Майкрософт. Подключение обеспечивается сетью партнера ExpressRoute или прямым подключением к устройствам Microsoft Enterprise (MSEE). После настройки и тестирования физического подключения можно включить подключение уровня 2 и уровня 3, создав канал ExpressRoute и настроив пиринг.

## <a name="upgrade-circuit-bandwidth"></a><a name="upgrade"></a>Пропускная способность канала обновления

Чтобы обновить пропускную способность канала, партнеру ExpressRoute Direct или ExpressRoute необходимо иметь [достаточную пропускную способность](#considerations) для успешности обновления.

Если доступна емкость, можно обновить цепь с помощью следующих методов.

* [Портал Azure](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [PowerShell](expressroute-howto-circuit-arm.md#modify)
* [Azure CLI](howto-circuit-cli.md#modify)

## <a name="capacity-considerations"></a><a name="considerations"></a>Рекомендации по емкости

### <a name="insufficient-expressroute-partner-bandwidth"></a><a name="bandwidth"></a>Недостаточная пропускная способность партнера ExpressRoute

Если у партнера ExpressRoute недостаточно ресурсов, необходимо создать новый канал, настроенный для требуемой пропускной способности. Чтобы поддерживать подключение, не удаляйте старый канал до подготовки вновь созданного канала, настройки пиринга и (в отношении частного пиринга), что был подготовлен объект подключения к шлюзу виртуальной сети ExpressRoute.

Если у вашего партнера ExpressRoute недостаточно доступной емкости, необходимо запросить дополнительную емкость в нужном месте пиринга. После подготовки новой емкости можно выполнить действия, описанные в статьях в разделе [пропускная способность канала обновления](#upgrade) , чтобы создать новый канал, настроить подключение и удалить старый канал:


### <a name="insufficient-expressroute-direct-bandwidth"></a><a name="bandwidth"></a>Недостаточная прямая пропускная способность ExpressRoute

Если для ExpressRoute Direct не хватает емкости, можно либо удалить каналы, связанные с прямым ресурсом ExpressRoute, которые больше не нужны, либо создать новый прямой ресурс ExpressRoute. Инструкции по управлению прямым ресурсом ExpressRoute см. в разделе [Настройка Direct для](how-to-expressroute-direct-portal.md)канала expressroute.

## <a name="next-steps"></a>Дальнейшие действия

* [Создание и изменение канала](expressroute-howto-circuit-portal-resource-manager.md)
* [Создание и изменение конфигурации пиринга](expressroute-howto-routing-portal-resource-manager.md)
* [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
