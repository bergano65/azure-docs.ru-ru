---
title: Поставщики услуг подключения и расположения для Azure ExpressRoute | Документация Майкрософт
description: В этой статье приведена подробная информация о расположениях, где предлагаются услуги, и способах подключения к регионам Azure. Эта таблица отсортирована по поставщику услуг подключения.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/20/2019
ms.author: cherylmc
ms.openlocfilehash: cf5f857785c5add177a0534248764873df12a2c0
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030476"
---
# <a name="expressroute-partners-and-peering-locations"></a>Партнеры и одноранговые расположения ExpressRoute

> [!div class="op_single_selector"]
> * [Расположения по поставщикам](expressroute-locations.md)
> * [Поставщики по расположению](expressroute-locations-providers.md)


В таблицах этой статьи содержатся сведения о географическом покрытии и расположениях ExpressRoute, поставщиках подключений ExpressRoute и системных интеграторах ExpressRoute (SIs).

> [!Note]
> Регионы Azure и расположения ExpressRoute — это два отдельных и разные понятия. понимание различий между ними крайне важно для изучения гибридных сетевых подключений Azure. 
>
>

## <a name="azure-regions"></a>Регионы Azure
Регионы Azure — это глобальные центры обработки данных, в которых находятся ресурсы вычислений, сети и хранилища Azure. При создании ресурса Azure клиенту необходимо выбрать расположение ресурса. Расположение ресурса определяет, в каком центре обработки данных Azure (или зоне доступности) создается ресурс.

## <a name="expressroute-locations"></a>Расположения ExpressRoute
Расположения ExpressRoute (которые иногда называют расположениями пиринга или "соответствие-мне") — это средства совместного размещения, в которых находятся устройства Microsoft Enterprise (MSEE). Расположения ExpressRoute являются точкой входа в сеть корпорации Майкрософт и глобально распределены, предоставляя клиентам возможность подключаться к сети корпорации Майкрософт по всему миру. Эти расположения позволяют партнерам ExpressRoute и клиентам ExpressRoute напрямую выдавать перекрестные подключения к сети корпорации Майкрософт. В общем случае расположение ExpressRoute не обязательно должно соответствовать региону Azure. Например, клиент может создать канал ExpressRoute с расположением ресурса *восточной части США*в расположении пиринга в *Сиэтле* .

Вы сможете получить доступ ко всем службам Azure во всех регионах соответствующего геополитического региона, если вы подключены по меньшей мере к одному расположению ExpressRoute в этом регионе.

## <a name="locations"></a>Регионы Azure для расположений ExpressRoute в гео-неадминистративной области.
В следующей таблице сопоставлены регионы Azure с расположениями ExpressRoute в пределах геополитических регионов.

| **Геополитический регион** | **Регионы Azure** | **Расположения ExpressRoute** |
| --- | --- | --- |
| **Государственные организации Австралии** |Центральная Австралия, Центральная Австралия 2 |Канберра, Канберра 2 |
| **Европа** | Центральная Франция, Южная Франция, Северная Европа, Западная Европа, западная часть Соединенного Королевства, южная часть Соединенного Королевства |Амстердам, Amsterdam2, Копенгаген, Дублин, Франкфурт, Лондон, London2, Марселе, Ньюпорт (), Париж, Стокгольм, Цюрих |
| **Северная Америка** |Восточная часть США, западная часть США, восточная часть США 2, западная часть США 2, центральная часть США, центрально-южная часть США, центрально-северная часть США, центрально-западная часть США, Центральная Канада, Восточная Канада |Атланта, Чикаго, Далласе, Денвер, Лас-, Лос-Анджелес, Майами, Нью-Йорк, Сан Сан Антонио, Сиэтл, впадина, Silicon Valley2, Вашингтон (округ Колумбия), штат Вашингтон, округ DC2, Монреаль, Квебек, Москва, Торонто |
| **Азия** | Восточная Азия, Юго-Восточная Азия |Гонконг, САР, Куала, Сингапур, Сингапур 2, Тайбэй |
| **Индия** | Западная Индия, Центральная Индия, Южная Индия |Ченнаи, Ченнаи 2, Мумбаи, Мумбаи 2 |
| **Япония** | Западная Япония, Восточная Япония |Осака, Токио |
| **Океания** | Восточная Австралия, Юго-Восточная Австралия |Г., Мельбурн, Перт, Сидней |
| **Южная Корея** | Центральная Корея, Южная Корея |Пусан, Сеул|
| **ЧАСТЬ ОАЭ** | Центральная часть ОАЭ, Север ОАЭ | Дубаи, Dubai2 |
| **ЮАР** | Южно-Африканская Республика, Юго-Африканская Республика, Северная Африка |Кейптаун, Йоханнесбург |
| **Южная Америка** | Южная часть Бразилии |Сан-Паулу |


## <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Регионы и геополитические границы для национальных облаков
В таблице ниже содержатся сведения о регионах и геополитических границах для национальных облаков.

| **Геополитический регион** | **Регионы Azure** | **Расположения ExpressRoute** |
| --- | --- | --- |
| **Облако правительства США** |US Gov (Аризона), US Gov (Айова), US Gov (Техас), US Gov (Вирджиния), центральный регион US DoD, восточный регион US DoD  |Чикаго, Даллас, Нью-Йорк, Феникс, Сан-Антонио, Сиэтл, Кремниевая долина, Вашингтон (округ Колумбия) |
| **Восточный Китай** |"Восточный Китай", "Восточный Китай 2" |Шанхай, Шанхай 2 |
| **Северный Китай** |"Северный Китай", "Северный Китай 2" |Пекин, Пекин 2 |
| **Германия** |Центральная Германия, восточная Германия |Берлин, Франкфурт |

В стандартном номере SKU ExpressRoute подключение между геополитическими регионами не поддерживается. Для поддержки глобальных подключений необходимо включить надстройку ExpressRoute класса "Премиум". Подключение к национальным облачным средам не поддерживается. При необходимости вы можете работать с поставщиками услуг подключения.

## <a name="partners"></a>Поставщики услуг подключения ExpressRoute

В следующей таблице показаны расположения по поставщикам услуг. См. дополнительные сведения о [службах доступных поставщиков по расположению](expressroute-locations-providers.md).


### <a name="production-azure"></a>Рабочая среда Azure

| **Поставщик услуг** | **Microsoft Azure** | **Office 365**  | **Расположения** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |Поддерживаются |Поддерживаются |Мельбурн, Сидней |
| **[Airtel](https://www.airtel.in/business/#/)** | Поддерживаются | Поддерживаются | Ченнаи 2, Мумбаи 2 |
| **[Aryaka Networks](https://www.aryaka.com/)** |Поддерживаются |Поддерживаются |Амстердам, Чикаго, Далласе, Гонконг, САР Сан-Паулу, Сиэтл, Silicon впадина, Сингапур, Токио, Вашингтон (округ Колумбия) |
| **[Ascenty Data Centers](https://www.ascenty.com/en/cloud/microsoft-express-route)** |Поддерживаются |Поддерживаются |Сан-Паулу |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Поддерживаются |Поддерживаются |Амстердам, Вашингтон (округ Колумбия), Даллас, Кремниевая долина, Лондон, Сидней, Сингапур, Токио, Торонто, Чикаго |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Поддерживаются |Поддерживаются |Квебек, Монреаль, Торонто |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/bt-compute-for-microsoft-azure)** |Поддерживаются |Поддерживаются |Амстердам, Гонконг, САР, Йоханнесбург, Лондон, Ньюпорт (штат Вашингтон), Сан-Паулу, Silicon впадина, Сингапур, Сидней, Токио, Вашингтон (округ Колумбия) |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |Поддерживаются |Поддерживаются |Майами |
| **CDC** | Поддерживаются | Поддерживаются | Канберра, Канберра 2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Поддерживаются |Поддерживаются |Amsterdam2, Чикаго, Гонконг, Лас деньги, Нью Йорк, Париж, Сан Сан Антонио, Silicon впадина, Токио, Торонто, Вашингтон (округ Колумбия) |
| **[Главный телекоммуникации](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** |Поддерживаются |Поддерживаются |Гонконг, Тайбэй |
| **China Telecom Global** |Поддерживаются |Поддерживаются |Гонконг, САР |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |Поддерживаются |Поддерживаются |Чикаго, Даллас, Монреаль, Торонто, Вашингтон (округ Колумбия) |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Поддерживаются |Поддерживаются |Амстердам, Amsterdam2, Дублин, Лондон, Ньюпорт, Осака, Париж, Сингапур 2, Токио |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Поддерживаются |Поддерживаются |Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Поддерживаются |Поддерживаются |Чикаго, Денвер, Лос-Анджелес, Нью-Йорк, Санкт-Петербург, Silicon Valley2, Вашингтон (округ Колумбия), Вашингтон |
| **[DE-ЦИКС](https://www.de-cix.net/en/de-cix-service-world/directcloud/find-a-cloud-service/detail/microsoft-azure)** | Поддерживаются |Поддерживаются |Amsterdam2, Франкфурт, Марселе|
| **[деволи](https://devoli.com/expressroute)** | Поддерживаются |Поддерживаются | Г., Мельбурн, Сидней |
| **Du датамена** |Поддерживаются |Поддерживаются | Dubai2 |
| **eir** |Поддерживаются |Поддерживаются |Дублин|
| **[Глобальная связь Эпсилон](https://www.epsilontel.com/solutions/direct-cloud-connect)** |Поддерживаются |Поддерживаются |Сингапур, Сингапур 2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Поддерживаются |Поддерживаются |Амстердам, Атланта, Чикаго, Далласе, Дублин, Гонконг, САР, Лондон, London2, Лос-Анджелес, Мельбурн, Майами, Нью-Йорк, Осака, Париж, Сан-Паулу, Сиэтл, Silicon впадина, Сингапур, Сидней, Токио, Торонто, Вашингтон, округ Колумбия |
| **Етисалат ОАЭ** |Поддерживаются |Поддерживаются |Дубай|
| **[еунетворкс](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |Поддерживаются |Поддерживаются |Амстердам, Дублин, Лондон |
| **фареастоне** |Поддерживаются |Поддерживаются |Тайбэй|
| **GÉANT** |Поддерживаются |Поддерживаются |Амстердам |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Поддерживаются| Поддерживаются | Ченнаи, Мумбаи |
| **[InterCloud](https://www.intercloud.com/)** |Поддерживаются |Поддерживаются |Амстердам, Чикаго, Лондон, Нью Йорк, Париж, Silicon впадина, Сингапур, Вашингтон, округ Колумбия, Цюрих |
| **[Internet2](https://www.internet2.edu/products-services/cloud-services-applications/microsoft-azure/#service-cloud-connect)** |Поддерживаются |Поддерживаются |Чикаго, Dallas, Silicon впадин, Вашингтон (округ Колумбия) |
| **[Internet Initiative Japan Inc. - IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Поддерживаются |Поддерживаются |Осака, Токио |
| **[Internet Solutions - Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Поддерживаются |Поддерживаются |Кейптаун, Йоханнесбург, Лондон |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Поддерживаются |Поддерживаются |Амстердам, Amsterdam2, Копенгаген, Дублин, Франкфурт, Лондон, Марселе, Париж, Цюрих |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Поддерживаются |Поддерживаются | Амстердам, London2, Silicon впадина, Торонто |
| **Жагуар сеть** |Поддерживаются |Поддерживаются |Марсель|
| **[JISC](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |Поддерживаются |Поддерживаются |Лондон |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Поддерживаются |Поддерживаются |Сеул |
| **[кордиа](https://www.kordia.co.nz/cloudconnect)** | Поддерживаются |Поддерживаются |О., Сидней |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Поддерживаются | Поддерживаются | Амстердам | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Поддерживаются |Поддерживаются |Амстердам, Вашингтон (округ Колумбия), Даллас, Кремниевая долина, Лондон, Ньюпорт (Уэльс), Сан-Паулу, Сингапур, Сиэтл, Чикаго |
| **LG CNS** |Поддерживаются |Поддерживаются |Пусан, Сеул |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Поддерживаются |Поддерживаются |Кейптаун, Йоханнесбург |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Поддерживаются |Поддерживаются |Амстердам, Атланта, Москва, Чикаго, Далласе, Денвер, Дублин, Гонконг, Лас-деньги, Лондон, Лос-Анджелес, Мельбурн, Майами, Монреаль, Нью-Йорк, Перт, Квебек City, San Сан Антонио, Сиэтл, Silicon впадина, Сингапур, Сингапур 2, Сидней, Торонто, штат Вашингтон Постоянный |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** |Поддерживаются |Поддерживаются |Лондон |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |Поддерживаются |Поддерживаются |Далласе, Лос-Анджелес, Майами, Сан-Паулу, Вашингтон (округ Колумбия) |
| **[Next Generation Data](https://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Поддерживаются |Поддерживаются |Ньюпорт (Уэльс) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Поддерживаются |Поддерживаются |Мельбурн, Перт, Сидней |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Поддерживаются |Поддерживаются |Амстердам, Гонконг, САР, Лондон, Лос-Анджелес, Осака, Сингапур, Сидней, Токио, Вашингтон (округ Колумбия) |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |Поддерживаются |Поддерживаются |Токио |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Поддерживаются |Поддерживаются |Осака |
| **[Optus](https://www.optus.com.au/enterprise/networking/network-connectivity/express-link)** |Поддерживаются |Поддерживаются |Мельбурн, Сидней |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |Поддерживаются |Поддерживаются |Амстердам, Франкфурт, Гонконг, САР, Йоханнесбург, Лондон, Париж, Сан-Паулу, Silicon впадина, Сингапур, Сидней, Токио, Вашингтон (округ Колумбия) |
| **[орикском](https://www.orixcom.com/cloud-solutions/)** | Поддерживаются | Поддерживаются | Dubai2 |
| **[PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)** |Поддерживаются |Поддерживаются |Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Поддерживаются |Поддерживаются |Чикаго, САР Гонконг, Лондон |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Поддерживаются |Поддерживаются |Сеул |
| **[SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | Поддерживаются |Поддерживаются | Вашингтон, округ Колумбия |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Поддерживаются |Поддерживаются |Ченнаи, Мумбаи 2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Поддерживаются |Поддерживаются |Сингапур, Сингапур 2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Поддерживаются |Поддерживаются |Осака, Токио |
| **[Spark (NZ)](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |Поддерживаются |Поддерживаются |О., Сидней |
| **[Спринт](https://business.sprint.com/solutions/cloud-networking/)** |Поддерживаются |Поддерживаются |Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Поддерживаются |Поддерживаются |Амстердам, Ченнаи, Гонконг, САР, Лондон, Мумбаи, Сан-Паулу, Silicon впадина, Сингапур, Вашингтон (округ Колумбия) |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Поддерживаются |Поддерживаются |Амстердам, Сан-Паулу |
| **[Telehouse — KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Поддерживаются |Поддерживаются |Лондон |
| **Telenor** |Поддерживаются |Поддерживаются |Амстердам, Лондон |
| **[Telia Carrier](https://teliacarrier.com/our-services/connectivity/cloud-connect.html?title=Cloud%20Connect)** | Поддерживаются | Поддерживаются |Амстердам, Чикаго, Далласе, Гонконг, Лондон, Париж, Silicon впадина, Стокгольм, Вашингтон (округ Колумбия) |
| **Telmex Uninet**| Поддерживаются | Поддерживаются | Даллас |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Поддерживаются |Поддерживаются |Мельбурн, Сингапур, Сидней |
| **[Telus](https://www.telus.com)** |Поддерживаются |Поддерживаются |Монреаль, Сиэтл, Торонто |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Поддерживаются |Поддерживаются |Кейптаун, Йоханнесбург |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/cloud-interconnect)** | Поддерживаются | Поддерживаются | Куала-Лумпур |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |Поддерживаются |Поддерживаются |Далласе, + Лос-Анджелес|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Поддерживаются |Поддерживаются |Сан-Паулу |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Поддерживаются |Поддерживаются |Амстердам, Чикаго, Далласе, Гонконг ОАР, Лондон, Мумбаи, Silicon впадина, Сингапур, Сидней, Токио, Торонто, Вашингтон (округ Колумбия) |
| **[виасат](http://www.directcloud.viasatbusiness.com/)** | Поддерживаются | Поддерживаются | Вашингтон (округ Колумбия) 2; |
| **[Вокус Group NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | Поддерживаются | Поддерживаются | О., Сидней |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Поддерживаются |Поддерживаются |Amsterdam2, Лондон, Сингапур |
| **[Идея Vodafone](https://discover.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | Поддерживаются | Поддерживаются | Мумбаи, Мумбаи 2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Поддерживаются |Поддерживаются |Амстердам, Чикаго, Далласе, Денвер, Лондон, Лос-Анджелес, Монреаль, Нью-Йорк, Париж, Сиэтл, Silicon впадина, Торонто, Вашингтон (округ Колумбия), штат Вашингтон |

 **+** означает "скоро"

### <a name="national-cloud-environment"></a>Национальная облачная среда

### <a name="us-government-cloud"></a>Облако правительства США

| **Поставщик услуг** | **Microsoft Azure** | **Office 365** | **Расположения** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Поддерживаются |Поддерживаются |Чикаго, Phoenix, Вашингтон (округ Колумбия) |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Поддерживаются |Поддерживаются |Нью Йорк, Phoenix, Сан Сан Антонио, Вашингтон (округ Колумбия) |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Поддерживаются |Поддерживаются |Чикаго, Даллас, Нью-Йорк, Сиэтл, Кремниевая долина, Вашингтон (округ Колумбия) |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Поддерживаются |Поддерживаются |Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Поддерживаются | Поддерживаются | Чикаго, Далласе, Сан Сан Антонио, Сиэтл, Вашингтон (округ Колумбия) |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Поддерживаются |Поддерживаются |Чикаго, Даллас, Нью-Йорк, Кремниевая долина, Вашингтон (округ Колумбия) |

### <a name="china"></a>Китай

| **Поставщик услуг** | **Microsoft Azure** | **Office 365** | **Расположения** |
| --- | --- | --- | --- |
| **China Telecom** |Поддерживаются |Не поддерживается |Пекин, Beijing2, Шанхай, Shanghai2 |
| **[GDS](http://en.gds-services.com/news_detail/newsId=21.html)** |Поддерживаются |Не поддерживается |Пекин 2, Шанхай 2 |

Дополнительные сведения см. на странице [ExpressRoute в Китае](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Германия

| **Поставщик услуг** | **Microsoft Azure** | **Office 365** | **Расположения** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Поддерживаются |Не поддерживается |Франкфурт |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Поддерживаются |Не поддерживается |Франкфурт |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Поддерживаются |Не поддерживается |Берлин |
| **Interxion** |Поддерживаются |Не поддерживается |Франкфурт |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Поддерживаются  | Не поддерживается | Берлин |
| **T-Systems** |Поддерживаются |Не поддерживается |Берлин |

## <a name="connectivity-through-exchange-providers"></a>Подключение через поставщиков Exchange

Вы можете создать подключение, даже если ваш поставщик услуг подключения не указан в предыдущих разделах.

* Узнайте у своего поставщика услуг подключения, подключен ли он к какому-либо Exchange, указанному в таблице выше. Дополнительные сведения об услугах, предлагаемых поставщиками Exchange, см. по ссылкам ниже. Несколько поставщиков услуг подключения уже подключены к серверам Ethernet Exchange.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equnix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/) 
* Обратитесь к своему поставщику услуг подключения, чтобы он расширил вашу сеть, добавив необходимое пиринговое расположение.
  * Убедитесь, что поставщик услуг подключения расширяет границы вашего подключения, сохраняя высокую доступность во избежание влияния единых точек отказа.
* Закажите канал ExpressRoute с Exchange у вашего поставщика услуг подключения для связи с Майкрософт.
  * Для настройки подключения выполните действия, описанные в статье [Создание и изменение канала ExpressRoute с помощью PowerShell](expressroute-howto-circuit-classic.md) .

## <a name="connectivity-through-satellite-operators"></a>Подключение через вспомогательные операторы
Если вы используете удаленную и не имеете возможности оптоволоконного подключения или хотите изучить другие варианты подключения, можно проверить следующие вспомогательные операторы. 

* интелсат
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [виасат](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a>Подключение через дополнительных поставщиков услуг

| **Поставщик услуг подключения** | **Exchange** | **Расположения** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Сингапур |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/expressroute)** | Equinix, Cologix | Торонто, Монреаль |
| **[Alaska Communications](https://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Сиэтл; |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |Нью-Йорк, Вашингтон (округ Колумбия) |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Токио |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Даллас|
| **[Беанфиелд Метроконнект](https://www.beanfield.com/cloud-exchange/)** |Megaport |Торонто|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Лондон |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Амстердам, Франкфурт, Лондон, Сингапур, Вашингтон (округ Колумбия) |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Токио |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Даллас |
| **[Chief](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | Гонконг, САР |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Франкфурт, Германия |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Амстердам | 
| **[Телекоммуникации CMC]( https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | Сингапур | 
| **[Технологии аптум](https://aptum.com/services/cloud/managed-azure/)**| Equinix | Монреаль, Торонто |
| **[кореазуре](http://coreazure.com/expressroute)**| Equinix | Лондон |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | Даллас, Кремниевая долина, Вашингтон, округ Колумбия |
| **[Самые Castle](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | Атланта, Чикаго, Далласе, Лос-Анджелес, Нью-Йорк, Вашингтон (округ Колумбия) |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Даллас |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | Лондон, Сингапур, Вашингтон, округ Колумбия |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Амстердам |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Лондон |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Амстердам |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Город Квебек |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Вашингтон, округ Колумбия |
| **[Gulf Bridge International](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | Амстердам |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | Лондон, Слау |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Торонто |
| **[Каалам телекоммуникации Бахрейн B. S. C](http://www.kalaam-telecom.com/en/inbusiness/expressroute.html)**| Связь уровня 3 |Амстердам |
| **LGA Telecom** |Equinix |Сингапур|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Гонконг, САР 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Сидней |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Амстердам |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Вашингтон, округ Колумбия |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** | Teraco | Кейптаун, Йоханнесбург |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Лондон |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | Амстердам, Франкфурт |
| **[СТОЙКА для телекоммуникации](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Амстердам |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Амстердам, Дублин, Лондон, Париж |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Франкфурт |  
| **Rogers** | Cologix, Equinix | Монреаль, Торонто |
| **[Спектр предприятий](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | Чикаго, Далласе, Лос-Анджелес, Нью-Йорк, Silicon впадина | 
| **[Tamares Telecom](https://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | Лондон | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Амстердам | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Амстердам |
| **[Telekom Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | Амстердам, Франкфурт |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Амстердам |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Торонто | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Сингапур |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | Сан-Паулу |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | Нью-Йорк |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite |Silicon впадина, полупроводниковая 2|
| **Zain** |Equinix |Лондон|
| **[Zertia](https://www.zertia.es)**| Level 3 | Мадрид |
| **[Zirro](https://zirro.com/services/)**| Cologix, Equinix | Монреаль, Торонто |

## <a name="connectivity-through-datacenter-providers"></a>Подключение через поставщиков центров обработки данных

| **Поставщик** | **Exchange** |
| --- | --- |
| **[цирусоне](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Orange, Паккетфабрик |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Orange, Паккетфабрик |
| **[Банк.](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[Найденные](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | Доступ IX, Orange Паккетфабрик |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Orange, Паккетфабрик |
| **[флексентиал](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | Доступ IX, Orange, Паккетфабрик |
| **[Центры обработки данных КТС](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Orange, Паккетфабрик |
| **[Потоковые центры обработки данных]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire Data Centers](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | Доступ IX, Orange, Паккетфабрик |
| **[вксчнже](https://www.vxchnge.com/colocation-services/interconnection)** | Доступ IX, Orange |
| **[T5 Datacenters](https://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Подключение через местные исследования и сети образования (НРЕН)

| **Поставщик**|
| --- |
| **AARNET**| 
| **DeIC, через GÉANT**|
| **GARR, через GÉANT**|
| **GÉANT**|
| **HEAnet, через GÉANT**|
| **Internet2**|
| **JISC**|
| **RedIRIS, через GÉANT**|
| **SINET**|
| **Surfnet, через GÉANT**|

* Если вашего поставщика услуг подключения нет в списке, проверьте, подключены ли они к любому из перечисленных выше партнеров ExpressRoute Exchange.

## <a name="expressroute-system-integrators"></a>Системные интеграторы ExpressRoute
Возможность частного подключения, соответствующего вашим потребностям, будет зависеть от масштаба сети. Чтобы упростить переход на ExpressRoute, вы можете обратиться к одному из системных интеграторов, указанных в таблице ниже.

| **Системный интегратор** | **Континент** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | Европа |
| **[Avanade Inc.](https://www.avanade.com/)** | Азия, Европа, Северная Америка, Южная Америка |
| **[Bright Skies GmbH](https://bskies.io/expressroute)** | Европа
| **[Ensyst](https://www.ensyst.com.au)** | Азия
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | Северная Америка |
| **[FlexManage](https://www.flexmanage.com/cloud)** | Северная Америка |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | Северная Америка |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | Австралия |
| **[MOQdigital](https://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | Австралия |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Европа (Германия) |
| **[Nelite](https://www.nelite.com/offres-services/)** | Европа |
| **[New Signature](https://newsignature.com/technologies/express-route/)** | Европа |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Азия |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Европа |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Северная Америка |
| **[Presidio](https://info.presidio.com/microsoft-azure-expressroute)** | Северная Америка |
| **[sol-tec](https://www.sol-tec.com/services)** | Европа |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Северная Америка |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Австралия |

## <a name="next-steps"></a>Следующие шаги
* Дополнительные сведения об ExpressRoute см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).
* Убедитесь, что выполнены все необходимые условия. Ознакомьтесь с разделом [Предварительные требования и контрольный список для ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Карта расположения"
