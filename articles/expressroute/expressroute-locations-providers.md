---
title: Расположения и поставщики услуг подключения Azure ExpressRoute | Документация Майкрософт
description: В этой статье приведена подробная информация о расположениях, где предлагаются услуги, и способах подключения к регионам Azure. Эта таблица отсортирована по расположению.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: cherylmc
ms.openlocfilehash: ba0573af59c2924648361fec10bffed189291d28
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768569"
---
# <a name="expressroute-partners-and-peering-locations"></a>Партнеры и одноранговые расположения ExpressRoute

> [!div class="op_single_selector"]
> * [Расположение по провайдеру](expressroute-locations.md)
> * [Поставщики по местоположению](expressroute-locations-providers.md)


Таблицы в этой статье предоставляют информацию о географическом охвате и местоположении ExpressRoute, поставщиках услуг подключения ExpressRoute и интеграторах системы ExpressRoute (SIs).

> [!Note]
> Регионы Azure и местоположения ExpressRoute представляют собой две различные концепции, понимание разницы между ними имеет решающее значение для изучения гибридной сети Azure. 
>
>

## <a name="azure-regions"></a>Регионы Azure
Регионы Azure — это глобальные центры обработки данных, в которых расположены вычислительные, сетевые и хранилища Azure. При создании ресурса Azure клиенту необходимо выбрать местоположение ресурса. Местоположение ресурса определяет, в каком центре обработки данных Azure (или зоне доступности) создается ресурс.

## <a name="expressroute-locations"></a>Расположение ExpressRoute
Местоположения ExpressRoute (иногда называемые пиринговыми локациями или местоположениями встреч) — это объекты совместного размещения, на которых расположены устройства Microsoft Enterprise edge (MSEE). Расположение ExpressRoute является точкой входа в сеть корпорации Майкрософт и распространяется по всему миру, предоставляя клиентам возможность подключения к сети Майкрософт по всему миру. В этих местах партнеры ExpressRoute и клиенты ExpressRoute Direct выдают перекрестное подключение к сети Майкрософт. Как правило, расположение ExpressRoute не должно соответствовать региону Azure. Например, клиент может создать схему ExpressRoute с расположением ресурсов *East US,* в месте Seattle *Peering.*

Вы сможете получить доступ ко всем службам Azure во всех регионах соответствующего геополитического региона, если вы подключены по меньшей мере к одному расположению ExpressRoute в этом регионе. 

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Лазурные регионы будут расположены в геополитическом регионе
В следующей таблице сопоставлены регионы Azure с расположениями ExpressRoute в пределах геополитических регионов.

| **Геополитический регион** | **Лазурные регионы** | **Расположение ExpressRoute** |
| --- | --- | --- |
| **Государственные организации Австралии** | Центральная Австралия, Центральная Австралия 2 |Канберра, Канберра 2 |
| **Европа** | Франция Центральная, Франция юг, Германия Север, Германия Западная Центральная, Северная Европа, Норвегия Восток, Норвегия Запад, Швейцария Север, Швейцария Запад, Великобритания Запад, Великобритания на юге, Западная Европа |Амстердам, Амстердам2, Копенгаген, Дублин, Франкфурт, Ньюсен, Лондон, Лондон2, Марсель, Милан, Мюнхен, Ньюпорт (Уэльс), Осло, Париж, Ставангер, Стокгольм, Цюрих, Мюнхен |
| **Северная Америка** | Восточная часть США, западная часть США, восточная часть США 2, западная часть США 2, центральная часть США, центрально-южная часть США, центрально-северная часть США, центрально-западная часть США, Центральная Канада, Восточная Канада |Атланта, Чикаго, Даллас, Денвер, Лас-Вегас, Лос-Анджелес, Майами, Нью-йорк, Сан-Антонио, Сиэтл, Силиконовая долина, Силиконовая долина2, Вашингтон, Вашингтон, округ Колумбия, Монреаль, Квебек, Торонто, Ванкувер |
| **Азия** | Восточная Азия, Юго-Восточная Азия | Бангкок, Гонконг, Гонконг2, Джакарта, Куала-Лумпур, Сингапур, Сингапур2, Тайбэй |
| **Индия** | Западная Индия, Центральная Индия, Южная Индия |Ченнаи, Ченнаи 2, Мумбаи, Мумбаи 2 |
| **Япония** | Западная Япония, Восточная Япония |Осака, Токио2 |
| **Океания** | Восточная Австралия, Юго-Восточная Австралия |Окленд, Мельбурн, Перт, Сидней, Сидней2 | 
| **Южная Корея** | Республика Корея, центральный регион, Республика Корея, южный регион |Пусан, Сеул|
| **ОАЭ** | Центральный ОАЭ, ОАЭ Север | Дубай, Дубай2 |
| **ЮАР** | Западная часть Африки, северная часть Африки |Кейптаун, Йоханнесбург |
| **Южная Америка** | Южная Бразилия |Сан-Паулу |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Лазурные регионы и геополитические границы для национальных облаков
В таблице ниже содержатся сведения о регионах и геополитических границах для национальных облаков.

| **Геополитический регион** | **Лазурные регионы** | **Расположение ExpressRoute** |
| --- | --- | --- |
| **Облако US Gov ** |US Gov (Аризона), US Gov (Айова), US Gov (Техас), US Gov (Вирджиния), центральный регион US DoD, восточный регион US DoD  |Атланта, Чикаго, Даллас, Нью-йорк, Финикс, Сан-Антонио, Сиэтл, Силиконовая долина, Вашингтон |
| **Восточный Китай** |"Восточный Китай", "Восточный Китай 2" |Шанхай, Шанхай 2 |
| **Северный Китай** |"Северный Китай", "Северный Китай 2" |Пекин, Пекин 2 |
| **Германия** |Центральная Германия, восточная Германия |Берлин, Франкфурт |

В стандартном номере SKU ExpressRoute подключение между геополитическими регионами не поддерживается. Для поддержки глобальных подключений необходимо включить надстройку ExpressRoute класса "Премиум". Подключение к национальным облачным средам не поддерживается. При необходимости вы можете работать с поставщиками услуг подключения.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>Поставщики услуг подключения ExpressRoute

В таблице ниже приведены расположения для подключения и поставщики услуг в каждом расположении. Поставщики услуг и расположения, в которых они предоставляют услуги, перечислены в разделе [Расположения поставщиков услуг подключения](expressroute-locations.md).

* **Локальные регионы Azure** являются теми, к которым можно получить доступ [к ExpressRoute Local](expressroute-faqs.md) в каждом месте для вглядывающихся. **n/a** указывает на то, что ExpressRoute Local недоступен в этом вглядываемом месте.

* **Зона** относится к [ценообразованию](https://azure.microsoft.com/pricing/details/expressroute/).


### <a name="global-commercial-azure"></a>Глобальный коммерческий Azure
| **Расположение** | **Адрес** | **Зоны** | **Местные лазурные регионы** | **ER Прямая** | **Поставщики услуг** |
| --- | --- | --- | --- | --- | --- |
| **Амстердам** | [Эквиникс AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | Западная Европа | 10G, 100G | Арьяка Сети, AT&T NetBond, Британский Телеком, Кольт, Equinix, euNetworks, ГАЗН, InterCloud, Interxion, KPN, IX Reach, Уровень 3 Связи, Мегапорт, NTT связи, оранжевый, Тата связи, Telefonica, Telenor, Telia Carrier, Verizon |
| **Амстердам 2** | [Интерксион AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | Западная Европа | 10G, 100G | CenturyLink Облако Connect, Кольт, DE-CIX, euNetworks, Interxion, оранжевый, Vodafone |
| **Атланта** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | Недоступно | Недоступно | Equinix, Megaport |
| **Окленд** | [Группа Вокусов Н. Олбани](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | Недоступно | 10G | Деволи, Кордия, Мегапорт, Спарк НЗ, Вокус Групп НЗ |
| **Бангкок** | [AIS](https://business.ais.co.th/solution/en/azure-expressroute.html) | 2 | Недоступно | 10G | AIS |
| **Пусан** | [LG CNS](https://www.lgcns.com/datacenter) | 2 | Республика Корея, южный регион | Недоступно | LG CNS |
| **Канберра** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Центральная Австралия | 10G, 100G | CDC |
| **Канберра 2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Центральная Австралия 2| 10G, 100G | CDC |
| **Кейптаун** | [Терако CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | Западная часть Африки | 10G | BCX, Интернет-решения - Cloud Connect, Liquid Telecom, Teraco |
| **Ченнай** | Tata Communications | 2 | Южная Индия | 10G | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | 2 | Южная Индия | Недоступно | Airtel |
| **Чикаго** | [Эквиникс CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | Центрально-северная часть США | 10G, 100G | Арьяка Сети, AT&T NetBond, CenturyLink Облако Connect, Cologix, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, Уровень 3 Коммуникации, Мегапорт, PacketFabric, PCCW Global Limited, Спринт, Telia Carrier, Verizon, Зайо |
| **Копенгаген** | [Интерксион CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | Недоступно | 10G | Interxion |
| **Даллас** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | Недоступно | 10G, 100G | Арьяка Сети, AT&T NetBond, Cologix, Equinix, Internet2, Уровень 3 Коммуникации, Мегапорт, Neutrona Сети, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Зайо|
| **Денвер** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | центрально-западная часть США | Недоступно | CoreSite, Мегапорт, Зайо |
| **Дубай** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | Северная часть ОАЭ; | Недоступно | Этисалат ОАЭ |
| **Дубай2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | Северная часть ОАЭ; | Недоступно | du Datamena, Мегапорт, Оранжевый, Orixcom |
| **Дублин** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Северная Европа | 10G, 100G | Кольт, Эйр, Эквиникс, euNetworks, Интерксион, Мегапорт |
| **Франкфурт** | [Интерксион FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | Германия Западно-Центральная | 10G, 100G | AT&T NetBond, CenturyLink Cloud Connect, Colt, DE-CIX, Equinix, GEANT, Interxion, Megaport, Orange, Telia Carrier |
| **Женева** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | Швейцария Запад | 10G, 100G | Equinix, Megaport |
| **Гонконг, САР** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Восточная Азия | Недоступно | Арьяк Сети, Британский Телеком, CenturyLink Cloud Connect, Главный Телеком, China Telecom Global, Equinix, InterCloud, Мегапорт, NTT Связи, Оранжевый, PCCW Global Limited, Tata Communications, Telia Carrier, Verizon |
| **Гонконг2** | [МЕГА-и](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | Недоступно | 10G | |
| **Джакарта** | Телком Индонезия | 4 | Недоступно | 10G | |
| **Йоханнесбург** | [Терако JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Северная часть ЮАР; | 10G | BCX, British Telecom, Интернет-решения - Cloud Connect, Liquid Telecom, Orange, Teraco |
| **Куала-Лумпур** | [ВРЕМЯ дотКом Менара AIMS](https://www.aims.com.my/co-location/points-of-presence.html) | 2 | Недоступно | Недоступно | TIME dotCom |
| **Лас-Вегас** | [Переключатель LV](https://www.switch.com/las-vegas) | 1 | Недоступно | Недоступно | CenturyLink Cloud Connect, Megaport |
| **Лондон** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | южная часть Соединенного Королевства | 10G, 100G | AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse - KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **Лондон 2** | [Телехаус Северный 2](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | южная часть Соединенного Королевства | 10G, 100G | CenturyLink Облако Connect, Кольт, IX Достижение, Equinix, Мегапорт, Телехаус - KDDI |
| **Лос-Анджелес** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | Недоступно | Недоступно | CoreSite, Equinix, Мегапорт, Neutrona Networks, NTT, Transtelco, Зайо |
| **Марсель** |[Интерксион MRS1](https://www.interxion.com/Locations/marseille/) | 1 | Южная Франция | Недоступно | DE-CIX, GEANT, Interxion, Jaguar Network, Ooredoo Cloud Connect |
| **Мельбурн** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Юго-Восточная часть Австралии | 10G, 100G | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Майами** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | Недоступно | 10G | C3ntro, Equinix, Мегапорт, Neutrona Сети |
| **Milan** | [ИРИДЕОС](https://irideos.it/en/data-centers/) | 1 | Недоступно | 10G | |
| **Монреаль** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | Недоступно | 10G, 100G | Белл Канада, Кологикс, Мегапорт, Телус, Зайо |
| **Мумбаи** | Tata Communications | 2 | Западная Индия | Недоступно | Global CloudXchange (GCX), Reliance Jio, Sify, Tata Communications, Verizon |
| **Мумбаи 2** | Airtel | 2 | Западная Индия | Недоступно | Airtel, Sify, Vodafone Idea |
| **Мюнхен** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/) | 1 | Недоступно | 10G, 100G | |
| **Нью-Йорк** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | Недоступно | Недоступно | CenturyLink Облако Connect, Кольт, Coresite, Equinix, InterCloud, Мегапорт, Пакет, Зайо |
| **Ньюпорт (Уэльс)** | [Данные следующего поколения](https://www.nextgenerationdata.co.uk) | 1 | западная часть Соединенного Королевства | Недоступно | British Telecom, Colt, Уровень 3 Коммуникации, Данные следующего поколения |
| **Осака** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Западная Япония | 10G, 100G | Colt, Equinix, Internet Initiative Japan Inc. - IIJ, Megaport, NTT Communications, NTT SmartConnect, Softbank |
| **Oslo** | [DigiPlex Ульвен](https://www.digiplex.com/locations/oslo-datacentre) | 1 | Восточная Норвегия; | 10G, 100G | Мегапорт, Теленор, Telia Carrier |
| **Париж** | [Интерксион PAR5](https://www.interxion.com/Locations/paris/) | 1 | Центральная Франция | 10G, 100G | CenturyLink Облако Connect, Кольт, Equinix, Intercloud, Интерксион, Оранжевый, Telia Carrier, Зайо |
| **Перт** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | Недоступно | 10G | Мегапорт, NextDC |
| **Город Квебек** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Восточная Канада | Недоступно | Bell Canada, Megaport |
| **Сан-Антонио** | [Сайрусон SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | Центрально-южная часть США | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Сан-Паулу** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Южная Бразилия | Недоступно | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Сиэтл** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | западная часть США 2 | 10G, 100G | Арьяка Сети, Эквиникс, Уровень 3 Связи, Мегапорт, Телус, Зайо |
| **Сеул** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | 2 | Республика Корея, центральный регион | 10G, 100G | KINX, KT, LG CNS, Sejong Telecom |
| **Кремниевая долина** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | западная часть США | 10G, 100G | Арьяка Сети, AT&T NetBond, Британский Телеком, CenturyLink Облако Connect, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, IX Reach, Пакет, PacketFabric, Уровень 3 Связи, Мегапорт, Оранжевый, Спринт, Тата связи, Telia Carrier, Verizon, Зайо |
| **Силиконовая долина2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | западная часть США | 10G, 100G | Кольт, Coresite | 
| **Сингапур** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Юго-Восточная Азия | 10G, 100G | Арьяка Сети, AT&T NetBond, British Telecom, China Mobile International, Epsilon Global Communications, Equinix, InterCloud, Уровень 3 Коммуникации, Мегапорт, NTT Связи, Оранжевый, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Сингапур 2** | [Глобальный коммутатор Тай Сенг](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Юго-Восточная Азия | 10G, 100G | China Unicom Global, Colt, Epsilon Global Communications, Megaport, PCCW Global Limited, SingTel |
| **Ставангер** | [Зеленая гора DC1](https://greenmountain.no/dc1-stavanger/) | 1 | Норвегия Запад | 10G, 100G | |
| **Стокгольм** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | Недоступно | 10G | Эквиникс, Telia Carrier |
| **Сидней** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Восточная Австралия | 10G, 100G | AARNet, AT&T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Spark N, Telstra Corporation, TPG Telecom, Verizon, Vocus Group N |
| **Сидней2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | Восточная Австралия | 10G, 100G | Мегапорт, NextDC |
| **Тайбэй** | Chief Telecom | 2 | Недоступно | 10G | Главный телеком, FarEasTone |
| **Токио** | [Эквийс TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Восточная Япония | 10G, 100G | Aryaka Networks, AT&T NetBond, BBIX, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, Megaport, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Токио2** | [В ТОКИО](https://www.attokyo.com/) | 2 | Восточная Япония | 10G, 100G | В ТОКИО |
| **Торонто** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Центральная Канада | 10G, 100G | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **Ванкувер** | [Кельникс ВАН1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | Cologix | 10G, 100G | |
| **Вашингтон, округ Колумбия** | [Эквиникс DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | Восток США, Восток США 2 | 10G, 100G | Арьяка Сети, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, Internet2, InterCloud, IX Reach, Уровень 3 Коммуникации, Мегапорт, Neutrona Сети, NTT Связи, Оранжевый, PacketFabric, SES, Sprint, Tata Communications, Telia Carrier, Verizon |
| **Вашингтон, округ Колумбия 2** | [Основной Рестон](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | Восток США, Восток США 2 | 10G, 100G | CenturyLink Облако Connect, Coresite, Intelsat, Viasat, Зайо | 
| **Цюрих** | [Интерксион ЦУР2](https://www.interxion.com/Locations/zurich/) | 1 | Швейцария Север | 10G, 100G | Интероблако, Интерксион, Мегапорт, Swisscom |

 **+** обозначает скоро

### <a name="national-cloud-environments"></a>Национальные облачные среды

Национальные облака Azure изолированы друг от друга и от глобального коммерческого Azure. ExpressRoute для одного облака Azure не может подключиться к регионам Azure в других.

### <a name="us-government-cloud"></a>Облако US Gov 
| **Расположение** | **Адрес** | **Местные лазурные регионы**| **ER Прямая** | **Поставщики услуг** |
| --- | --- | --- | --- | --- |
| **Атланта** | [Equinix AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | Недоступно | 10G, 100G | Equinix |
| **Чикаго** | [Эквиникс CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | Недоступно | 10G, 100G | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Даллас** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | Недоступно | 10G, 100G | Equinix, Megaport, Verizon |
| **Нью-Йорк** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | Недоступно | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [СайрусОн Чендлер](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | US Gov (Аризона) | Недоступно | AT&T NetBond, CenturyLink Cloud Connect, Мегапорт |
| **Сан-Антонио** | [Сайрусон SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | US Gov (Техас) | Недоступно | CenturyLink Cloud Connect, Megaport |
| **Кремниевая долина** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | Недоступно | 10G, 100G | AT&T, Equinix, Уровень 3 Коммуникации, Verizon |
| **Сиэтл** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | Недоступно | Недоступно | Equinix, Megaport |
| **Вашингтон, округ Колумбия** | [Эквиникс DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | США DoD Востоке, ГУБЕРНАТОР США Вирджиния | 10G, 100G | AT&T NetBond, CenturyLink Cloud Connect, Equinix, Уровень 3 Коммуникации, Мегапорт, Verizon |

### <a name="china"></a>Китай
| **Расположение** | **Поставщики услуг** |
| --- | --- |
| **Пекин** |China Telecom |
| **Пекин 2** | China Telecom, China Unicom, GDS |
| **Шанхай** |China Telecom |
| **Шанхай 2** | Китай Телеком, GDS |

Чтобы узнать больше, смотрите [ExpressRoute в Китае](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>Германия
| **Расположение** | **Поставщики услуг** |
| --- | --- |
| **Берлин** |e-shelter, Megaport +, T-Systems |
| **Франкфурт** |Colt, Equinix, Interxion |

## <a name="connectivity-through-exchange-providers"></a><a name="c1partners"></a>Связь через поставщиков Exchange
Вы можете создать подключение, даже если ваш поставщик услуг подключения не указан в предыдущих разделах.

* Узнайте у своего поставщика услуг подключения, подключен ли он к какому-либо Exchange, указанному в таблице выше. Дополнительные сведения об услугах, предлагаемых поставщиками Exchange, см. по ссылкам ниже. Несколько поставщиков услуг подключения уже подключены к серверам Ethernet Exchange.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equnix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [ИнтерКсион](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
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

## <a name="connectivity-through-additional-service-providers"></a><a name="c1partners"></a>Связь с дополнительными поставщиками услуг
| **Расположение** | **Exchange** | **Поставщики услуг подключения** |
| --- | --- | --- |
| **Амстердам** | Equinix, Интерксион, Уровень 3 Коммуникации | BICS, CloudXpress, Eurofiber, Fastweb S.p.A, Gulf Bridge International, Kalaam Telecom Bahrain B.S.C, MainOne, Nianet, POST Telecom Luxembourg, Proximus, RETN, TDC Erhverv, Telecom Italia Sparkle, Telekom Deutschland GmbH, Telia |
| **Атланта** | Equinix| Замок Корона
| **Кейптаун** | Teraco | MTN |
| **Чикаго** | Equinix| Коронный замок, Спектр Энтерпрайз, Виндстрим |
| **Даллас** | Equinix, Megaport | Axtel, C3ntro Телеком, Кокс Бизнес, Коронный замок, Data Foundry, Спектр Энтерпрайз, Транстелко |
| **Франкфурт** | Interxion | BICS, Cinia, Equinix, Nianet, ЗСК АГ, Телеком Deutschland GmbH |
| **Гамбург** | Equinix | Cinia |
| **Гонконг, САР** | Equinix | Chief, Macroview Telecom |
| **Йоханнесбург** | Teraco | MTN |
| **Лондон** | BICS, Equinix, euNetworks| Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponential E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **Лос-Анджелес** | Equinix |Коронный замок, Спектр Энтерпрайз, Транстелко |
| **Мадрид** | Level3 | Zertia |
| **Монреаль** | Cologix| Айргейт Технологии, Inc. Аптум Технологии, Роджерс, Зирро |
| **Нью-Йорк** |Equinix, Megaport | Altice Business, Crown Castle, Спектр Энтерпрайз, Вебэйр |
| **Париж** | Equinix | Proximus |
| **Город Квебек** | Megaport | Fibrenoire |
| **Сан-Паулу** | Equinix | Venha Pra Nuvem |
| **Сиэтл** |Equinix | Alaska Communications |
| **Кремниевая долина** |Coresite, Эквиникс | Кокс Бизнес, Спектр Предприятия, Windstream, X2nsat Inc. |
| **Сингапур** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, Epsilon Телекоммуникации Лимитед, LGA Телеком, Объединенная информационная магистраль (UIH) |
| **Slough** | Equinix | HSO|
| **Сидней** | Megaport | Macquarie Telecom Group|
| **Токио** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Торонто** | Equinix, Megaport | Airgate Technologies Inc., Бинфилд Метроконнект, Аптум Технолоджи, Иведха Инк, Роджерс, Thinktel, Зирро|
| **Вашингтон, округ Колумбия** |Equinix | Altice Business, BICS, Cox Business, Crown Castle, Gtt Communications Inc, Epsilon Telelecommunications Limited, Masergy, Windstream |

## <a name="expressroute-system-integrators"></a>Системные интеграторы ExpressRoute
Возможность частного подключения, соответствующего вашим потребностям, будет зависеть от масштаба сети. Чтобы упростить переход на ExpressRoute, вы можете обратиться к одному из системных интеграторов, указанных в таблице ниже.

| **Continent** | **Системные интеграторы** |
| --- | --- |
| **Азия** |Avanade Inc., OneAs1a |
| **Австралия** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Европа** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Северная Америка** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **Южная Америка** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Следующие шаги
* Для получения дополнительной информации о ExpressRoute, [см.](expressroute-faqs.md)
* Убедитесь, что выполнены все необходимые условия. Ознакомьтесь с разделом [Предварительные требования и контрольный список для ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Карта местонахождения"
