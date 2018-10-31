---
title: Взаимодействие между ExpressRoute, VPN-подключением типа "сеть — сеть" и пирингом виртуальных сетей. Анализ на уровне управления для взаимодействия компонентов подключения к серверной части в Azure | Документация Майкрософт
description: Эта страница содержит анализ на уровне управления для тестовой конфигурации, созданной для анализа взаимодействия ExpressRoute, VPN-подключения типа "сеть — сеть" и пиринга виртуальных сетей.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: ee887da18b5666e61bc25365791b2e7dffb925e0
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947120"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---control-plane-analysis"></a>Взаимодействие ExpressRoute, VPN типа "сеть — сеть" и пиринга виртуальных сетей. Анализ на уровне управления

В этой статьей мы выполним анализ тестовой конфигурации на уровне управления. Сама тестовая конфигурация описана в [этой статье][Setup]. Чтобы просмотреть подробные сведения о настройке тестовой конфигурации, изучите [этот документ][Configuration].

Анализ на уровне управления по сути изучает маршруты между сетями внутри топологии. Анализ на уровне управления помогает увидеть топологию различных сетей.

##<a name="hub-and-spoke-vnet-perspective"></a>Схема топологии виртуальной сети со звездообразной архитектурой

На следующей схеме продемонстрирована топология виртуальной сети со звездообразной архитектурой (выделена синим цветом). На схеме также показан номер автономной системы различных сетей и маршруты, используемые между разными сетями. 

[![1]][1]

Обратите внимание, что ASN шлюза ExpressRoute виртуальной сети отличается от ASN других корпоративных пограничных маршрутизаторов Microsoft Enterprise (MSEE). Шлюз ExpressRoute использует частный ASN (65515), а MSEE используют общедоступный ASN (12076) глобально. Когда вы настраиваете пиринг ExpressRoute, так как MSEE является кэширующим узлом, в качестве ASN кэширующего узла вы используете 12076. На стороне Azure MSEE устанавливает пиринг eBGP со шлюзом ExpressRoute. Двойной пиринг eBGP, который MSEE устанавливает для каждого пирингового подключения ExpressRoute, прозрачен на уровне управления. Таким образом, при просмотре таблицы маршрутизации ExpressRoute вы увидите, что для префиксов виртуальной сети используется ASN шлюза ExpressRoute. Ниже приведен пример снимка экрана таблицы маршрутов ExpressRoute: 

[![5]][5]

В Azure ASN имеет важное значение только с точки зрения пиринга. По умолчанию ASN для обоих шлюзов (ExpressRoute и VPN) — 65515.

##<a name="on-premises-location-1-and-remote-vnet-perspective-via-expressroute-1"></a>Схема подключения локального расположения 1 к удаленной виртуальной сети через ExpressRoute-1

Локальное расположение 1 и удаленная виртуальная сеть соединены с центральной виртуальной сетью через ExpressRoute-1, и поэтому имеют одинаковую схему топологии, как показано ниже.

[![2]][2]

##<a name="on-premises-location-1-and-branch-vnet-perspective-via-site-to-site-vpn"></a>Схема подключения локального расположения 1 к виртуальной сети филиала через VPN-подключение типа "сеть — сеть"

Локальное расположение 1 и виртуальная сеть филиала подключены к шлюзу VPN центральной виртуальной сети через VPN-подключение типа "сеть — сеть", и поэтому имеют одинаковую схему топологии, как показано ниже.

[![3]][3]

##<a name="on-premises-location-2-perspective"></a>Схема топологии локального расположения 2

Локальное расположение 2 подключено к центральной виртуальной сети с помощью частного пиринга ExpressRoute 2. 

[![4]][4]

## <a name="further-reading"></a>Дополнительные материалы

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Параллельное использование подключения ExpressRoute и VPN-подключения типа "сеть — сеть"

####  <a name="site-to-site-vpn-over-expressroute"></a>VPN-подключение типа "сеть — сеть" через ExpressRoute

VPN-подключение типа "сеть — сеть" можно настроить через пиринг Microsoft ExpressRoute, чтобы организовать закрытый обмен данными между локальной сетью и виртуальными сетями Azure с гарантией конфиденциальности, защиты от повтора, подлинности и целостности. Дополнительные сведения о настройке VPN-подключения IPSec типа "сеть — сеть" в туннельном режиме через пиринг ExpressRoute Майкрософт см. [в этой статье][S2S-Over-ExR]. 

Основным ограничением конфигурации с VPN-подключением типа "сеть — сеть" через пиринг Майкрософт является пропускная способность. Пропускная способность туннеля IPSec ограничена емкостью VPN-шлюза. Пропускная способность шлюза VPN всегда меньше пропускной способности ExpressRoute. В таких ситуациях можно оптимизировать использование пропускной способности ExpressRoute, настроив туннель IPSec для трафика, требующего высокого уровня безопасности, и частный пиринг для всего остального трафика.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN-подключение типа "сеть — сеть" в качестве пути отработки отказа для ExpressRoute.
ExpressRoute предоставляется в виде пары каналов, что обеспечивает избыточность и высокий уровень доступности. Вы можете настроить географически избыточное подключение ExpressRoute в разных регионах Azure. Как и в нашей тестовой конфигурации, если для подключения ExpressRoute нужен переход на другой ресурс в пределах определенного региона Azure, его можно создать с помощью VPN-подключения типа "сеть — сеть". Если одни и те же префиксы объявляются через ExpressRoute и VPN-подключение типа "сеть — сеть", Azure отдает предпочтение ExpressRoute. Чтобы избежать асимметричной маршрутизации между ExpressRoute и VPN-подключением типа "сеть — сеть", в локальной сети следует также настроить конфигурацию с более высоким приоритетом ExpressRoute относительно VPN-подключения типа "сеть — сеть".

Дополнительные сведения о настройке параллельных подключений ExpressRoute и VPN типа "сеть — сеть" вы найдете в статье [Настройка параллельных подключений "сеть — сеть" и ExpressRoute с помощью PowerShell][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Расширение подключения серверной части к периферийным виртуальным сетям и филиалам

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Подключение к периферийной виртуальной сети с использованием пиринга

Звездообразная архитектура виртуальных сетей нашла широкое применение. Главной является виртуальная сеть в Azure, которая выступает в качестве центральной точки подключения периферийных виртуальных сетей к локальной сети. "Периферийными" называются виртуальные сети, которые устанавливают пиринг с центральной сетью и могут использоваться для изоляции рабочих нагрузок. Трафик передается между локальным центром обработки данных и центральной сетью через подключение ExpressRoute и (или) VPN. Дополнительные сведения о звездообразной архитектуре см. в [этой статье][Hub-n-Spoke].

Пиринг виртуальных сетей в пределах региона позволяет периферийным виртуальным сетям использовать шлюзы центральной виртуальной сети (шлюз VPN или ExpressRoute) для подключения к удаленным сетям.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Подключение к виртуальной сети филиала с помощью VPN типа "сеть — сеть"

Виртуальные сети филиалов (расположенные в разных регионах) могут взаимодействовать с локальными сетями через центральную виртуальную сеть. Стандартным решением в Azure для такой конфигурации является VPN-подключение типа "сеть — сеть". Для маршрутизации в центральной сети также можно использовать виртуальные сетевые модули.

Сведения о настройке VPN-шлюзов см. в [этой статье][VPN]. Сведения о развертывании высокодоступных виртуальных сетевых модулей (NVA) см. в [этой статье][Deploy-NVA].

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о плоскости данных тестовой конфигурации и представлениях функций мониторинга сети Azure см. в [этой статье][Data-Analysis].

Чтобы узнать, сколько каналов ExpressRoute можно подключить к шлюзу ExpressRoute и сколько шлюзов ExpressRoute можно подключить к каналу ExpressRoute, а также изучить другие ограничения масштабирования ExpressRoute, см. [вопросы и ответы об ExpressRoute][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "Схема топологии звездообразной архитектуры виртуальной сети"
[2]: ./media/backend-interoperability/Loc1ExRView.png "Схема топологии подключения между локальным расположением 1 и удаленной виртуальной сетью через ExpressRoute 1"
[3]: ./media/backend-interoperability/Loc1VPNView.png "Схема топологии подключения между локальным расположением 1 и виртуальной сетью филиала через VPN-подключение типа "сеть — сеть""
[4]: ./media/backend-interoperability/Loc2View.png "Схема топологии расположения 2"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "Таблица маршрутов ExpressRoute 1"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-config
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering




