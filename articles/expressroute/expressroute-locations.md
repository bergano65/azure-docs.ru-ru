---
title: Поставщики услуг подключения и расположения для Azure ExpressRoute | Документация Майкрософт
description: В этой статье приведена подробная информация о расположениях, где предлагаются услуги, и способах подключения к регионам Azure. Эта таблица отсортирована по поставщику услуг подключения.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2019
ms.author: pareshmu
ms.openlocfilehash: a30280339b2172860fa8c859de569c2de1c68e17
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59267452"
---
# <a name="expressroute-partners-and-peering-locations"></a>Партнеры и одноранговые расположения ExpressRoute

> [!div class="op_single_selector"]
> * [Расположения по поставщикам](expressroute-locations.md)
> * [Поставщики по расположению](expressroute-locations-providers.md)


В данной статье приведены таблицы со сведениями о поставщиках услуг подключения ExpressRoute, географическом покрытии ExpressRoute, облачных службах Майкрософт, поддерживаемых через ExpressRoute, и системных интеграторах ExpressRoute.

## <a name="partners"></a>Поставщики услуг подключения ExpressRoute
ExpressRoute поддерживается во всех регионах и расположениях Azure. На следующей карте обозначены регионы Azure и расположения ExpressRoute. Расположения ExpressRoute соответствуют тем территориям, где Майкрософт взаимодействует с несколькими одноранговыми поставщиками услуг.

![Карта расположения][0]

Вы сможете получить доступ ко всем службам Azure во всех регионах соответствующего геополитического региона, если вы подключены по меньшей мере к одному расположению ExpressRoute в этом регионе.

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Регионы Azure с расположениями ExpressRoute в пределах геополитических регионов
В следующей таблице сопоставлены регионы Azure с расположениями ExpressRoute в пределах геополитических регионов.

| **Геополитический регион** | **Зона** | **Регионы Azure** | **Расположения ExpressRoute** |
| --- | --- | --- | --- |
| **Государственные организации Австралии** | 1 | Центральная Австралия, Центральная Австралия 2 |Канберра, Канберра 2 |
| **Европа** | 1 |Центральная Франция, Южная Франция, Северная Европа, Западная Европа, западная часть Соединенного Королевства, южная часть Соединенного Королевства |Амстердам, Amsterdam2, Дублин, Лондон, London2, Марселе, ньюпорт (Уэльс), Париж, Цюрих |
| **Северная Америка** | 1 |Восточная часть США, западная часть США, восточная часть США 2, западная часть США 2, центральная часть США, центрально-южная часть США, центрально-северная часть США, центрально-западная часть США, Центральная Канада, Восточная Канада |Атланта, Вашингтон (округ Колумбия), Вашингтон (округ Колумбия) 2, Даллас, Денвер, Квебек, Кремниевая долина, Лас-Вегас, Лос-Анджелес, Майами, Монреаль, Нью-Йорк, Сан-Антонио, Сиэтл, Торонто, Чикаго |
| **Азия** | 2 |Восточная Азия, Юго-Восточная Азия |Гонконг, Куала-Лумпур, Сингапур, Сингапур 2, Тайбэй |
| **Австралия** | 2 |Восточная Австралия, Юго-Восточная Австралия |Мельбурн, Перт, Сидней | 
| **Индия** | 2 |Западная Индия, Центральная Индия, Южная Индия |Ченнаи, Ченнаи 2, Мумбаи, Мумбаи 2 |
| **Япония** | 2 |Западная Япония, Восточная Япония |Осака, Токио |
| **Южная Корея** | 2 |Центральная Корея, Южная Корея |Пусан, Сеул|
| **ОАЭ** | 2 | Центральная часть ОАЭ, Северная часть ОАЭ | Дубай |
| **Южная Африка** | 3 |Запад ЮАР, Север ЮАР |Кейптаун, Йоханнесбург |
| **Южная Америка** | 3 |Южная часть Бразилии |Сан-Паулу |


### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Регионы и геополитические границы для национальных облаков
В таблице ниже содержатся сведения о регионах и геополитических границах для национальных облаков.

| **Геополитический регион** | **Регионы Azure** | **Расположения ExpressRoute** |
| --- | --- | --- |
| **Облако правительства США** |US Gov (Аризона), US Gov (Айова), US Gov (Техас), US Gov (Вирджиния), центральный регион US DoD, восточный регион US DoD  |Чикаго, Даллас, Нью-Йорк, Феникс, Сан-Антонио, Сиэтл, Кремниевая долина, Вашингтон (округ Колумбия) |
| **Восточный Китай** |"Восточный Китай", "Восточный Китай 2" |Шанхай, Шанхай 2 |
| **Северный Китай** |"Северный Китай", "Северный Китай 2" |Пекин, Пекин 2 |
| **Германия** |Центральная Германия, восточная Германия |Берлин, Франкфурт |

В стандартном номере SKU ExpressRoute подключение между геополитическими регионами не поддерживается. Для поддержки глобальных подключений необходимо включить надстройку ExpressRoute класса "Премиум". Подключение к национальным облачным средам не поддерживается. При необходимости вы можете работать с поставщиками услуг подключения.

## <a name="locations"></a>Расположения поставщиков услуг подключения

В следующей таблице показаны расположения по поставщикам услуг. См. дополнительные сведения о [службах доступных поставщиков по расположению](expressroute-locations-providers.md#locations).


### <a name="production-azure"></a>Рабочая среда Azure

| **Поставщик услуг** | **Microsoft Azure** | **Office 365 и Dynamics 365** | **Местоположения** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |Поддерживается |Поддерживается |Мельбурн, Сидней |
| **[Airtel](https://www.airtel.in/creatingsmiles/)** | Поддерживается | Поддерживается | Ченнаи 2, Мумбаи 2 |
| **[Aryaka Networks](https://www.aryaka.com/)** |Поддерживается |Поддерживается |Амстердам, Чикаго, Даллас, Гонконг, Сан-Паулу, Сиэтл, Кремниевая долина, Сингапур, Токио, Вашингтон (округ Колумбия) |
| **[Ascenty Data Centers](https://ascenty.com/servicos/cloud-connect/microsoft-expressroute/)** |Поддерживается |Поддерживается |Сан-Паулу |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Поддерживается |Поддерживается |Амстердам, Вашингтон (округ Колумбия), Даллас, Кремниевая долина, Лондон, Сидней, Сингапур, Токио, Торонто, Чикаго |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Поддерживается |Поддерживается |Квебек, Монреаль, Торонто |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/bt-compute-for-microsoft-azure)** |Поддерживается |Поддерживается |Амстердам, Гонконг, Лондон, Ньюпорт (Уэльс), Сан-Паулу, Кремниевая долина, Сингапур, Сидней, Токио, Вашингтон (округ Колумбия) |
| **C3ntro** |Скоро выход |Скоро выход |Майами |
| **CDC** | Поддерживается | Поддерживается | Канберра, Канберра 2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Поддерживается |Поддерживается |Кремниевая долина, Лас-Вегас, Нью-Йорк, Сан-Антонио, Токио, Торонто |
| **Chief Telecom** |Поддерживается |Поддерживается |Тайбэй |
| **Глобальные затратами на телекоммуникации для Китая** |Поддерживается |Не поддерживается |Гонконг |
| **[Cologix](https://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |Поддерживается |Поддерживается |Чикаго, Даллас, Монреаль, Торонто, Вашингтон (округ Колумбия) |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Поддерживается |Поддерживается |Амстердам, Дублин, Лондон, Париж, Сингапур 2, Токио |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Поддерживается |Поддерживается |Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Поддерживается |Поддерживается |Чикаго, Денвер, Лос-Анджелес, Нью-Йорк, Кремниевая долина, Вашингтон (округ Колумбия), Вашингтон (округ Колумбия) 2 |
| **DE-CIX** | Поддерживается |Поддерживается |Amsterdam2|
| **EIR** |Поддерживается |Поддерживается |Дублин|
| **Эпсилон глобальных каналов связи** |Поддерживается |Поддерживается |Сингапур, Сингапур 2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Поддерживается |Поддерживается |Амстердам, Атланта, Вашингтон (округ Колумбия), Гонконг (САР), Даллас, Дублин, Кремниевая долина, Лондон, Лондон 2, Лос-Анджелес, Майами, Мельбурн, Нью-Йорк, Осака, Париж, Сан-Пауло, Сидней, Сингапур, Сиэтл, Токио, Торонто, Чикаго |
| **Etisalat ОАЭ** |Поддерживается |Поддерживается |Дубай|
| **euNetworks** |Поддерживается |Поддерживается |Амстердам, Дублин, Лондон |
| **GÉANT** |Поддерживается |Поддерживается |Амстердам |
| **[Глобальное облако Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Поддерживается| Поддерживается | Ченнаи, Мумбаи |
| **[InterCloud](https://www.intercloud.com/)** |Поддерживается |Поддерживается |Амстердам, Чикаго, Лондон, Нью-Йорк, Париж, кремниевая Долина, Сингапур, Вашингтон Округ Колумбия |
| **Internet2** |Поддерживается |Поддерживается |Чикаго, Даллас, Вашингтон (округ Колумбия) |
| **[Internet Initiative Japan Inc. — IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Поддерживается |Поддерживается |Осака, Токио |
| **[Решения для Интернет - облако](https://www.is.co.za/solution/cloud-connect/)** |Поддерживается |Поддерживается |Кейптаун, Йоханнесбург, Лондон |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Поддерживается |Поддерживается |Амстердам, Amsterdam2, Дублин, Лондон, Марселе, Париж, Цюрих |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Поддерживается |Поддерживается | Амстердам, London2, кремниевая Долина, Торонто |
| **Jisc +** |Поддерживается |Поддерживается |Лондон |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Поддерживается |Поддерживается |Сеул |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | Поддерживается |Поддерживается |Сидней |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Поддерживается | Поддерживается | Амстердам | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Поддерживается |Поддерживается |Амстердам, Вашингтон (округ Колумбия), Даллас, Кремниевая долина, Лондон, Ньюпорт (Уэльс), Сан-Паулу, Сингапур, Сиэтл, Чикаго |
| **LG CNS** |Поддерживается |Поддерживается |Пусан, Сеул |
| **[Liquid затратами на телекоммуникации](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Поддерживается |Поддерживается |Кейптаун, Йоханнесбург |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Поддерживается |Поддерживается |Амстердам, Атланта, Чикаго, Даллас, Денвер, Дублин, Гонконг, Лас-Вегас, Лондон, Лос-Анджелес, Мельбурн, Майами, Нью-Йорк, ПЕРТ, Квебек, Сан-Антонио, Сиэтл, кремниевая Долина, Сингапур, Сингапур 2, Сидней, Торонто, Вашингтон Округ Колумбия |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** |Поддерживается |Поддерживается |Лондон |
| **[Neutrona сетей](http://www.neutrona.com/index.php/azure-expressroute/)** |Поддерживается |Поддерживается |Даллас, Лос-Анджелес, Майами, Сан-Паулу |
| **[Next Generation Data](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Поддерживается |Поддерживается |Ньюпорт (Уэльс) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Поддерживается |Поддерживается |Мельбурн, Перт, Сидней |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Поддерживается |Поддерживается |Амстердам, Специальный административный регион Гонконг, Лондон, Лос-Анджелес, Осака, Сингапур, Сидней, Токио, Вашингтон (округ Колумбия) |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |Поддерживается |Поддерживается |Токио |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Поддерживается |Поддерживается |Осака |
| **[Optus](https://www.optus.com.au/enterprise/networking/network-connectivity/express-link)** |Поддерживается |Поддерживается |Мельбурн, Сидней |
| **[Оранжевый](https://www.orange-business.com/en/products/business-vpn-galerie)** |Поддерживается |Поддерживается |Амстердам, Гонконг, Лондон, Париж, Сан-Паулу, Кремниевая долина, Сингапур, Сидней, Токио, Вашингтон (округ Колумбия) |
| **[PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)** |Поддерживается |Поддерживается |Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **[Ограниченные PCCW глобального](https://consoleconnect.com/clouds/#azureRegions)** |Поддерживается |Поддерживается |Чикаго, Гонконг, Лондон |
| **[Sejong затратами на телекоммуникации](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Поддерживается |Поддерживается |Сеул |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Поддерживается |Поддерживается |Ченнаи, Мумбаи 2 |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Поддерживается |Поддерживается |Сингапур, Сингапур 2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Поддерживается |Поддерживается |Осака, Токио |
| **[Спринта](https://business.sprint.com/solutions/cloud-networking/)** |Поддерживается |Поддерживается |Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Поддерживается |Поддерживается |Амстердам, Ченнаи, Гонконг, Лондон, Мумбаи, Сан-Паулу, Кремниевая долина, Сингапур, Вашингтон (округ Колумбия) |
| **Telecity Group** |Поддерживается |Поддерживается |Амстердам |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Поддерживается |Поддерживается |Амстердам, Сан-Паулу |
| **[Telehouse — KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Поддерживается |Поддерживается |Лондон |
| **Telenor** |Поддерживается |Поддерживается |Амстердам, Лондон |
| **[Telia перевозчика](https://teliacarrier.com/our-services/connectivity/cloud-connect.html?title=Cloud%20Connect)** | Поддерживается | Поддерживается |Амстердам, Вашингтон, округ Колумбия, Даллас, Лондон, Чикаго |
| **Telmex Uninet**| Поддерживается | Поддерживается | Даллас |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Поддерживается |Поддерживается |Мельбурн, Сингапур, Сидней |
| **[Telus](https://www.telus.com)** |Поддерживается |Поддерживается |Монреаль, Торонто |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Поддерживается |Поддерживается |Кейптаун, Йоханнесбург |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/cloud-interconnect)** | Поддерживается | Поддерживается | Куала-Лумпур |
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Поддерживается |Поддерживается |Сан-Паулу |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Поддерживается |Поддерживается |Амстердам, Чикаго, Даллас, Специальный административный регион Гонконг, Лондон, Кремниевая долина, Сингапур, Сидней, Токио, Вашингтон (округ Колумбия) |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Поддерживается |Не поддерживается |Лондон, Сингапур |
| **Идея Vodafone** | Поддерживается | Поддерживается | Мумбаи, Мумбаи 2 |
| **[Zayo](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Поддерживается |Поддерживается |Амстердам, Чикаго, Даллас, Денвер, Лондон, Лос-Анджелес, Монреаль, Нью-Йорк, Париж, Сиэтл, кремниевая Долина, Торонто, Вашингтон Округ Колумбия |

 **+** означает "скоро"

### <a name="national-cloud-environment"></a>Национальная облачная среда

### <a name="us-government-cloud"></a>Облако правительства США

| **Поставщик услуг** | **Microsoft Azure** | **Office 365** | **Местоположения** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Поддерживается |Поддерживается |Чикаго, Phoenix, Вашингтон Округ Колумбия |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Поддерживается |Поддерживается |Нью-Йорк, Финикс |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Поддерживается |Поддерживается |Чикаго, Даллас, Нью-Йорк, Сиэтл, Кремниевая долина, Вашингтон (округ Колумбия) |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Поддерживается |Поддерживается |Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Поддерживается | Поддерживается | Чикаго, Даллас, Сан-Антонио, Сиэтл |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Поддерживается |Поддерживается |Чикаго, Даллас, Нью-Йорк, Кремниевая долина, Вашингтон (округ Колумбия) |

### <a name="china"></a>Китай

| **Поставщик услуг** | **Microsoft Azure** | **Office 365** | **Местоположения** |
| --- | --- | --- | --- |
| **China Telecom** |Поддерживается |Не поддерживается |Пекин, Шанхай |
| **[GDS](http://en.gds-services.com/news_detail/newsId=21.html)** |Поддерживается |Не поддерживается |Пекин 2, Шанхай 2 |

Дополнительные сведения см. на странице [ExpressRoute в Китае](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Германия

| **Поставщик услуг** | **Microsoft Azure** | **Office 365** | **Местоположения** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Поддерживается |Не поддерживается |Франкфурт |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Поддерживается |Не поддерживается |Франкфурт |
| **[e укрытия](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Поддерживается |Не поддерживается |Берлин |
| **Interxion** |Поддерживается |Не поддерживается |Франкфурт |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Поддерживается  | Не поддерживается | Берлин |
| **T-Systems** |Поддерживается |Не поддерживается |Берлин |

## <a name="connectivity-through-exchange-providers"></a>Подключение через поставщиков Exchange

Вы можете создать подключение, даже если ваш поставщик услуг подключения не указан в предыдущих разделах.

* Узнайте у своего поставщика услуг подключения, подключен ли он к какому-либо Exchange, указанному в таблице выше. Дополнительные сведения об услугах, предлагаемых поставщиками Exchange, см. по ссылкам ниже. Несколько поставщиков услуг подключения уже подключены к серверам Ethernet Exchange.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/) 
* Обратитесь к своему поставщику услуг подключения, чтобы он расширил вашу сеть, добавив необходимое пиринговое расположение.
  * Убедитесь, что поставщик услуг подключения расширяет границы вашего подключения, сохраняя высокую доступность во избежание влияния единых точек отказа.
* Закажите канал ExpressRoute с Exchange у вашего поставщика услуг подключения для связи с Майкрософт.
  * Для настройки подключения выполните действия, описанные в статье [Создание и изменение канала ExpressRoute с помощью PowerShell](expressroute-howto-circuit-classic.md) .

## <a name="connectivity-through-additional-service-providers"></a>Подключение через дополнительных поставщиков услуг

| **Поставщик услуг подключения** | **Exchange** | **Местоположения** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Сингапур |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/expressroute)** | Equinix, Cologix | Торонто, Монреаль |
| **[Alaska Communications](https://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Сиэтл; |
| **[Altice бизнеса](https://golightpath.com/transport)** |Equinix |Нью-Йорк, Вашингтон (округ Колумбия) |
| **[Корпорация Arteria сетей](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Токио |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Даллас|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Лондон |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Амстердам, Франкфурт, Лондон, Сингапур, Вашингтон (округ Колумбия) |
| **[Башня широкополосное подключение, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Токио |
| **[C3ntro затратами на телекоммуникации](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Даллас |
| **[Руководитель](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | Гонконг |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Франкфурт, Германия |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Амстердам | 
| **[Cogeco Peer 1](https://www.cogecopeer1.com/en/)**| Equinix | Монреаль, Торонто |
| **[CoreAzure](http://coreazure.com/expressroute)**| Equinix | Лондон |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)** | Equinix | Даллас, Кремниевая долина, Вашингтон, округ Колумбия | 
| **[Данные Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Даллас |
| **[Эпсилон телекоммуникаций Limited](http://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | Лондон, Сингапур, Вашингтон, округ Колумбия |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Амстердам |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Лондон |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Амстердам |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Город Квебек |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Вашингтон, округ Колумбия |
| **[Мост залива International](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | Амстердам |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | Лондон, Слау |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Торонто |
| **LGA затратами на телекоммуникации** |Equinix |Сингапур|
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix | Вашингтон (округ Колумбия), Нью-Йорк, Чикаго |
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Гонконг |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Сидней |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Амстердам |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Вашингтон, округ Колумбия |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** | Teraco | Кейптаун, Йоханнесбург |
| **[NexGen сетей](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Лондон |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | Амстердам, Франкфурт |
| **[Опубликовать](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Амстердам |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Амстердам, Дублин, Лондон, Париж |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Франкфурт |  
| **Rogers** | Cologix, Equinix | Монреаль, Торонто |
| **[Tamares затратами на телекоммуникации](http://www.tamarestelecom.com/our-services/#Connectivity)** | Telecity | Лондон | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Амстердам | 
| **[Узнать о Italia затратами на телекоммуникации](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Амстердам |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Амстердам |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Торонто | 
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | Даллас, Лос-Анджелес |
| **[США информационные потоки (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Сингапур |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | Сан-Паулу |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | Нью-Йорк |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **Набулси** |Equinix |Лондон|
| **[Zertia](https://www.zertia.es)**| Level 3 | Мадрид |
| **[Zirro](https://zirro.com/services/)**| Equinix | Монреаль, Торонто |

## <a name="connectivity-through-datacenter-providers"></a>Подключение с помощью поставщиков центров обработки данных

| **Поставщик** | **Exchange** |
| --- | --- |
| **[Cyrus один](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport |
| **[Цифровой недвижимости](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | Megaport |
| **[EdgeConnex](http://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport |
| **[RagingWire центров обработки данных](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach |
| **[T5 центров обработки данных](https://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Подключение через национальные исследовательские и образовательные сети (NREN)

| **Поставщик**|
| --- |
| **AARNET**| 
| **DeIC через GÉANT**|
| **GARR через GÉANT**|
| **GÉANT**|
| **HEAnet через GÉANT**|
| **Internet2**|
| **JISC +**|
| **RedIRIS через GÉANT**|
| **SINET**|
| **Surfnet через GÉANT**|

* Если вашего поставщика услуг подключения нет в списке, проверьте, подключены ли они к любому из перечисленных выше партнеров ExpressRoute Exchange.

## <a name="expressroute-system-integrators"></a>Системные интеграторы ExpressRoute
Возможность частного подключения, соответствующего вашим потребностям, будет зависеть от масштаба сети. Чтобы упростить переход на ExpressRoute, вы можете обратиться к одному из системных интеграторов, указанных в таблице ниже.

| **Системный интегратор** | **Continent** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | Европа |
| **[Avanade Inc.](https://www.avanade.com/)** | Азия, Европа, Северная Америка, Южная Америка |
| **[Bright Skies GmbH](http://bskies.io/expressroute)** | Европа
| **[Ensyst](https://www.ensyst.com.au)** | Азия
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | Северная Америка |
| **[FlexManage](https://www.flexmanage.com/cloud)** | Северная Америка |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | Северная Америка |
| **[ИТ-отдел консультации](https://itconsult.com.au/)** | Австралия |
| **[MOQdigital](https://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | Австралия |
| **[MSG служб](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Европа (Германия) |
| **[Nelite](https://www.nelite.com/offres-services/)** | Европа |
| **[New Signature](https://newsignature.com/technologies/express-route/)** | Европа |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Азия |
| **[Оранжевый сетей](https://orange-networks.com/blog/88-azureexpressroute)** | Европа |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Северная Америка |
| **[Presidio](https://info.presidio.com/microsoft-azure-expressroute)** | Северная Америка |
| **[SoL tec](https://www.sol-tec.com/services)** | Европа |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Южная Америка |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Австралия |

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения об ExpressRoute см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).
* Убедитесь, что выполнены все необходимые условия. Ознакомьтесь с разделом [Предварительные требования и контрольный список для ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Карта расположения"
