---
title: Поставщики услуг подключения и расположения Azure ExpressRoute | Документация Майкрософт
description: В этой статье приведена подробная информация о расположениях, где предлагаются услуги, и способах подключения к регионам Azure. Эта таблица отсортирована по поставщику услуг подключения.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: cherylmc
ms.openlocfilehash: 793d88ec67fbd839851894077e8f5c481b9ebda7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217094"
---
# <a name="expressroute-partners-and-peering-locations"></a>Партнеры и одноранговые расположения ExpressRoute

> [!div class="op_single_selector"]
> * [Расположения по поставщикам](expressroute-locations.md)
> * [Поставщики по расположению](expressroute-locations-providers.md)


The tables in this article provide information on ExpressRoute geographical coverage and locations, ExpressRoute connectivity providers,and ExpressRoute System Integrators (SIs).

> [!Note]
> Azure regions and ExpressRoute locations are two distinct and different concepts, understanding the difference between the two is critical to exploring Azure hybrid networking connectivity. 
>
>

## <a name="azure-regions"></a>Регионы Azure
Azure regions are global datacenters where Azure compute, networking and storage resources are located. When creating an Azure resource, a customer needs to select a resource location. The resource location determines which Azure datacenter (or availability zone) the resource is created in.

## <a name="expressroute-locations"></a>ExpressRoute locations
ExpressRoute locations (sometimes referred to as peering locations or meet-me-locations) are co-location facilities where Microsoft Enterprise Edge (MSEE) devices are located. ExpressRoute locations are the entry point to Microsoft’s network – and are globally distributed, providing customers the opportunity to connect to Microsoft’s network around the world. These locations are where ExpressRoute partners and ExpressRoute Direct customers issue cross connections to Microsoft’s network. In general, the ExpressRoute location does not need to match the Azure region. For example, a customer can create an ExpressRoute circuit with the resource location *East US*, in the *Seattle* Peering location.

Вы сможете получить доступ ко всем службам Azure во всех регионах соответствующего геополитического региона, если вы подключены по меньшей мере к одному расположению ExpressRoute в этом регионе.

## <a name="locations"></a>Azure regions to ExpressRoute locations within a geopolitical region.
В следующей таблице сопоставлены регионы Azure с расположениями ExpressRoute в пределах геополитических регионов.

| **Геополитический регион** | **Регионы Azure** | **Расположения ExpressRoute** |
| --- | --- | --- |
| **Государственные организации Австралии** |Центральная Австралия, Центральная Австралия 2 |Канберра, Канберра 2 |
| **Европа** | Центральная Франция, Южная Франция, Северная Европа, Западная Европа, западная часть Соединенного Королевства, южная часть Соединенного Королевства |Amsterdam, Amsterdam2, Copenhagen, Dublin, Frankfurt, Geneva, London, London2, Marseille, Milan, Munich, Newport(Wales), Oslo, Paris, Stavanger, Stockholm, Zurich |
| **Северная Америка** |Восточная часть США, западная часть США, восточная часть США 2, западная часть США 2, центральная часть США, центрально-южная часть США, центрально-северная часть США, центрально-западная часть США, Центральная Канада, Восточная Канада |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto |
| **Азия** | Восточная Азия, Юго-Восточная Азия |Hong Kong SAR, Jakarta, Kuala Lumpur, Singapore, Singapore2, Taipei |
| **Индия** | Западная Индия, Центральная Индия, Южная Индия |Ченнаи, Ченнаи 2, Мумбаи, Мумбаи 2 |
| **Япония** | Западная Япония, Восточная Япония |Осака, Токио |
| **Океания** | Восточная Австралия, Юго-Восточная Австралия |Auckland, Melbourne, Perth, Sydney, Sydney2 |
| **Южная Корея** | Центральная Корея, Южная Корея |Пусан, Сеул|
| **UAE** | UAE Central, UAE North | Dubai, Dubai2 |
| **ЮАР** | South Africa West, South Africa North |Кейптаун, Йоханнесбург |
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


### <a name="global-commercial-azure"></a>Global commercial Azure

| **Поставщик услуг** | **Microsoft Azure** | **Office 365**  | **Расположения** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |Поддерживается |Поддерживается |Мельбурн, Сидней |
| **[Airtel](https://www.airtel.in/business/#/)** | Поддерживается | Поддерживается | Ченнаи 2, Мумбаи 2 |
| **[Aryaka Networks](https://www.aryaka.com/)** |Поддерживается |Поддерживается |Amsterdam, Chicago, Dallas, Hong Kong SAR, Sao Paulo, Seattle, Silicon Valley, Singapore, Tokyo, Washington DC |
| **[Ascenty Data Centers](https://www.ascenty.com/en/cloud/microsoft-express-route)** |Поддерживается |Поддерживается |Сан-Паулу |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Поддерживается |Поддерживается |Амстердам, Вашингтон (округ Колумбия), Даллас, Кремниевая долина, Лондон, Сидней, Сингапур, Токио, Торонто, Чикаго |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Поддерживается |Поддерживается |Квебек, Монреаль, Торонто |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/bt-compute-for-microsoft-azure)** |Поддерживается |Поддерживается |Amsterdam, Hong Kong SAR, Johannesburg, London, Newport(Wales), Sao Paulo, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |Поддерживается |Поддерживается |Майами |
| **CDC** | Поддерживается | Поддерживается | Канберра, Канберра 2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Поддерживается |Поддерживается |Amsterdam2, Chicago, Hong Kong, Las Vegas, New York, Paris, San Antonio, Silicon Valley, Tokyo, Toronto, Washington DC |
| **[Chief Telecom](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** |Поддерживается |Поддерживается |Hong Kong, Taipei |
| **China Telecom Global** |Поддерживается |Поддерживается |Гонконг, САР |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |Поддерживается |Поддерживается |Чикаго, Даллас, Монреаль, Торонто, Вашингтон (округ Колумбия) |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Поддерживается |Поддерживается |Amsterdam, Amsterdam2, Dublin, London, Newport, New York, Osaka, Paris, Silicon Valley, Silicon Valley2, Singapore2, Tokyo |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Поддерживается |Поддерживается |Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Поддерживается |Поддерживается |Chicago, Denver, Los Angeles, New York, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/directcloud/find-a-cloud-service/detail/microsoft-azure)** | Поддерживается |Поддерживается |Amsterdam2, Frankfurt, Marseille|
| **[Devoli](https://devoli.com/expressroute)** | Поддерживается |Поддерживается | Auckland, Melbourne, Sydney |
| **du datamena** |Поддерживается |Поддерживается | Dubai2 |
| **eir** |Поддерживается |Поддерживается |Дублин|
| **[Epsilon Global Communications](https://www.epsilontel.com/solutions/direct-cloud-connect)** |Поддерживается |Поддерживается |Сингапур, Сингапур 2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Поддерживается |Поддерживается |Amsterdam, Atlanta, Chicago, Dallas, Dublin,  Hong Kong SAR, London, London2, Los Angeles, Melbourne, Miami, New York, Osaka, Paris, Sao Paulo, Seattle, Silicon Valley, Singapore, Stockholm, Sydney, Tokyo, Toronto, Washington DC |
| **Etisalat UAE** |Поддерживается |Поддерживается |Дубай|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |Поддерживается |Поддерживается |Amsterdam, Amsterdam2, Dublin, London |
| **FarEasTone** |Поддерживается |Поддерживается |Тайбэй;|
| **GÉANT** |Поддерживается |Поддерживается |Амстердам |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Поддерживается| Поддерживается | Ченнаи, Мумбаи |
| **[InterCloud](https://www.intercloud.com/)** |Поддерживается |Поддерживается |Amsterdam, Chicago, London, New York, Paris, Silicon Valley, Singapore, Washington DC, Zurich |
| **[Internet2](https://www.internet2.edu/products-services/cloud-services-applications/microsoft-azure/#service-cloud-connect)** |Поддерживается |Поддерживается |Chicago, Dallas, Silicon Valley, Washington DC |
| **[Internet Initiative Japan Inc. - IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Поддерживается |Поддерживается |Осака, Токио |
| **[Internet Solutions - Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Поддерживается |Поддерживается |Кейптаун, Йоханнесбург, Лондон |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Поддерживается |Поддерживается |Amsterdam, Amsterdam2, Copenhagen, Dublin, Frankfurt, London, Marseille, Paris, Zurich |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Поддерживается |Поддерживается | Amsterdam, London2, Silicon Valley, Toronto |
| **Jaguar Network** |Поддерживается |Поддерживается |Марсель|
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |Поддерживается |Поддерживается |Лондон |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Поддерживается |Поддерживается |Сеул |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | Поддерживается |Поддерживается |Auckland, Sydney |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Поддерживается | Поддерживается | Амстердам | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Поддерживается |Поддерживается |Амстердам, Вашингтон (округ Колумбия), Даллас, Кремниевая долина, Лондон, Ньюпорт (Уэльс), Сан-Паулу, Сингапур, Сиэтл, Чикаго |
| **LG CNS** |Поддерживается |Поддерживается |Пусан, Сеул |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Поддерживается |Поддерживается |Кейптаун, Йоханнесбург |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Поддерживается |Поддерживается |Amsterdam, Atlanta, Auckland, Chicago, Dallas, Denver, Dubai2, Dublin, Hong Kong SAR, Las Vegas, London, Los Angeles, Melbourne, Miami, Montreal, New York, Perth, Quebec City, San Antonio, Seattle, Silicon Valley, Singapore, Singapore2, Sydney, Toronto, Washington DC |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** |Поддерживается |Поддерживается |Лондон |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |Поддерживается |Поддерживается |Dallas, Los Angeles, Miami, Sao Paulo, Washington DC |
| **[Next Generation Data](https://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Поддерживается |Поддерживается |Ньюпорт (Уэльс) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Поддерживается |Поддерживается |Melbourne, Perth, Sydney, Sydney2 |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Поддерживается |Поддерживается |Amsterdam, Hong Kong SAR, London, Los Angeles, Osaka, Singapore, Sydney, Tokyo, Washington DC |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |Поддерживается |Поддерживается |Токио |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Поддерживается |Поддерживается |Осака |
| **[Optus](https://www.optus.com.au/enterprise/)** |Поддерживается |Поддерживается |Мельбурн, Сидней |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |Поддерживается |Поддерживается |Amsterdam, Frankfurt, Hong Kong SAR, Johannesburg, London, Paris, Sao Paulo, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | Поддерживается | Поддерживается | Dubai2 |
| **[PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)** |Поддерживается |Поддерживается |Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Поддерживается |Поддерживается |Chicago, Hong Kong SAR, London |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Поддерживается |Поддерживается |Сеул |
| **[SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | Поддерживается |Поддерживается | Вашингтон, округ Колумбия |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Поддерживается |Поддерживается |Ченнаи, Мумбаи 2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Поддерживается |Поддерживается |Сингапур, Сингапур 2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Поддерживается |Поддерживается |Осака, Токио |
| **[Spark NZ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |Поддерживается |Поддерживается |Auckland, Sydney |
| **[Спринт](https://business.sprint.com/solutions/cloud-networking/)** |Поддерживается |Поддерживается |Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | Поддерживается | Поддерживается | Цюрих |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Поддерживается |Поддерживается |Amsterdam, Chennai, Hong Kong SAR, London, Mumbai, Sao Paulo, Silicon Valley, Singapore, Washington DC |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Поддерживается |Поддерживается |Амстердам, Сан-Паулу |
| **[Telehouse — KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Поддерживается |Поддерживается |London, London2 |
| **Telenor** |Поддерживается |Поддерживается |Amsterdam, London, Oslo |
| **[Telia Carrier](https://teliacarrier.com/our-services/connectivity/cloud-connect.html?title=Cloud%20Connect)** | Поддерживается | Поддерживается |Amsterdam, Chicago, Dallas, Hong Kong, London, Paris, Silicon Valley, Stockholm, Washington DC |
| **Telmex Uninet**| Поддерживается | Поддерживается | Даллас |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Поддерживается |Поддерживается |Мельбурн, Сингапур, Сидней |
| **[Telus](https://www.telus.com)** |Поддерживается |Поддерживается |Montreal, Seattle, Toronto |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Поддерживается |Поддерживается |Кейптаун, Йоханнесбург |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/cloud-interconnect)** | Поддерживается | Поддерживается | Куала-Лумпур |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |Поддерживается |Поддерживается |Даллас, Лос-Анджелес|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Поддерживается |Поддерживается |Сан-Паулу |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Поддерживается |Поддерживается |Amsterdam, Chicago, Dallas, Hong Kong SAR, London, Mumbai, Silicon Valley, Singapore, Sydney, Tokyo, Toronto, Washington DC |
| **[Viasat](http://www.directcloud.viasatbusiness.com/)** | Поддерживается | Поддерживается | Вашингтон (округ Колумбия) 2; |
| **[Vocus Group NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | Поддерживается | Поддерживается | Auckland, Sydney |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Поддерживается |Поддерживается |Amsterdam2, London, Singapore |
| **[Vodafone Idea](https://discover.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | Поддерживается | Поддерживается | Мумбаи, Мумбаи 2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Поддерживается |Поддерживается |Amsterdam, Chicago, Dallas, Denver, London, Los Angeles, Montreal, New York, Paris, Seattle, Silicon Valley, Toronto, Washington DC, Washington DC2 |

 **+** означает "скоро"

### <a name="national-cloud-environment"></a>Национальная облачная среда

Azure national clouds are isolated from each other and from global commerical Azure. ExpressRoute for one Azure cloud can't connect to the Azure regions in the others. 

### <a name="us-government-cloud"></a>Облако правительства США

| **Поставщик услуг** | **Microsoft Azure** | **Office 365** | **Расположения** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Поддерживается |Поддерживается |Chicago, Phoenix,  Washington DC |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Поддерживается |Поддерживается |New York, Phoenix, San Antonio, Washington DC |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Поддерживается |Поддерживается |Чикаго, Даллас, Нью-Йорк, Сиэтл, Кремниевая долина, Вашингтон (округ Колумбия) |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Поддерживается |Поддерживается |Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Поддерживается | Поддерживается | Chicago, Dallas, San Antonio, Seattle, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Поддерживается |Поддерживается |Чикаго, Даллас, Нью-Йорк, Кремниевая долина, Вашингтон (округ Колумбия) |

### <a name="china"></a>Китай

| **Поставщик услуг** | **Microsoft Azure** | **Office 365** | **Расположения** |
| --- | --- | --- | --- |
| **China Telecom** |Поддерживается |Не поддерживается |Beijing, Beijing2, Shanghai, Shanghai2 |
| **[GDS](http://en.gds-services.com/news_detail/newsId=21.html)** |Поддерживается |Не поддерживается |Пекин 2, Шанхай 2 |

Дополнительные сведения см. на странице [ExpressRoute в Китае](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Германия

| **Поставщик услуг** | **Microsoft Azure** | **Office 365** | **Расположения** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Поддерживается |Не поддерживается |Франкфурт |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Поддерживается |Не поддерживается |Франкфурт |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Поддерживается |Не поддерживается |Берлин |
| **Interxion** |Поддерживается |Не поддерживается |Франкфурт |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Поддерживается  | Не поддерживается | Берлин |
| **T-Systems** |Поддерживается |Не поддерживается |Берлин |

## <a name="connectivity-through-exchange-providers"></a>Connectivity through Exchange providers

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

## <a name="connectivity-through-satellite-operators"></a>Connectivity through satellite operators
If you are remote and don't have fiber connectivity or you want to explore other connectivity options you can check the following satellite operators. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a>Connectivity through additional service providers

| **Поставщик услуг подключения** | **Exchange** | **Расположения** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Сингапур |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/expressroute)** | Equinix, Cologix | Торонто, Монреаль |
| **[Alaska Communications](https://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Сиэтл; |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |Нью-Йорк, Вашингтон (округ Колумбия) |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Токио |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Даллас|
| **[Beanfield Metroconnect](https://www.beanfield.com/cloud-exchange/)** |Megaport |Торонто|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Лондон |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Амстердам, Франкфурт, Лондон, Сингапур, Вашингтон (округ Колумбия) |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Токио |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Даллас |
| **[Chief](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | Гонконг, САР |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Франкфурт, Германия |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Амстердам | 
| **[CMC Telecom](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | Сингапур | 
| **[Aptum Technologies](https://aptum.com/services/cloud/managed-azure/)**| Equinix | Монреаль, Торонто |
| **[CoreAzure](http://www.coreazure.com/expressroute/)**| Equinix | Лондон |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | Даллас, Кремниевая долина, Вашингтон, округ Колумбия |
| **[Crown Castle](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | Atlanta, Chicago, Dallas, Los Angeles, New York, Washington DC |
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
| **[Kaalam Telecom Bahrain B.S.C](http://www.kalaam-telecom.com/en/inbusiness/expressroute.html)**| Level 3 Communications |Амстердам |
| **LGA Telecom** |Equinix |Сингапур|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Гонконг, САР 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Сидней |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Амстердам |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Вашингтон, округ Колумбия |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** | Teraco | Кейптаун, Йоханнесбург |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Лондон |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | Амстердам, Франкфурт |
| **[POST Telecom Luxembourg](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Амстердам |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Амстердам, Дублин, Лондон, Париж |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Франкфурт |  
| **Rogers** | Cologix, Equinix | Монреаль, Торонто |
| **[Spectrum Enterprise](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | Chicago, Dallas, Los Angeles, New York, Silicon Valley | 
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
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite |Silicon Valley, Silicon Valley 2|
| **Zain** |Equinix |Лондон|
| **[Zertia](https://www.zertia.es)**| Level 3 | Мадрид |
| **[Zirro](https://zirro.com/services/)**| Cologix, Equinix | Монреаль, Торонто |

## <a name="connectivity-through-datacenter-providers"></a>Connectivity through datacenter providers

| **Поставщик** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport, PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport, PacketFabric |
| **[Databank](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX Reach, Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport, PacketFabric |
| **[Flexential](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX Reach, Megaport, PacketFabric |
| **[QTS Data Centers](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport, PacketFabric |
| **[Stream Data Centers]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire Data Centers](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach, Megaport, PacketFabric |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX Reach, Megaport |
| **[T5 Datacenters](https://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Connectivity through National Research and Education Networks (NREN)

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
| **[Nelite](https://www.exakis-nelite.com/offres/)** | Европа |
| **[New Signature](https://newsignature.com/technologies/express-route/)** | Европа |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Азия |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Европа |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Северная Америка |
| **[Presidio](https://info.presidio.com/microsoft-azure-expressroute)** | Северная Америка |
| **[sol-tec](https://www.sol-tec.com/what-we-do/)** | Европа |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Южная Америка |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Австралия |

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения об ExpressRoute см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).
* Убедитесь, что выполнены все необходимые условия. См. статью [Предварительные требования и контрольный список для ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Карта расположения"
