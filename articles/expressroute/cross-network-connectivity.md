---
title: Подключение между сетями в Azure
description: На этой странице описывается сценарий приложения для подключения между сетями и решения на основе сетевых компонентов Azure.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: e503dc2b4ae8773ebfedc7a9b73bc5ea93dd9d5a
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076748"
---
# <a name="cross-network-connectivity"></a>Возможность подключения нескольких сетей

Компания Fabrikam Inc. активно работает в восточной части США и имеет развернутые службы Azure в этом регионе. Fabrikam использует ExpressRoute для связи между серверными компонентами, размещенными в локальной сети и в Azure. Аналогично, Contoso Ltd. имеет присутствие и развертывание Azure в западной части США. Contoso использует ExpressRoute для связи между серверными компонентами, размещенными в локальной сети и в Azure.  

Fabrikam Inc. получает Contoso Ltd. После слияния компания Fabrikam хочет присоединиться к сетям. Этот сценарий показан на следующей схеме:

 [![1]][1]

Пунктирные стрелки в середине приведенного выше рисунка указывают на требуемые сетевые соединения. В частности, существует три типа, для которых требуется перекрестное подключение: 1) Fabrikam и Contoso виртуальных сетей Cross Connect, 2) кросс-региональный локальный и виртуальных сетей перекрестное подключение (т. е. подключение локальной сети Fabrikam к виртуальной сети Contoso и подключение Contoso) Локальная сеть для Fabrikam VNet) и 3) Fabrikam и локальная сеть Contoso Cross Connect. 

В следующей таблице показана таблица маршрутов частного пиринга ExpressRoute компании Contoso Ltd. до слияния.

[![2]][2]

В следующей таблице показаны действующие маршруты виртуальной машины в подписке Contoso до слияния. В каждой таблице виртуальная машина в виртуальной сети учитывает адресное пространство виртуальной сети и локальную сеть Contoso, помимо используемых по умолчанию. 

[![4]][4]

В следующей таблице показана таблица маршрутов частного пиринга ExpressRoute компании Fabrikam Inc. до слияния.

[![3]][3]

В следующей таблице показаны действующие маршруты виртуальной машины в подписке Fabrikam до слияния. В каждой таблице виртуальная машина в виртуальной сети учитывает адресное пространство виртуальной сети и локальную сеть Fabrikam, помимо значений по умолчанию.

[![5]][5]

В этой статье мы рассмотрим пошаговые инструкции и расскажем, как добиться нужных перекрестных подключений с помощью следующих компонентов сети Azure:

* [Пиринг между виртуальными сетями][Virtual network peering] 
* [Подключение ExpressRoute виртуальной сети][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>Перекрестное подключение виртуальных сетей

Пиринг виртуальных сетей (пиринг виртуальной сети) обеспечивает наиболее оптимальную и оптимальную производительность сети при подключении двух виртуальных сетей. Пиринг виртуальных сетей поддерживает пиринг двух виртуальных сетей как в одном регионе Azure (называемом пирингом виртуальных сетей), так и в двух разных регионах Azure (обычно именуемых глобальным пирингом виртуальных сетей). 

Давайте настроим глобальную пиринг виртуальной сети между виртуальных сетей в подписках Contoso и Fabrikam Azure. Сведения о создании пиринга между двумя виртуальными сетями см. в статье [Создание пиринга виртуальной сети][Configure VNet peering] .

На следующем рисунке показана сетевая архитектура после настройки пиринга глобальной виртуальной сети.

[![6]][6]

В следующей таблице показаны маршруты, известные виртуальной машине подписки contoso. Обратите внимание на последнюю запись таблицы. Эта запись является результатом перекрестного подключения виртуальных сетей.

[![7]][7]

В следующей таблице показаны маршруты, известные для виртуальной машины подписки Fabrikam. Обратите внимание на последнюю запись таблицы. Эта запись является результатом перекрестного подключения виртуальных сетей.

[![8]][8]

Пиринг виртуальных сетей напрямую связывает две виртуальные сети (см. следующий прыжок для записи *внетглобалпиринг* в двух приведенных выше таблицах)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Перекрестное подключение виртуальных сетей к локальным сетям

Мы можем подключить канал ExpressRoute к нескольким виртуальным сетям. См. раздел [ограничения подписки и службы][Subscription limits] для максимального числа виртуальных сетей, которые могут быть подключены к каналу ExpressRoute. 

Давайте подключим канал Fabrikam ExpressRoute к виртуальной сети подписки Contoso и аналогичному каналу Contoso ExpressRoute в виртуальную сеть подписки Fabrikam, чтобы обеспечить перекрестное подключение между виртуальными сетями и локальными сетями. Чтобы подключить виртуальную сеть к каналу ExpressRoute в другой подписке, необходимо создать и использовать авторизацию.  См. статью: [подключение виртуальной сети к каналу ExpressRoute][Connect-ER-VNet].

На следующем рисунке показана сетевая архитектура после настройки перекрестного подключения ExpressRoute к виртуальным сетям.

[![8]][9]

В следующей таблице показана таблица маршрутов частного пиринга ExpressRoute компании Contoso Ltd. после перекрестного подключения виртуальных сетей к локальным сетям через ExpressRoute. Проверьте, что таблица маршрутов имеет маршруты, принадлежащие обеим виртуальным сетям.

[![штук]][10]

В следующей таблице показана таблица маршрутов частного пиринга ExpressRoute компании Fabrikam Inc. после перекрестного подключения виртуальных сетей к локальным сетям через ExpressRoute. Проверьте, что таблица маршрутов имеет маршруты, принадлежащие обеим виртуальным сетям.

[![стр]][11]

В следующей таблице показаны маршруты, известные виртуальной машине подписки contoso. Обратите внимание на записи *шлюза виртуальной сети* таблицы. Виртуальная машина видит маршруты для локальных сетей.

[![Двенадцать]][12]

В следующей таблице показаны маршруты, известные для виртуальной машины подписки Fabrikam. Обратите внимание на записи *шлюза виртуальной сети* таблицы. Виртуальная машина видит маршруты для локальных сетей.

[![решением]][13]

>[!NOTE]
>В подписках Fabrikam и/или Contoso также можно иметь периферийную виртуальных сетей в соответствующую виртуальную сеть концентратора (в схемах архитектуры в этой статье не проиллюстрирована конструкция HUB и лучевой конструкции). Перекрестные подключения между шлюзами виртуальной сети концентратора и ExpressRoute также разрешают обмен данными между налево и западными центрами и периферийными серверами.
>

## <a name="cross-connecting-on-premises-networks"></a>Перекрестное подключение локальных сетей

ExpressRoute Global Reach обеспечивает подключение между локальными сетями, подключенными к разным каналам ExpressRoute. Давайте настроим Global Reach между каналами Contoso и Fabrikam ExpressRoute. Так как каналы ExpressRoute находятся в разных подписках, необходимо создать и использовать авторизацию. Пошаговые инструкции см. в статье [настройка Global REACH ExpressRoute][Configure Global Reach] .

На следующем рисунке показана сетевая архитектура после настройки Global Reach.

[![открыт]][14]

В следующей таблице показана таблица маршрутов частного пиринга ExpressRoute компании Contoso Ltd. После настройки Global Reach. Проверьте, что таблица маршрутов имеет маршруты, принадлежащие обеим локальным сетям. 

[![15]][15]

В следующей таблице показана таблица маршрутов частного пиринга ExpressRoute компании Fabrikam Inc. После настройки Global Reach. Проверьте, что таблица маршрутов имеет маршруты, принадлежащие обеим локальным сетям.

[![глубин]][16]

## <a name="next-steps"></a>Дополнительная информация

Дополнительные вопросы по виртуальной сети и пирингу виртуальных сетей см. в разделе [часто ЗАдаваемые вопросы о виртуальном сетевом][VNet-FAQ]уровне. Дополнительные вопросы об ExpressRoute и подключении к виртуальной сети см. в разделе [вопросы и ответы по expressroute][ER-FAQ] .

Global Reach размещается в стране или регионе по странам или регионам. Чтобы узнать, доступны ли Global Reach в нужных странах или регионах, см. раздел [ExpressRoute Global REACH][Global Reach].

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "Сценарий приложения"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "Таблица маршрутов для Contoso ExpressRoute перед слиянием"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "Таблица маршрутов компании Fabrikam ExpressRoute перед слиянием"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "маршрута виртуальных машин Contoso до слияния"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "маршрутов виртуальных машин Fabrikam до слияния"
[6]: ./media/cross-network-connectivity/vnet-peering.png "архитектура после пиринга виртуальной сети"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "маршрутов виртуальных машин Contoso после пиринга виртуальной сети"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "маршрутов виртуальных машин Fabrikam после пиринга виртуальной сети"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "архитектура после которыми перекрестного подключения"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png. "Таблица маршрутов Contoso ExpressRoute после перекрестного подключения екср и виртуальных сетей"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png. "Таблица маршрутов компании Fabrikam ExpressRoute после перекрестного подключения екср и виртуальных сетей"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "маршрутов виртуальных машин Contoso после перекрестного подключения екср и виртуальных сетей"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "маршрутов виртуальных машин Fabrikam после перекрестного подключения екср и виртуальных сетей"
[14]: ./media/cross-network-connectivity/globalreach.png. "архитектура после настройки Global REACH"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "Таблица маршрутов для Contoso ExpressRoute после Global REACH"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png. "Таблица маршрутов "Fabrikam ExpressRoute" после Global REACH"

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