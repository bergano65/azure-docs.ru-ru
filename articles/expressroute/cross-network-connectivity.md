---
title: Кросс-сетевое подключение Azure
description: На этой странице описывается сценарий приложения для перекрестного подключения к сети и решения, основанного на сетевых функциях Azure.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 48ec26cc98310dfeb61aa17018c940b431cfbcee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644262"
---
# <a name="cross-network-connectivity"></a>Возможность подключения нескольких сетей

Компания Fabrikam Inc. активно работает в восточной части США и имеет развернутые службы Azure в этом регионе. Fabrikam использует ExpressRoute для связи между серверными компонентами, размещенными в локальной сети и в Azure. Аналогичным образом, Contoso Ltd. имеет присутствие и развертывание Azure в Западной части США. Contoso использует ExpressRoute для связи между серверными компонентами, размещенными в локальной сети и в Azure.  

Fabrikam Inc. приобретает Contoso Ltd. После слияния Fabrikam хочет соединить сети. Этот сценарий показан на следующей схеме:

 [![1]][1 1]

Разбитые стрелки в середине вышеуказанного рисунка указывают на желаемые сетевые соединения. В частности, Есть три типа перекрестных соединений желаемого: 1) Fabrikam и Contoso VNets кросс-соединение, 2) Крест региональных на местах и VNets кросс соединяется (то есть, соединяя Fabrikam предпосылок с Contoso VNet и подключения Contoso на-припредствии сети Fabrikam VNet), и 3) Fabrikam и Contoso предприимчной сети кросс-соединения. 

В следующей таблице показана таблица маршрутов частного пиринга ExpressRoute of Contoso Ltd., до слияния.

[![2]][2]

В следующей таблице показаны эффективные маршруты ВМ в подписке Contoso, до слияния. В таблице VM на VNet знает адресное пространство VNet и локтевую сеть Contoso, помимо по умолчанию. 

[![4]][4 4]

В следующей таблице показана таблица маршрутов частного пиринга ExpressRoute компании Fabrikam Inc. до слияния.

[![3]][3]

В следующей таблице показаны эффективные маршруты ВМ в подписке Fabrikam, до слияния. В таблице VM на VNet известно о пространстве адреса VNet и предварительной сети Fabrikam, помимо по умолчанию.

[![5]][5]

В этой статье давайте поэтапно рассмотрим, как достичь желаемого перекрестного соединения, используя следующие функции сети Azure:

* [Виртуальная сеть пиринга][Virtual network peering] 
* [Виртуальное подключение к сети ExpressRoute][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>Крестопомый VNets

Виртуальная сеть пиринга (VNet пиринг) обеспечивает наиболее оптимальную и лучшую производительность сети при подключении двух виртуальных сетей. Вонючейская поддержка VNet поддерживает внеся два VNets как в одном и том же регионе Azure (обычно называемом VNet peering), так и в двух разных регионах Azure (обычно называемых Global VNet). 

Давайте наладим Global VNet между VNets в подписках Contoso и Fabrikam Azure. О том, как создать виртуальную сеть, вглядывающуюся между двумя виртуальными сетями, можно [узнать: «Создайте виртуальную статью для пиринга сети.][Configure VNet peering]

Следующая картинка показывает архитектуру сети после настройки вращения Global VNet.

[![6]][6]

В следующей таблице показаны маршруты, известные подписке Contoso VM. Обратите внимание на последнюю запись таблицы. Эта запись является результатом перекрестного подключения виртуальных сетей.

[![7]][7]

В следующей таблице показаны маршруты, известные подписке Fabrikam VM. Обратите внимание на последнюю запись таблицы. Эта запись является результатом перекрестного подключения виртуальных сетей.

[![8]][8]

VNet пиринг непосредственно ссылки двух виртуальных сетей (см. Есть нет следующего перехода для *VNetGlobalPeering* запись в двух вышеуказанных таблицах)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Кросс-соединение VNets с сетевыми сетями

Мы можем подключить схему ExpressRoute к нескольким виртуальным сетям. Просмотрите [ограничения подписки и обслуживания][Subscription limits] для максимального количества виртуальных сетей, которые могут быть подключены к схеме ExpressRoute. 

Давайте подключим схему Fabrikam ExpressRoute к подписке Contoso VNet и аналогично схеме Contoso ExpressRoute с подпиской Fabrikam VNet, чтобы обеспечить перекрестное подключение между виртуальными сетями и сетевыми сетями. Чтобы подключить виртуальную сеть к схеме ExpressRoute в другой подписке, нам необходимо создать и использовать авторизацию.  Смотрите статью: [Подключите виртуальную сеть к схеме ExpressRoute][Connect-ER-VNet].

На следующем рисунке показана архитектура сети после настройки кросс-соединения ExpressRoute с виртуальными сетями.

[![9]][9]

В следующей таблице показана таблица маршрутов частного пиринга ExpressRoute of Contoso Ltd., после перекрестного подключения виртуальных сетей к закрытым сетям через ExpressRoute. Обратите, что в таблице маршрутов есть маршруты, принадлежащие обеим виртуальным сетям.

[![10]][10]

В следующей таблице показана таблица маршрутов частного пиринга ExpressRoute of Fabrikam Inc., после перекрестного подключения виртуальных сетей к закрытым сетям через ExpressRoute. Обратите, что в таблице маршрутов есть маршруты, принадлежащие обеим виртуальным сетям.

[![11]][11]

В следующей таблице показаны маршруты, известные подписке Contoso VM. Обратите внимание на *виртуальные сетевые шлюзы* записей таблицы. ВМ видит маршруты для обеих сетей.

[![12]][12]

В следующей таблице показаны маршруты, известные подписке Fabrikam VM. Обратите внимание на *виртуальные сетевые шлюзы* записей таблицы. ВМ видит маршруты для обеих сетей.

[![13]][13]

>[!NOTE]
>В подписках Fabrikam and/or Contoso вы также можете говорить vNets с соответствующим концентратором VNet (дизайн концентра и говорил не иллюстрируется в диаграммах архитектуры в этой статье). Перекрестное соединение между шлюзами концентраторv VNet к ExpressRoute также позволит связь между востоком и Западом узлов и спицы.
>

## <a name="cross-connecting-on-premises-networks"></a>Кросс-соединение натерритории сетей

ExpressRoute Global Reach обеспечивает подключение между присоединенными сетями, подключенными к различным схемам ExpressRoute. Давайте направим Global Reach между схемами Contoso и Fabrikam ExpressRoute. Поскольку схемы ExpressRoute находятся в разных подписках, нам необходимо создать и использовать авторизацию. Подробнее о руководстве для [настройки ExpressRoute Global Reach][Configure Global Reach] читайте в материале журнала "Огонек" "Шаг за шагом".

На следующем рисунке показана архитектура сети после настройки Global Reach.

[![14]][14]

В следующей таблице показана таблица маршрутов частного пиринга ExpressRoute of Contoso Ltd., после настройки Global Reach. Обратите, что в таблице маршрутов есть маршруты, принадлежащие обеим входящим сетям. 

[![15]][15]

В следующей таблице показана таблица маршрутов частного пиринга ExpressRoute of Fabrikam Inc., после настройки Global Reach. Обратите, что в таблице маршрутов есть маршруты, принадлежащие обеим входящим сетям.

[![16]][16]

## <a name="next-steps"></a>Дальнейшие действия

Смотрите [виртуальные задаваемые вопросы сети,][VNet-FAQ]для любых дальнейших вопросов о VNet и VNet-пранинга. Дополнительные вопросы о ExpressRoute и виртуальном подключении к сети можно посмотреть в [вопросах ExpressRoute.][ER-FAQ]

Глобальный охват внедряется по стране/региону по странам/регионам. Чтобы узнать, доступен ли Global Reach в [ExpressRoute Global Reach][Global Reach]нужных странах/регионах, см.

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "Сценарий приложения"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "Таблица маршрута Contoso ExpressRoute перед слиянием"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "Таблица маршрутов Fabrikam ExpressRoute до слияния"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "Маршруты Contoso VM до слияния"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "маршрутов Fabrikam VM до слияния"
[6]: ./media/cross-network-connectivity/vnet-peering.png "Архитектура после VNet-пиринга"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "Маршруты Contoso VM после вглядывания VNet"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "маршрутов Fabrikam VM после вглядывания VNet"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "Архитектура после перекрестного соединения ExpressRoutes"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "Таблица маршрутов Contoso ExpressRoute после перекрестного соединения ExR и VNets"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "Фабрикам ЭкспрессРут маршруттаблицпосле после перекрестного соединения ExR и VNets"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "маршрутов Contoso VM после перекрестного соединения ExR и VNets"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "маршрутов Fabrikam VM после пересечения ExR и VNets"
[14]: ./media/cross-network-connectivity/globalreach.png "Архитектура после настройки Глобального Охвата"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "Таблица маршрутов Contoso ExpressRoute после Глобального Охвата"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "Таблица маршрутов Fabrikam ExpressRoute после Глобального Охвата"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq