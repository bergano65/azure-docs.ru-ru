---
title: 'Azure ExpressRoute: подключение к Microsoft Cloud с помощью Global Reach'
description: Узнайте, как Azure ExpressRoute Global Reach может связать каналы ExpressRoute вместе, чтобы создать частную сеть между локальными сетями.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: duau
ms.openlocfilehash: 8c6ed19a585bc1ebae65045cd1cc4c442f113597
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89651502"
---
# <a name="expressroute-global-reach"></a>Global Reach ExpressRoute
ExpressRoute — это частный и устойчивый способ подключения локальных сетей к Microsoft Cloud. Вы можете получить доступ ко многим облачным службам Майкрософт, таким как Azure и Microsoft 365, из частного центра обработки данных или корпоративной сети. Например, у вас есть филиал в Сан-Франциско с каналом ExpressRoute в Кремниевой долине и другой филиал в Лондоне с каналом ExpressRoute в том же городе. Оба филиала имеют высокоскоростное подключение к ресурсам Azure в Западная часть США и южная часть Соединенного Королевства. Однако офисы филиалов не могут подключаться и обмениваться данными напрямую друг с другом. Иными словами, 10.0.1.0/24 может передавать данные в сеть 10.0.3.0/24 и 10.0.4.0/24, но не в 10.0.2.0/24 сети.

![Схема, показывающая цепи, не связанные вместе с выпуском Express Route Global Reach.][1]

С помощью **ExpressRoute Global Reach** можно связать каналы ExpressRoute, чтобы настроить частную сеть между локальными сетями. В приведенном выше примере с добавлением ExpressRoute Global Reach офис в Сан-Франциско (10.0.1.0/24) может напрямую обмениваться данными с офисом в Лондоне (10.0.2.0/24) через имеющиеся каналы ExpressRoute и через глобальную сеть Майкрософт. 

![Схема, на которой показаны каналы, связанные вместе с Express Route Global Reach.][2]

## <a name="use-case"></a>Вариант использования
ExpressRoute Global Reach предназначен для дополнения реализации поставщика услуг глобальной сети и подключения ваших филиалов по всему миру. Например, если ваш поставщик услуг в основном работает в США и связал все филиалы в этой стране, но он не работает в Японии и Гонконге, то с помощью ExpressRoute Global Reach осуществляется работа с локальным поставщиком службы, а корпорация Майкрософт подключит филиалы к представленным в США, с помощью ExpressRoute и всемирной сети.

![Схема, в которой показан вариант использования для Global Reach Express Route.][3]

## <a name="availability"></a>Доступность 
ExpressRoute Global Reach поддерживается в большинстве регионов, в которых в настоящее время поддерживается ExpressRoute. Для текущих поддерживаемых регионов можно обратиться к [поставщикам услуг подключения ExpressRoute](expressroute-locations-providers.md#partners) . 

> [!NOTE] 
> Чтобы включить ExpressRoute Global Reach между [различными регионами](expressroute-locations-providers.md#locations)гео-ческих регионов, эти каналы должны иметь **номер SKU**уровня "Премиум".

## <a name="next-steps"></a>Дальнейшие действия
- Просмотрите [Global REACH часто задаваемые вопросы](expressroute-faqs.md#globalreach).
- Узнайте, как [включить Global REACH](expressroute-howto-set-global-reach.md).
- Узнайте, как [связать канал ExpressRoute с виртуальной сетью](expressroute-howto-linkvnet-arm.md).

<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "схема без Global Reach"
[2]: ./media/expressroute-global-reach/2.png "схема с Global Reach"
[3]: ./media/expressroute-global-reach/3.png "вариант использования глобального доступа"
