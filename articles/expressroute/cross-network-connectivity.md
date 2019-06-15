---
title: Подключения к сети между Azure | Документация Майкрософт
description: Эта страница описывает сценарий приложения для кросс-подключения к сети и решение, основанное на сетевыми компонентами Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 3bc189cf269084fdb26f141a36755c96554cad7b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64866003"
---
# <a name="cross-network-connectivity"></a>Возможность подключения нескольких сетей

Компания Fabrikam Inc. активно работает в восточной части США и имеет развернутые службы Azure в этом регионе. Fabrikam использует ExpressRoute для связи между серверными компонентами, размещенными в локальной сети и в Azure. Аналогичным образом Contoso Ltd. обладает присутствия и развертывания Azure в западной части США. Contoso использует ExpressRoute для связи между серверными компонентами, размещенными в локальной сети и в Azure.  

Компания Fabrikam Inc. приобретает компанию Contoso Ltd. После этого слияния компания Fabrikam намерена объединить обе сети. Этот сценарий показан на следующей схеме:

 [![1]][1]

Пунктирные стрелки середине рисунке выше обозначают соединения нужную сеть. В частности существует три типа, требуемого кросс-подключений: Fabrikam и Contoso виртуальных сетей, кросс-подключение (1), 2) кросс-региональные в локальной сети и виртуальные сети между подключается (то есть, подключение к виртуальной сети Contoso Fabrikam в локальной сети и подключение к виртуальной сети Fabrikam Contoso в локальной сети) и (3) Fabrikam и Contoso в локальной сети, кросс-подключения. 

В следующей таблице показаны таблицу маршрутов для частного пиринга ExpressRoute из Contoso Ltd., перед слиянием.

[![2]][2]

В следующей таблице показаны фактические маршруты виртуальной машины в подписке Contoso, перед слиянием. В таблице виртуальной Машины в виртуальной сети учитывает адресное пространство виртуальной сети и в локальной сети Contoso, помимо тех, которые по умолчанию. 

[![4]][4]

В следующей таблице показаны таблицу маршрутов для частного пиринга ExpressRoute из Fabrikam Inc., перед слиянием.

[![3]][3]

В следующей таблице показаны фактические маршруты виртуальной машины в подписке Fabrikam, перед слиянием. В таблице виртуальной Машины в виртуальной сети учитывает адресное пространство виртуальной сети и локальной сетью компании Fabrikam, помимо тех, которые по умолчанию.

[![5]][5]

В этой статье мы разберем шаг за шагом и рассматриваются способы достижения нужного кросс-подключений с помощью следующих компонентов сети Azure:

* [Пиринг между виртуальными сетями][Virtual network peering] 
* [Подключение ExpressRoute виртуальной сети][connection]
* [В мире][Global Reach] 

## <a name="cross-connecting-vnets"></a>Кросс-подключение виртуальных сетей

Пиринг виртуальных сетей (пиринг) предоставляет наиболее оптимальной и самую высокую производительность сети, при подключении двух виртуальных сетей. Пиринг виртуальной сети поддерживает пиринг двумя виртуальными сетями в одном регионе Azure (обычно его называют пиринг виртуальной сети) и в двух разных регионах Azure (обычно его называют пиринг глобальной виртуальной сети). 

Давайте настроим глобальный Пиринг между виртуальными сетями в подписки Contoso и Fabrikam Azure. Способы создания виртуальной сети, пиринг между двумя виртуальными сетями, см. в разделе [Пиринга виртуальных сетей] [ Configure VNet peering] статьи.

На следующем рисунке показаны архитектуры сети, настроив пиринг глобальной виртуальной сети.

[![6]][6]

В следующей таблице показаны маршруты, известные в подписке Contoso виртуальной Машины. Обратите внимание на последний элемент таблицы. Эта запись является результатом кросс-подключение виртуальных сетей.

[![7]][7]

В следующей таблице показаны маршруты, известные в Fabrikam подписку виртуальной Машины. Обратите внимание на последний элемент таблицы. Эта запись является результатом кросс-подключение виртуальных сетей.

[![8]][8]

Непосредственно пиринговой связи две виртуальные сети (см. в разделе есть не следующий прыжок для *VNetGlobalPeering* записи в таблицах выше)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Кросс-подключение виртуальных сетей к локальным сетям

Канал ExpressRoute можно будет подключить к несколько виртуальных сетей. См. в разделе [ограничения подписки и службы] [ Subscription limits] количество виртуальных сетей, которые могут быть подключены к каналу ExpressRoute. 

Подключимся канал Fabrikam ExpressRoute и Contoso подписки виртуальной сети и аналогично каналу ExpressRoute компании Contoso, Fabrikam подписки виртуальной сети для обеспечения перекрестного подключения между виртуальными сетями и локальными сетями. Для подключения виртуальной сети к каналу ExpressRoute в другой подписке, необходимо создать и использовать авторизацию.  См. в статье: [Подключение виртуальной сети к каналу ExpressRoute][Connect-ER-VNet].

На следующем рисунке показаны архитектуры сети после настройки ExpressRoute кросс-подключения к виртуальным сетям.

[![9]][9]

В следующей таблице показаны в таблицу маршрутов частный пиринг ExpressRoute из Contoso Ltd., после кросс-подключение виртуальных сетей к локальным сетям через ExpressRoute. См. в разделе, что в таблице маршрутизации указан маршруты, принадлежащих обеих виртуальных сетей.

[![10]][10]

В следующей таблице показаны таблицу маршрутов для частного пиринга ExpressRoute для корпорации Fabrikam Inc., после кросс-подключение виртуальных сетей к локальным сетям через ExpressRoute. См. в разделе, что в таблице маршрутизации указан маршруты, принадлежащих обеих виртуальных сетей.

[![11]][11]

В следующей таблице показаны маршруты, известные в подписке Contoso виртуальной Машины. Обратите внимание на *шлюз виртуальной сети* записей таблицы. Виртуальная машина видит маршруты для локальных сетей.

[![12]][12]

В следующей таблице показаны маршруты, известные в Fabrikam подписку виртуальной Машины. Обратите внимание на *шлюз виртуальной сети* записей таблицы. Виртуальная машина видит маршруты для локальных сетей.

[![13]][13]

>[!NOTE]
>В любом Fabrikam и Contoso подписки, которые также могут виртуальные сети периферийных зон в соответствующих концентратор виртуальной сети (звездообразной разработки не проиллюстрирован в схемах архитектуры, в этой статье). Кросс-подключений между шлюзами виртуальной сети концентратора для ExpressRoute также поддерживают обмен данными между Восток и запад концентраторов и периферийных зон.
>

## <a name="cross-connecting-on-premises-networks"></a>Кросс-подключения для локальных сетей

Глобальным доступом ExpressRoute обеспечивает обмен данными между локальными сетями, которые подключены к разным каналам ExpressRoute. Давайте настроим глобальным доступом между Contoso и Fabrikam ExpressRoute каналов. Поскольку каналы ExpressRoute в разных подписках, нам нужно создать и использовать авторизацию. См. в разделе [Настройка глобальным доступом ExpressRoute] [ Configure Global Reach] статьи в параметре Пошаговое руководство.

На следующем рисунке показаны архитектуры сети после настройки глобальным доступом.

[![14]][14]

В следующей таблице показаны таблицу маршрутов для частного пиринга ExpressRoute из Contoso Ltd., настроив глобальным доступом. См. в разделе, что в таблице маршрутизации указан маршруты, относящихся к локальным сетям. 

[![15]][15]

В следующей таблице показаны таблицу маршрутов для частного пиринга ExpressRoute из Fabrikam Inc., настроив глобальным доступом. См. в разделе, что в таблице маршрутизации указан маршруты, относящихся к локальным сетям.

[![16]][16]

## <a name="next-steps"></a>Дальнейшие действия

См. в разделе [вопросы по виртуальным сетям][VNet-FAQ]для дальнейшего любым вопросам о виртуальной сети и пиринг виртуальной сети. См. в разделе [вопросы и ответы по ExpressRoute] [ ER-FAQ] для каких-либо вопросов на ExpressRoute и виртуальных сетевых подключений.

В мире реализована на основе страны или региона, страны или региона. Если глобальным доступом доступен в странах и регионах, которые должны см. в разделе [глобальным доступом ExpressRoute][Global Reach].

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "Сценарий приложения"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "таблицы маршрутов ExpressRoute компании Contoso, прежде чем средство слияния"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "таблицы маршрутов Fabrikam ExpressRoute перед слиянием"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "виртуальной Машины Contoso направляет перед слиянием"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "виртуальной Машине Fabrikam направляет перед слиянием"
[6]: ./media/cross-network-connectivity/vnet-peering.png "архитектуры после пиринга виртуальной сети"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "виртуальной Машины Contoso направляет после пиринга виртуальной сети"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "виртуальной Машине Fabrikam направляет после пиринга виртуальной сети"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "архитектуры после каналы Expressroute перекрестного подключения"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "таблицы маршрутов Contoso ExpressRoute после подключения ExR "и" виртуальные сети"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "таблицы маршрутов Fabrikam ExpressRoute после подключения ExR "и" виртуальные сети"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "маршруты виртуальной Машины Contoso после Перекрестные подключения ExR и виртуальных сетей"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "маршруты на виртуальной Машине Fabrikam после Перекрестные подключения ExR и виртуальных сетей"
[14]: ./media/cross-network-connectivity/globalreach.png "архитектуры после настройки глобальным доступом"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "таблицы маршрутов Contoso ExpressRoute после глобальным доступом"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "таблицы маршрутов Fabrikam ExpressRoute после глобальным доступом"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq