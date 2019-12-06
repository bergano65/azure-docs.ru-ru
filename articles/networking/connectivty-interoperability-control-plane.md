---
title: Взаимодействие в функциях подключения к серверной части в Azure. Анализ на уровне управления | Документация Майкрософт
description: В этой статье описывается анализ на уровне управления для тестовой конфигурации, с помощью которой можно выполнять анализ взаимодействия между ExpressRoute, VPN типа "сеть — сеть" и пирингом виртуальных сетей в Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 4921e4c4fc0da95250a0171c66d6a69093b10687
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873851"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-control-plane-analysis"></a>Взаимодействие в функциях подключения к серверной части в Azure. Анализ на уровне управления

В этой статье описывается анализ плоскости управления для [настройки теста][Setup]. Кроме того, можно ознакомиться с [конфигурацией настройки тестирования][Configuration] и [анализом плоскости данных][Data-Analysis] для настройки теста.

Анализ на уровне управления по сути изучает маршруты между сетями внутри топологии. Такой анализ помогает понять топологию различных сетей.

## <a name="hub-and-spoke-vnet-perspective"></a>Схема топологии виртуальной сети со звездообразной архитектурой

На следующем рисунке показана схема топологии сети со звездообразной архитектурой: центральная и периферийная виртуальная сеть (выделена синим). На рисунке также показан номер автономной системы различных сетей и маршруты, используемые между разными сетями: 

![1][1]

ASN шлюза Azure ExpressRoute виртуальной сети отличается от ASN других корпоративных пограничных маршрутизаторов Microsoft Enterprise (MSEE). Шлюз ExpressRoute использует частный ASN (значение **65515**), а MSEE используют общедоступный ASN (значение **12076**) глобально. Когда вы настраиваете пиринг ExpressRoute, так как MSEE является кэширующим узлом, в качестве ASN кэширующего узла вы используете **12076**. На стороне Azure MSEE устанавливает пиринг eBGP со шлюзом ExpressRoute. Двойной пиринг eBGP, который MSEE устанавливает для каждого пирингового подключения ExpressRoute, прозрачен на уровне управления. Таким образом, при просмотре таблицы маршрутов ExpressRoute вы увидите, что для префиксов виртуальной сети используется ASN шлюза ExpressRoute. 

На следующем рисунке показан пример таблицы маршрутов ExpressRoute: 

![5][5]

В Azure ASN имеет важное значение только с точки зрения пиринга. По умолчанию ASN шлюза ExpressRoute и VPN-шлюза Azure имеет значение **65515**.

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>Схема подключения локального расположения 1 к удаленной виртуальной сети через ExpressRoute 1

Локальное расположение 1 и удаленная виртуальная сеть подключены к центральной виртуальной сети через ExpressRoute 1. Они имеют одинаковую схему топологии, как показано на рисунке, приведенном ниже:

![2][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>Схема подключения локального расположения 1 к виртуальной сети филиала через VPN-подключение типа "сеть — сеть"

И расположение 1, и виртуальная сеть филиала подключены к VPN-шлюзу центральной виртуальной сети через VPN-подключение типа "сеть — сеть". Они имеют одинаковую схему топологии, как показано на рисунке, приведенном ниже:

![3][3]

## <a name="on-premises-location-2-perspective"></a>Схема топологии локального расположения 2

Локальное расположение 2 подключено к центральной виртуальной сети с помощью частного пиринга ExpressRoute 2: 

![4..][4]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Параллельное использование подключений ExpressRoute и VPN типа "сеть — сеть"

###  <a name="site-to-site-vpn-over-expressroute"></a>VPN типа "сеть — сеть" через ExpressRoute

VPN типа "сеть — сеть" можно настроить с использованием пиринга Microsoft ExpressRoute, чтобы организовать закрытый обмен данными между локальной сетью и виртуальными сетями Azure. Такая конфигурация гарантирует конфиденциальность, подлинность и целостность при обмене данными. Обмен данными также защищается от повторов. Дополнительные сведения о настройке VPN-подключения типа "сеть — сеть" в режиме туннелирования с помощью пиринга Microsoft ExpressRoute см. в [статье VPN-подключение типа "сеть — сеть" через Expressroute Microsoft пиринг][S2S-Over-ExR]. 

Основным ограничением настройки VPN типа "сеть — сеть" с использованием пиринга Майкрософт является пропускная способность. Пропускная способность туннеля IPsec ограничена емкостью VPN-шлюза. Пропускная способность VPN-шлюза всегда ниже пропускной способности ExpressRoute. В такой ситуации можно оптимизировать использование пропускной способности ExpressRoute, используя туннель IPSec для трафика, требующего высокого уровня безопасности, и частный пиринг для всего остального трафика.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN типа "сеть — сеть" в качестве защищенного пути отработки отказа для ExpressRoute

ExpressRoute предоставляется в виде пары каналов, что обеспечивает избыточность и высокий уровень доступности. Вы можете настроить географически избыточное подключение ExpressRoute в разных регионах Azure. Как и в нашей тестовой конфигурации, вы можете создать путь отработки отказа для подключения ExpressRoute в пределах определенного региона Azure с помощью VPN типа "сеть — сеть". Если одни и те же префиксы объявляются через ExpressRoute и VPN типа "сеть — сеть", Azure отдает предпочтение ExpressRoute. Чтобы избежать асимметричной маршрутизации между ExpressRoute и VPN типа "сеть — сеть", в локальной сети следует также настроить конфигурацию ExpressRoute, прежде чем использовать VPN типа "сеть — сеть".

Дополнительные сведения о настройке существующих подключений для ExpressRoute и VPN-подключений типа "сеть — сеть" см. в статье [сосуществование expressroute и][ExR-S2S-CoEx]подключения "сеть — сеть".

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Предоставление периферийным виртуальным сетям и расположениям филиалов доступа к подключениям серверной части

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Подключение к периферийной виртуальной сети с использованием пиринга

Звездообразная архитектура виртуальных сетей нашла широкое применение. Центральная виртуальная сеть в Azure выполняет роль центральной точки для подключения между периферийными виртуальными сетями и локальной сетью. Периферийными называются виртуальные сети, которые устанавливают пиринг с центральной сетью и применяются для изоляции рабочих нагрузок. Трафик передается между локальным центром обработки данных и центральной сетью через подключение ExpressRoute и (или) VPN. Дополнительные сведения об архитектуре см. [в статье реализация топологии сети с центральным периферийным портом в Azure][Hub-n-Spoke].

Пиринг виртуальных сетей в пределах региона позволяет периферийным виртуальным сетям использовать шлюзы центральной виртуальной сети (шлюз VPN или ExpressRoute) для подключения к удаленным сетям.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Подключение к виртуальной сети филиала с помощью VPN типа "сеть — сеть"

Через центральную виртуальную сеть можно подключить друг к другу виртуальные сети филиалов из разных регионов и локальные сети. Обычно в Azure решением для этой конфигурации является VPN типа "сеть — сеть". Альтернативой может быть использование в центральной сети виртуального сетевого модуля (NVA) для маршрутизации.

Дополнительные сведения см. в статье [что такое VPN-шлюз?][VPN] и [развертывание NVA высокой доступности][Deploy-NVA].

## <a name="next-steps"></a>Дальнейшие действия

Сведения об [анализе плоскости данных][Data-Analysis] в представлениях настройки тестирования и функции мониторинга сети Azure.

[Вопросы и ответы по ExpressRoute][ExR-FAQ] см. в следующих статьях:
-   количество каналов ExpressRoute, которые можно подключить к шлюзу ExpressRoute;
-   количество шлюзов ExpressRoute, которые можно подключить к каналу ExpressRoute;
-   Дополнительные ограничения масштаба ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "Перспектива топологии HUB и периферийной виртуальной сети"
[2]: ./media/backend-interoperability/Loc1ExRView.png "Расположение 1 и удаленная виртуальная сеть с точки зрения топологии через ExpressRoute 1"
[3]: ./media/backend-interoperability/Loc1VPNView.png "Расположение 1 и виртуальная сеть филиалов топологии через VPN-подключение типа "сеть — сеть""
[4]: ./media/backend-interoperability/Loc2View.png "Расположение 2 точки топологии"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "Таблица маршрутов ExpressRoute 1"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
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


