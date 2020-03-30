---
title: Поставщики услуг подключения и расположения Azure ExpressRoute | Документация Майкрософт
description: В этой статье приведена подробная информация о расположениях, где предлагаются услуги, и способах подключения к регионам Azure. Эта таблица отсортирована по поставщику услуг подключения.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/25/2020
ms.author: cherylmc
ms.openlocfilehash: 41e12a93f333936468cbdfbe7aa6bfa6e0b7e8ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278760"
---
# <a name="expressroute-partners-and-peering-locations"></a>Партнеры и одноранговые расположения ExpressRoute

> [!div class="op_single_selector"]
> * [Расположение по провайдеру](expressroute-locations.md)
> * [Поставщики по расположению](expressroute-locations-providers.md)


Таблицы в этой статье предоставляют информацию о географическом охвате и местоположении ExpressRoute, поставщиках услуг подключения ExpressRoute и интеграторах системы ExpressRoute (SIs).

> [!Note]
> Регионы Azure и местоположения ExpressRoute представляют собой две различные концепции, понимание разницы между ними имеет решающее значение для изучения гибридной сети Azure. 
>
>

## <a name="azure-regions"></a>Регионы Azure
Регионы Azure — это глобальные центры обработки данных, в которых расположены вычислительные, сетевые и хранилища Azure. При создании ресурса Azure клиенту необходимо выбрать местоположение ресурса. Местоположение ресурса определяет, в каком центре обработки данных Azure (или зоне доступности) создается ресурс.

## <a name="expressroute-locations"></a>Расположение ExpressRoute
Местоположения ExpressRoute (иногда называемые пиринговыми локациями или местоположениями встреч) — это объекты совместного размещения, на которых расположены устройства Microsoft Enterprise Edge (MSEE). Расположение ExpressRoute является точкой входа в сеть корпорации Майкрософт и распространяется по всему миру, предоставляя клиентам возможность подключения к сети Майкрософт по всему миру. В этих местах партнеры ExpressRoute и клиенты ExpressRoute Direct выдают перекрестное подключение к сети Майкрософт. Как правило, расположение ExpressRoute не должно соответствовать региону Azure. Например, клиент может создать схему ExpressRoute с расположением ресурсов *East US,* в месте Seattle *Peering.*

Вы сможете получить доступ ко всем службам Azure во всех регионах соответствующего геополитического региона, если вы подключены по меньшей мере к одному расположению ExpressRoute в этом регионе.

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Регионы Azure с расположениями ExpressRoute в пределах геополитических регионов
В следующей таблице сопоставлены регионы Azure с расположениями ExpressRoute в пределах геополитических регионов.

| **Геополитический регион** | **Лазурные регионы** | **Расположение ExpressRoute** |
| --- | --- | --- |
| **Государственные организации Австралии** |Центральная Австралия, Центральная Австралия 2 |Канберра, Канберра 2 |
| **Европа** | Франция Центральная, Франция юг, Германия Север, Германия Западная Центральная, Северная Европа, Норвегия Восток, Норвегия Запад, Швейцария Север, Швейцария Запад, Великобритания Запад, Великобритания на юге, Западная Европа |Амстердам, Амстердам2, Копенгаген, Дублин, Франкфурт, Ньюген, Лондон, Лондон2, Марсель, Милан, Мюнхен, Ньюпорт (Уэльс), Осло, Париж, Ставангер, Стокгольм, Цюрих |
| **Северная Америка** |Восточная часть США, западная часть США, восточная часть США 2, западная часть США 2, центральная часть США, центрально-южная часть США, центрально-северная часть США, центрально-западная часть США, Центральная Канада, Восточная Канада |Атланта, Чикаго, Даллас, Денвер, Лас-Вегас, Лос-Анджелес, Майами, Нью-йорк, Сан-Антонио, Сиэтл, Силиконовая долина, Силиконовая долина2, Вашингтон, Вашингтон, округ Колумбия, Монреаль, Квебек, Торонто, Ванкувер |
| **Азии** | Восточная Азия, Юго-Восточная Азия | Бангкок, Гонконг, Гонконг2, Джакарта, Куала-Лумпур, Сингапур, Сингапур2, Тайбэй |
| **Индия** | Западная Индия, Центральная Индия, Южная Индия |Ченнаи, Ченнаи 2, Мумбаи, Мумбаи 2 |
| **Япония** | Западная Япония, Восточная Япония |Осака, Токио2 |
| **Океания** | Восточная Австралия, Юго-Восточная Австралия |Окленд, Мельбурн, Перт, Сидней, Сидней2 |
| **Южная Корея** | Республика Корея, центральный регион, Республика Корея, южный регион |Пусан, Сеул|
| **ОАЭ** | Центральный ОАЭ, ОАЭ Север | Дубай, Дубай2 |
| **ЮАР** | Западная часть Африки, северная часть Африки |Кейптаун, Йоханнесбург |
| **Южная Америка** | Южная Бразилия |Сан-Паулу |


## <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Регионы и геополитические границы для национальных облаков
В таблице ниже содержатся сведения о регионах и геополитических границах для национальных облаков.

| **Геополитический регион** | **Лазурные регионы** | **Расположение ExpressRoute** |
| --- | --- | --- |
| **Облако US Gov ** |US Gov (Аризона), US Gov (Айова), US Gov (Техас), US Gov (Вирджиния), центральный регион US DoD, восточный регион US DoD  |Атланта, Чикаго, Даллас, Нью-йорк, Финикс, Сан-Антонио, Сиэтл, Силиконовая долина, Вашингтон |
| **Восточный Китай** |"Восточный Китай", "Восточный Китай 2" |Шанхай, Шанхай 2 |
| **Северный Китай** |"Северный Китай", "Северный Китай 2" |Пекин, Пекин 2 |
| **Германия** |Центральная Германия, восточная Германия |Берлин, Франкфурт |

В стандартном номере SKU ExpressRoute подключение между геополитическими регионами не поддерживается. Для поддержки глобальных подключений необходимо включить надстройку ExpressRoute класса "Премиум". Подключение к национальным облачным средам не поддерживается. При необходимости вы можете работать с поставщиками услуг подключения.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>Поставщики услуг подключения ExpressRoute

В следующей таблице показаны расположения по поставщикам услуг. См. дополнительные сведения о [службах доступных поставщиков по расположению](expressroute-locations-providers.md).


### <a name="global-commercial-azure"></a>Глобальный коммерческий Azure

| **Поставщик услуг** | **Microsoft Azure** | **Офис 365**  | **Местах** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |Поддерживается |Поддерживается |Мельбурн, Сидней |
| **[Airtel](https://www.airtel.in/business/#/)** | Поддерживается | Поддерживается | Ченнаи 2, Мумбаи 2 |
| **[AIS](https://business.ais.co.th/solution/en/azure-expressroute.html)** | Поддерживается | Поддерживается | Бангкок |
| **[Aryaka Networks](https://www.aryaka.com/)** |Поддерживается |Поддерживается |Амстердам, Чикаго, Даллас, Гонконг SAR, Сан-Паулу, Сиэтл, Силиконовая долина, Сингапур, Токио, Вашингтон |
| **[Ascenty Data Centers](https://www.ascenty.com/en/cloud/microsoft-express-route)** |Поддерживается |Поддерживается |Сан-Паулу |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Поддерживается |Поддерживается |Амстердам, Вашингтон (округ Колумбия), Даллас, Кремниевая долина, Лондон, Сидней, Сингапур, Токио, Торонто, Чикаго |
| **[В ТОКИО](https://www.attokyo.com/service/cloudsconnection/forazure.html)** | Поддерживается | Поддерживается | Токио2 |
| **[ББИКС](https://www.bbix.net/en/service/ix/)** | Поддерживается | Поддерживается | Токио |
| **[BCX](https://www.bcx.co.za/solutions)** |Поддерживается |Поддерживается |Кейптаун|
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Поддерживается |Поддерживается |Квебек, Монреаль, Торонто |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/cloud-connect-azure)** |Поддерживается |Поддерживается |Амстердам, Гонконг SAR, Иоганнесбург, Лондон, Ньюпорт (Уэльс), Сан-Паулу, Силиконовая долина, Сингапур, Сидней, Токио, Вашингтон |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |Поддерживается |Поддерживается |Майами |
| **CDC** | Поддерживается | Поддерживается | Канберра, Канберра 2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Поддерживается |Поддерживается |Амстердам2, Чикаго, Франкфурт, Гонконг, Лас-Вегас, Лондон2, Нью-йорк, Париж, Сан-Антонио, Силиконовая долина, Токио, Торонто, Вашингтон, Вашингтон, округ Колумбия2 |
| **[Chief Telecom](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** |Поддерживается |Поддерживается |Гонконг, Тайбэй |
| **Китай Мобильный Интернешнл** |Поддерживается |Поддерживается | Сингапур |
| **China Telecom Global** |Поддерживается |Поддерживается |Гонконг, САР |
| **Китай Unicom Глобальный** |Поддерживается |Поддерживается | Сингапур 2 |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |Поддерживается |Поддерживается |Чикаго, Даллас, Монреаль, Торонто, Вашингтон (округ Колумбия) |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Поддерживается |Поддерживается |Амстердам, Амстердам2, Чикаго, Дублин, Франкфурт, Лондон, Лондон2, Ньюпорт, Нью-йорк, Осака, Париж, Силиконовая долина, Силиконовая долина2, Сингапур2, Токио, Вашингтон |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Поддерживается |Поддерживается |Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Поддерживается |Поддерживается |Чикаго, Денвер, Лос-Анджелес, Нью-йорк, Силиконовая долина, Силиконовая долина2, Вашингтон, вашингтон, округ Колумбия2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/directcloud/find-a-cloud-service/detail/microsoft-azure)** | Поддерживается |Поддерживается |Амстердам2, Франкфурт, Марсель|
| **[Деволи](https://devoli.com/expressroute)** | Поддерживается |Поддерживается | Окленд, Мельбурн, Сидней |
| **du datamena** |Поддерживается |Поддерживается | Дубай2 |
| **eir** |Поддерживается |Поддерживается |Дублин|
| **[Эпсилон Глобал Коммуникации](https://www.epsilontel.com/solutions/direct-cloud-connect)** |Поддерживается |Поддерживается |Сингапур, Сингапур 2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Поддерживается |Поддерживается |Амстердам, Атланта, Чикаго, Даллас, Дублин, Франкфурт, Санкт-Петербург, Гонконг, Лондон, Лондон2, Лос-Анджелес, Мельбурн, Майами, Нью-йорк, Осака, Париж, Сан-Паулу, Сиэтл, Силиконовая долина, Сингапур, Стокгольм, Сидней, Токио, Торонто, Вашингтон |
| **Этисалат ОАЭ** |Поддерживается |Поддерживается |Дубай|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |Поддерживается |Поддерживается |Амстердам, Амстердам2, Дублин, Лондон |
| **ФарИсТон** |Поддерживается |Поддерживается |Тайбэй|
| **GÉANT** |Поддерживается |Поддерживается |Амстердам, Франкфурт, Марсель |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Поддерживается| Поддерживается | Ченнаи, Мумбаи |
| **Интелсат** | Поддерживается | Поддерживается | Вашингтон, округ Колумбия 2 |
| **[InterCloud](https://www.intercloud.com/)** |Поддерживается |Поддерживается |Амстердам, Чикаго, Гонконг, Лондон, Нью-йорк, Париж, Силиконовая долина, Сингапур, Вашингтон, Цюрих |
| **[Интернет2](https://www.internet2.edu/products-services/cloud-services-applications/microsoft-azure/#service-cloud-connect)** |Поддерживается |Поддерживается |Чикаго, Даллас, Силиконовая долина, Вашингтон |
| **[Internet Initiative Japan Inc. - IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Поддерживается |Поддерживается |Осака, Токио |
| **[Internet Solutions - Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Поддерживается |Поддерживается |Кейптаун, Йоханнесбург, Лондон |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Поддерживается |Поддерживается |Амстердам, Амстердам2, Копенгаген, Дублин, Франкфурт, Лондон, Марсель, Париж, Цюрих |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Поддерживается |Поддерживается | Амстердам, Лондон2, Силиконовая долина, Торонто, Вашингтон |
| **Сеть Jaguar** |Поддерживается |Поддерживается |Марсель|
| **[Джиск](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |Поддерживается |Поддерживается |London |
| **[КИНХ](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Поддерживается |Поддерживается |Сеул |
| **[Кордия](https://www.kordia.co.nz/cloudconnect)** | Поддерживается |Поддерживается |Окленд, Сидней |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Поддерживается | Поддерживается | Амстердам |
| **[Кт](https://cloud.kt.com/portal/ktcloudportal.epc.productintro.partnershipcloud_ConnectHub.html)** | Поддерживается | Поддерживается | Сеул |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Поддерживается |Поддерживается |Амстердам, Вашингтон (округ Колумбия), Даллас, Кремниевая долина, Лондон, Ньюпорт (Уэльс), Сан-Паулу, Сингапур, Сиэтл, Чикаго |
| **LG CNS** |Поддерживается |Поддерживается |Пусан, Сеул |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Поддерживается |Поддерживается |Кейптаун, Йоханнесбург |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Поддерживается |Поддерживается |Амстердам, Атланта, Окленд, Чикаго, Даллас, Денвер, Дубай2, Дублин, Франкфурт, Нью-йорк, Гонконг SAR, Лас-Вегас, Лондон, Лондон2, Лос-Анджелес, Мельбурн, Майами, Монреаль, Нью-йорк, Осло, Перт, Квебек, Сан-Антонио, Сиэтл, Силиконовая долина, Сингапур, Сингапур2, Сидней, Сидней2, Токио, Торонто, Вашингтон, Цюрих |
| **[Mtn](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** |Поддерживается |Поддерживается |London |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |Поддерживается |Поддерживается |Даллас, Лос-Анджелес, Майами, Сан-Паулу, Вашингтон |
| **[Данные следующего поколения](https://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Поддерживается |Поддерживается |Ньюпорт (Уэльс) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Поддерживается |Поддерживается |Мельбурн, Перт, Сидней, Сидней2 |
| **[NTT Связи](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Поддерживается |Поддерживается |Амстердам, Гонконг SAR, Лондон, Лос-Анджелес, Осака, Сингапур, Сидней, Токио, Вашингтон |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |Поддерживается |Поддерживается |Токио |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Поддерживается |Поддерживается |Осака |
| **[Ooredoo Облако Подключиться](https://www.ooredoo.qa/portal/OoredooQatar/cloud-connect-expressroute)** |Поддерживается |Поддерживается |Марсель |
| **[Optus](https://www.optus.com.au/enterprise/)** |Поддерживается |Поддерживается |Мельбурн, Сидней |
| **[Оранжевый](https://www.orange-business.com/en/products/business-vpn-galerie)** |Поддерживается |Поддерживается |Амстердам, Амстердам2, Дубай2, Франкфурт, Гонконг SAR, Иоганнесбург, Лондон, Париж, Сан-Паулу, Силиконовая долина, Сингапур, Сидней, Токио, Вашингтон |
| **[Орикском](https://www.orixcom.com/cloud-solutions/)** | Поддерживается | Поддерживается | Дубай2 |
| **[PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)** |Поддерживается |Поддерживается |Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Поддерживается |Поддерживается |Чикаго, Гонконг SAR, Лондон |
| **[Седжон Телеком](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Поддерживается |Поддерживается |Сеул |
| **[Ses](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | Поддерживается |Поддерживается | Вашингтон, округ Колумбия |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Поддерживается |Поддерживается |Ченнаи, Мумбаи 2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Поддерживается |Поддерживается |Сингапур, Сингапур 2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Поддерживается |Поддерживается |Осака, Токио |
| **[Искра НЗ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |Поддерживается |Поддерживается |Окленд, Сидней |
| **[Спринт](https://business.sprint.com/solutions/cloud-networking/)** |Поддерживается |Поддерживается |Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | Поддерживается | Поддерживается | Цюрих |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Поддерживается |Поддерживается |Амстердам, Ченнаи, Гонконг SAR, Лондон, Мумбаи, Сан-Паулу, Силиконовая долина, Сингапур, Вашингтон |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Поддерживается |Поддерживается |Амстердам, Сан-Паулу |
| **[Telehouse — KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Поддерживается |Поддерживается |Лондон, Лондон2 |
| **Telenor** |Поддерживается |Поддерживается |Амстердам, Лондон, Осло |
| **[Тэлия Кэрриер](https://www.teliacarrier.com/)** | Поддерживается | Поддерживается |Амстердам, Чикаго, Даллас, Франкфурт, Гонконг, Лондон, Осло, Париж, Силиконовая долина, Стокгольм, Вашингтон |
| **Telmex Uninet**| Поддерживается | Поддерживается | Даллас |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Поддерживается |Поддерживается |Мельбурн, Сингапур, Сидней |
| **[Telus](https://www.telus.com)** |Поддерживается |Поддерживается |Монреаль, Сиэтл, Торонто |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Поддерживается |Поддерживается |Кейптаун, Йоханнесбург |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/cloud-interconnect)** | Поддерживается | Поддерживается | Куала-Лумпур |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |Поддерживается |Поддерживается |Даллас, Лос-Анджелес|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Поддерживается |Поддерживается |Сан-Паулу |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Поддерживается |Поддерживается |Амстердам, Чикаго, Даллас, Гонконг SAR, Лондон, Мумбаи, Силиконовая долина, Сингапур, Сидней, Токио, Торонто, Вашингтон |
| **[Viasat](http://www.directcloud.viasatbusiness.com/)** | Поддерживается | Поддерживается | Вашингтон, округ Колумбия 2 |
| **[Группа Компаний «Вокус» НЗ](https://www.vocus.co.nz/business/cloud-data-centres)** | Поддерживается | Поддерживается | Окленд, Сидней |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Поддерживается |Поддерживается |Амстердам2, Лондон, Сингапур |
| **[Идея Vodafone](https://discover.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | Поддерживается | Поддерживается | Мумбаи, Мумбаи 2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Поддерживается |Поддерживается |Амстердам, Чикаго, Даллас, Денвер, Лондон, Лос-Анджелес, Монреаль, Нью-йорк, Париж, Сиэтл, Силиконовая долина, Торонто, Вашингтон, Вашингтон, округ Колумбия2 |

 **+** обозначает скоро

### <a name="national-cloud-environment"></a>Национальная облачная среда

Национальные облака Azure изолированы друг от друга и от глобального коммерческого Azure. ExpressRoute для одного облака Azure не может подключиться к регионам Azure в других. 

### <a name="us-government-cloud"></a>Облако US Gov 

| **Поставщик услуг** | **Microsoft Azure** | **Офис 365** | **Местах** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Поддерживается |Поддерживается |Чикаго, Финикс, Силиконовая долина, Вашингтон |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Поддерживается |Поддерживается |Нью-йорк, Финикс, Сан-Антонио, Вашингтон, округ Колумбия |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Поддерживается |Поддерживается |Атланта, Чикаго, Даллас, Нью-йорк, Сиэтл, Силиконовая долина, Вашингтон |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Поддерживается |Поддерживается |Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Поддерживается | Поддерживается | Чикаго, Даллас, Сан-Антонио, Сиэтл, Вашингтон |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Поддерживается |Поддерживается |Чикаго, Даллас, Нью-Йорк, Кремниевая долина, Вашингтон (округ Колумбия) |

### <a name="china"></a>Китай

| **Поставщик услуг** | **Microsoft Azure** | **Офис 365** | **Местах** |
| --- | --- | --- | --- |
| **China Telecom** |Поддерживается |Не поддерживается |Пекин, Пекин2, Шанхай, Шанхай2 |
| **Китай Unicom** | Поддерживается | Не поддерживается | Пекин 2 |
| **[Gds](http://www.gds-services.com/en/about_2.html)** |Поддерживается |Не поддерживается |Пекин 2, Шанхай 2 |

Дополнительные сведения см. на странице [ExpressRoute в Китае](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Германия

| **Поставщик услуг** | **Microsoft Azure** | **Офис 365** | **Местах** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Поддерживается |Не поддерживается |Франкфурт |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Поддерживается |Не поддерживается |Франкфурт |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Поддерживается |Не поддерживается |Берлин |
| **Interxion** |Поддерживается |Не поддерживается |Франкфурт |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Поддерживается  | Не поддерживается | Берлин |
| **T-Systems** |Поддерживается |Не поддерживается |Берлин |

## <a name="connectivity-through-exchange-providers"></a>Связь через поставщиков Exchange

Вы можете создать подключение, даже если ваш поставщик услуг подключения не указан в предыдущих разделах.

* Узнайте у своего поставщика услуг подключения, подключен ли он к какому-либо Exchange, указанному в таблице выше. Дополнительные сведения об услугах, предлагаемых поставщиками Exchange, см. по ссылкам ниже. Несколько поставщиков услуг подключения уже подключены к серверам Ethernet Exchange.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equnix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure) 
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)

* Обратитесь к своему поставщику услуг подключения, чтобы он расширил вашу сеть, добавив необходимое пиринговое расположение.
  * Убедитесь, что поставщик услуг подключения расширяет границы вашего подключения, сохраняя высокую доступность во избежание влияния единых точек отказа.
* Закажите канал ExpressRoute с Exchange у вашего поставщика услуг подключения для связи с Майкрософт.
  * Для настройки подключения выполните действия, описанные в статье [Создание и изменение канала ExpressRoute с помощью PowerShell](expressroute-howto-circuit-classic.md) .

## <a name="connectivity-through-satellite-operators"></a>Связь через спутниковых операторов
Если вы удалены и не имеете подключения к волокон новкоего или вы хотите изучить другие варианты подключения, вы можете проверить следующие спутниковые операторы. 

* Интелсат
* [Ses](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a>Связь с дополнительными поставщиками услуг

| **Поставщик услуг подключения** | **Exchange** | **Местах** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Сингапур |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/expressroute)** | Equinix, Cologix | Торонто, Монреаль |
| **[Alaska Communications](https://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |Нью-Йорк, Вашингтон (округ Колумбия) |
| **[Корпорация «Артерия Сетей»](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Токио |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Даллас|
| **[Бинфилд Метроконнект](https://www.beanfield.com/business/cloud-connect)** |Megaport |Toronto|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | London |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Амстердам, Франкфурт, Лондон, Сингапур, Вашингтон (округ Колумбия) |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Токио |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Даллас |
| **[Chief](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | Гонконг, САР |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Франкфурт, Германия |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Амстердам | 
| **[CMC Телеком](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | Сингапур | 
| **[Аптум Технологии](https://aptum.com/services/cloud/managed-azure/)**| Equinix | Монреаль, Торонто |
| **[CoreAzure](http://www.coreazure.com/expressroute/)**| Equinix | London |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | Даллас, Кремниевая долина, Вашингтон, округ Колумбия |
| **[Замок Корона](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | Атланта, Чикаго, Даллас, Лос-Анджелес, Нью-йорк, Вашингтон |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Даллас |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | Лондон, Сингапур, Вашингтон, округ Колумбия |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Амстердам |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | London |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Амстердам |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Город Квебек |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Вашингтон, округ Колумбия |
| **[Gulf Bridge International](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | Амстердам |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | Лондон, Слау |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **[Каалам Телеком Бахрейн Б.С.К.](http://www.kalaam-telecom.com/en/inbusiness/expressroute.html)**| Level 3 Communications |Амстердам |
| **LGA Telecom** |Equinix |Сингапур|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Гонконг, САР 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Сидней |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Амстердам |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Вашингтон, округ Колумбия |
| **[Mtn](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** | Teraco | Кейптаун, Йоханнесбург |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | London |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | Амстердам, Франкфурт |
| **[ПОСТ Телеком Люксембург](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Амстердам |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Амстердам, Дублин, Лондон, Париж |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Франкфурт |  
| **[Retn](https://retn.net/services/cloud-connect/)** | Equinix | Амстердам |
| **Rogers** | Cologix, Equinix | Монреаль, Торонто |
| **[Спектр Предприятия](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | Чикаго, Даллас, Лос-Анджелес, Нью-йорк, Силиконовая долина | 
| **[Tamares Telecom](https://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | London | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Амстердам | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Амстердам |
| **[Телеком Дойчланд GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | Амстердам, Франкфурт |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Амстердам |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Сингапур |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | Сан-Паулу |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | Нью-Йорк |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite |Силиконовая долина, Силиконовая долина 2|
| **Zain** |Equinix |London|
| **[Zertia](https://www.zertia.es)**| Level 3 | Мадрид |
| **[Zirro](https://zirro.com/services/)**| Cologix, Equinix | Монреаль, Торонто |

## <a name="connectivity-through-datacenter-providers"></a>Связь с поставщиками центров обработки данных

| **Поставщика** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Мегапорт, PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Мегапорт, PacketFabric |
| **[Банк данных](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX Reach, Мегапорт ПакетФабрик |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Мегапорт, PacketFabric |
| **[Гибкий](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX Reach, Мегапорт, PacketFabric |
| **[Центры обработки данных ЦТС](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Мегапорт, PacketFabric |
| **[Центры обработки данных потоков]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire Data Centers](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach, Мегапорт, PacketFabric |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX Reach, Мегапорт |
| **[T5 Datacenters](https://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Связь через национальные научно-исследовательские и образовательные сети (NREN)

| **Поставщика**|
| --- |
| **АРНЕТ**| 
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

| **Системный интегратор** | **Continent** |
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
* Для получения дополнительной информации о ExpressRoute, [см.](expressroute-faqs.md)
* Убедитесь, что выполнены все необходимые условия. Ознакомьтесь с разделом [Предварительные требования и контрольный список для ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Карта местонахождения"
