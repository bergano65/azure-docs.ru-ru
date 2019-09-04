---
title: Расположения и поставщики услуг подключения для Azure ExpressRoute | Документация Майкрософт
description: В этой статье приведена подробная информация о расположениях, где предлагаются услуги, и способах подключения к регионам Azure. Эта таблица отсортирована по расположению.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: feb67da3-5abc-4acb-bad4-f78e3c541ded
ms.service: expressroute
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/03/2019
ms.author: cherylmc
ms.openlocfilehash: 5469bd835b189df66c22a3fc13e91c435b4be73f
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257714"
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

## <a name="locations"></a>Регионы Azure для расположения ExpressRoute в гео-неадминистративной области
В следующей таблице сопоставлены регионы Azure с расположениями ExpressRoute в пределах геополитических регионов.

| **Геополитический регион** | **Зона** | **Регионы Azure** | **Расположения ExpressRoute** |
| --- | --- | --- | --- |
| **Государственные организации Австралии** | 1 | Центральная Австралия, Центральная Австралия 2 |Канберра, Канберра 2 |
| **Европа** | 1 |Центральная Франция, Южная Франция, Северная Европа, Западная Европа, западная часть Соединенного Королевства, южная часть Соединенного Королевства |Амстердам, Amsterdam2, Копенгаген, Дублин, Франкфурт, Лондон, London2, Марселе, Ньюпорт (), Париж, Стокгольм, Цюрих |
| **Северная Америка** | 1 |Восточная часть США, западная часть США, восточная часть США 2, западная часть США 2, центральная часть США, центрально-южная часть США, центрально-северная часть США, центрально-западная часть США, Центральная Канада, Восточная Канада |Атланта, Чикаго, Далласе, Денвер, Лас-, Лос-Анджелес, Майами, Нью-Йорк, Сан Сан Антонио, Сиэтл, впадина, Silicon Valley2, Вашингтон (округ Колумбия), штат Вашингтон, округ DC2, Монреаль, Квебек, Москва, Торонто |
| **Азия** | 2 |Восточная Азия, Юго-Восточная Азия |Гонконг, САР, Куала, Сингапур, Сингапур 2, Тайбэй |
| **Индия** | 2 |Западная Индия, Центральная Индия, Южная Индия |Ченнаи, Ченнаи 2, Мумбаи, Мумбаи 2 |
| **Япония** | 2 |Западная Япония, Восточная Япония |Осака, Токио |
| **Океания** | 2 |Восточная Австралия, Юго-Восточная Австралия |Г., Мельбурн, Перт, Сидней | 
| **Южная Корея** | 2 |Центральная Корея, Южная Корея |Пусан, Сеул|
| **ЧАСТЬ ОАЭ** | 3 | Центральная часть ОАЭ, Север ОАЭ | Дубаи, Dubai2 |
| **ЮАР** | 3 |Южно-Африканская Республика, Юго-Африканская Республика, Северная Африка |Кейптаун, Йоханнесбург |
| **Южная Америка** | 3 |Южная Бразилия |Сан-Пауло |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Регионы Azure и неизменяемые границы для национальных облаков
В таблице ниже содержатся сведения о регионах и геополитических границах для национальных облаков.

| **Геополитический регион** | **Регионы Azure** | **Расположения ExpressRoute** |
| --- | --- | --- |
| **Облако правительства США** |US Gov (Аризона), US Gov (Айова), US Gov (Техас), US Gov (Вирджиния), центральный регион US DoD, восточный регион US DoD  |Чикаго, Даллас, Нью-Йорк, Феникс, Сан-Антонио, Сиэтл, Кремниевая долина, Вашингтон (округ Колумбия) |
| **Восточный Китай** |"Восточный Китай", "Восточный Китай 2" |Шанхай, Шанхай 2 |
| **Северный Китай** |"Северный Китай", "Северный Китай 2" |Пекин, Пекин 2 |
| **Германия** |Центральная Германия, восточная Германия |Берлин, Франкфурт |

В стандартном номере SKU ExpressRoute подключение между геополитическими регионами не поддерживается. Для поддержки глобальных подключений необходимо включить надстройку ExpressRoute класса "Премиум". Подключение к национальным облачным средам не поддерживается. При необходимости вы можете работать с поставщиками услуг подключения.

## <a name="partners"></a>Поставщики услуг подключения ExpressRoute

В таблице ниже приведены расположения для подключения и поставщики услуг в каждом расположении. Поставщики услуг и расположения, в которых они предоставляют услуги, перечислены в разделе [Расположения поставщиков услуг подключения](expressroute-locations.md). 

**Локальные регионы Azure** — это те, к которым может получить доступ [Локальная служба ExpressRoute](expressroute-faqs.md) в каждом одноранговом расположении. **н/д** указывает, что локальная сеть ExpressRoute недоступна в этом расположении.


### <a name="production-azure"></a>Рабочая среда Azure
| **Location** | **Владелец и адрес** | **Локальные регионы Azure** | **Поставщики услуг** |
| --- | --- | --- | --- |
| **Амстердам** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | Западная Европа | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, TeleCity Group, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Амстердам 2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | Западная Европа | CenturyLink Cloud Connect, DE-ЦИКС, Interxion, Vodafone |
| **Атланта** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | Н/Д | Equinix, Megaport |
| **Окленд** | [Олбани группа вокус (NZ)](https://www.vocus.co.nz/business/cloud-data-centres) | Н/Д | Деволи, Кордиа, Orange, Spark NZ, Вокус Group NZ |
| **Пусан** | [LG CNS](https://datacenter.lgcns.com/Contents/En/Menu_1/Locations_1.aspx) | Республика Корея, южный регион | LG CNS |
| **Канберра** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | Центральная Австралия | CDC |
| **Канберра 2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | Центральная Австралия 2| CDC |
| **Кейптаун** | [Терако CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | западная часть ЮАР | Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **Ченнай** | Tata Communications | Южная Индия | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | Южная Индия | Airtel |
| **Чикаго** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | Центрально-северная часть США | Aryaka сети, в & T NetBond, CenturyLink Cloud Connect, Кологикс, Comcast, Коресите, Equinix, в облаке, Internet2, связи уровня 3, Orange, Паккетфабрик, ПККВ Global Limited, спринт, Телиа, Verizon, Zayo |
| **Копенгаген** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | Н/Д | Interxion |
| **Даллас** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | Н/Д | Aryaka Networks, AT & T NetBond, Кологикс, Equinix, Internet2, уровень 3, взаимодействие, Orange, Неутрона сети, Телмекс Унинет, Телиа перевозчик, Telco, Verizon, Zayo|
| **Денвер** | [Коресите DE1](https://www.coresite.com/data-centers/locations/denver/de1) | Центрально-западная часть США | Коресите, Orange, Zayo |
| **Дубаи** | [ПККС](https://www.pacificcontrols.net/cloudservices/index.html) | Северная часть ОАЭ | Етисалат ОАЭ |
| **Dubai2** | [Du датамена](http://datamena.com/solutions/data-centre) | Северная часть ОАЭ | Du датамена, Орикском |
| **Дублин** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | Северная Европа | Colt, eir, Equinix, Interxion, Megaport |
| **Франкфурт** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | Н/Д | DE-ЦИКС, Interxion, оранжевый |
| **Гонконг, САР** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | Восточная Азия | Aryaka сети, Британская телекоммуникации, CenturyLink Cloud Connect, главный телекоммуникации, Международная телекоммуникации, Глобальная, Equinix, Orange, NTT Communications, оранжевый, ПККВ глобально ограниченный, Tata обмен данными, Телиа, Verizon |
| **Йоханнесбург** | [Терако JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | северная часть ЮАР | Британская телекоммуникации, Интернет-решения — Cloud Connect, жидкостный телекоммуникации, оранжевый, Терако |
| **Куала-Лумпур** | [ВРЕМЯ, дотком Менара цель](https://www.aims.com.my/co-location/points-of-presence.html) | Н/Д | TIME dotCom |
| **Лас-Вегас** | [Переключение LV](https://www.switch.com/las-vegas) | Н/Д | CenturyLink Cloud Connect, Megaport |
| **Лондон** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | Южная часть Соединенного Королевства | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse — KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **Лондон 2** | [Два Севера для дома](https://www.telehouse.com/global-data-centers/emea/london-data-centers/telehouse-north-two/) | Южная часть Соединенного Королевства | Доступ IX, Equinix |
| **Лос-Анджелес** | [Коресите LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | Н/Д | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **Марсель** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | Южная Франция | DE-ЦИКС, Interxion, Жагуар Network |
| **Мельбурн** | [Некстдк M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | Юго-восточная Австралия | Аарнет, Деволи, Equinix, Orange, НЕКСТДК, ОПТУС, Телстра Corporation, ТПГ телекоммуникации |
| **Майами** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | Н/Д | C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Монреаль** | [Кологикс MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | Н/Д | Bell для Канады, Кологикс, Orange, Телус, Zayo |
| **Мумбай** | Tata Communications | Западная Индия | Global Клаудксчанже (ГККС), зависимость ЖИО, Сифи, Tata Communications, Verizon |
| **Мумбаи 2** | Airtel | Западная Индия | Airtel, Sify, Vodafone Idea |
| **Нью-Йорк** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | Н/Д | CenturyLink Cloud Connect, Коресите, Equinix, recloud, Orange, пакет, Zayo |
| **Ньюпорт (Уэльс)** | [Next Generation Data](https://www.nextgenerationdata.co.uk) | Западная часть Соединенного Королевства | Британская телекоммуникации, Colt, связь уровня 3, данные следующего поколения |
| **Осака** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | Западная Япония | Colt, Equinix, Интернет-инициатива Япония Inc. — IIJ, NTT Communications, NTT Смартконнект, Softbank " |
| **Париж** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | Центральная Франция | CenturyLink Cloud Connect, Colt, Equinix, в облаке, Interxion, оранжевый, Телиа, Zayo |
| **Перт** | [Некстдк P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | Н/Д | Orange, Некстдк |
| **Квебек** | [Разных](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | Восточная Канада | Bell Canada, Megaport |
| **Сан-Антонио** | [Цирусоне SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | Центрально-южная часть США | CenturyLink Cloud Connect, Megaport |
| **Сан-Паулу** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | Южная Бразилия | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Сиэтл;** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | Западная часть США 2 | Aryaka сети, Equinix, связь уровня 3, Orange, Телус, Zayo |
| **Сеул** | [КИНКС Гасан IDC](https://www.kinx.net/support/location/?lang=en) | Центральная Корея | KINX, LG CNS, Sejong Telecom |
| **Кремниевая долина** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | Западная часть США | Aryaka сети, AT & T NetBond, Британская телекоммуникации, CenturyLink Cloud Connect, Comcast, Коресите, Equinix, в облаке, Internet2, IX REACH, пакет, Паккетфабрик, связь уровня 3, Orange, оранжевый, спринт, Tata Communications, Телиа. Verizon, Zayo |
| **Полупроводниковая Valley2** | [Коресите SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | Западная часть США | Coresite | 
| **Сингапур** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | Юго-Восточная Азия | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Сингапур 2** | [Глобальный коммутатор Тай Сенг](https://www.globalswitch.com/locations/singapore-data-centres/) | Юго-Восточная Азия | Colt, Эпсилон Global Communications, Orange, SingTel |
| **Стокгольм** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | Н/Д | Перевозчик Телиа |
| **Сидней** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | Восточная Австралия | Аарнет, AT & T NetBond, Британская телекоммуникации, Деволи, Equinix, Кордиа, Orange, НЕКСТДК, NTT Communications, ОПТУС, оранжевый, Spark NZ, Телстра Corporation, ТПГ Communications, Verizon, Вокус Group NZ |
| **Тайбэй** | Chief Telecom | Н/Д | Главный телекоммуникации, Фареастоне |
| **Токио** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | Восточная Япония | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Торонто** | [Кологикс TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | Центральная Канада | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **Вашингтон, округ Колумбия** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | Восточная часть США, Восточная часть США 2 | Aryaka сети, AT & T NetBond, Британская телекоммуникации, CenturyLink Cloud Connect, Кологикс, Comcast, Коресите, Equinix, Internet2, между облаком, связь уровня 3, Orange, Неутрона сети, NTT Communications, оранжевый, Паккетфабрик, спринт, Tata Связь, перевозчик Телиа, Verizon, Zayo |
| **Вашингтон, округ Колумбия 2** | [Коресите Рестоне](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | Восточная часть США, Восточная часть США 2 |Coresite | 
| **Цюрих** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | Н/Д | В облаке, Interxion |

 **+** означает "скоро"

### <a name="national-cloud-environments"></a>Национальные облачные среды

### <a name="us-government-cloud"></a>Облако правительства США
| **Location** | **Поставщики услуг** |
| --- | --- |
| **Чикаго** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Даллас** |Equinix, Megaport, Verizon |
| **Нью-Йорк** |Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | В & T NetBond, CenturyLink Cloud Connect, Orange |
| **Сан-Антонио** | CenturyLink Cloud Connect, Megaport |
| **Кремниевая долина** | Equinix, Level 3 Communications, Verizon |
| **Сиэтл;** | Equinix, Megaport |
| **Вашингтон, округ Колумбия** |AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Китай
| **Location** | **Поставщики услуг** |
| --- | --- |
| **Пекин** |China Telecom |
| **Пекин 2** | Телекоммуникации в Китае, GDS |
| **Шанхай** |China Telecom |
| **Шанхай 2** | Телекоммуникации в Китае, GDS |

Дополнительные сведения см. на странице [ExpressRoute в Китае](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Германия
| **Location** | **Поставщики услуг** |
| --- | --- |
| **Берлин** |e-shelter, Megaport +, T-Systems |
| **Франкфурт** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Подключение через поставщиков Exchange
Вы можете создать подключение, даже если ваш поставщик услуг подключения не указан в предыдущих разделах.

* Узнайте у своего поставщика услуг подключения, подключен ли он к какому-либо Exchange, указанному в таблице выше. Дополнительные сведения об услугах, предлагаемых поставщиками Exchange, см. по ссылкам ниже. Несколько поставщиков услуг подключения уже подключены к серверам Ethernet Exchange.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equnix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)
  
* Обратитесь к своему поставщику услуг подключения, чтобы он расширил вашу сеть, добавив необходимое пиринговое расположение.
  * Убедитесь, что поставщик услуг подключения расширяет границы вашего подключения, сохраняя высокую доступность во избежание влияния единых точек отказа.
* Закажите канал ExpressRoute с Exchange у вашего поставщика услуг подключения для связи с Майкрософт.
  * Для настройки подключения выполните действия, описанные в статье [Создание и изменение канала ExpressRoute с помощью PowerShell](expressroute-howto-circuit-classic.md) .

## <a name="c1partners"></a>Подключение через дополнительных поставщиков услуг
| **Location** | **Exchange** | **Поставщики услуг подключения** |
| --- | --- | --- |
| **Амстердам** | Equinix, Interxion, связь уровня 3, город | Бикс, Клаудкспресс, Еурофибер, Фаствеб S. p. A, залива Bridge International, Калаам телекоммуникации Бахрейн B. S. C, Маиноне, Нианет, POST-Люксембург, Проксимус, TDC Ерхверв, телекоммуникационной Italia живы, Telekom Deutschland GmbH, Телиа |
| **Атланта** | Equinix| Самые Castle
| **Кейптаун** | Teraco | MTN |
| **Чикаго** | Equinix| Самые Castle, спектр для предприятий, Виндстреам |
| **Даллас** | Equinix, Megaport | Акстел, C3ntro, телекоммуникации, Кокс Business, самые Castle, обнаруженные данные, спектр предприятий, |
| **Франкфурт** | Interxion, город проживания | Бикс, Циниа, Нианет, КСК AG, Telekom Deutschland GmbH |
| **Гамбург** | Equinix | Cinia |
| **Гонконг, САР** | Equinix | Chief, Macroview Telecom |
| **Йоханнесбург** | Teraco | MTN |
| **Лондон** | BICS, Equinix, euNetworks, Telecity | Безек International Ltd., Кореазуре, Эпсилон, ограниченная, экспоненциальная E, ХСО, Нексжен сетей, Проксимус, Тамарес телекоммуникации, Заину |
| **Лос-Анджелес** | Equinix |Самые Castle, спектр для предприятий, переtelco |
| **Мадрид** | Level3 | Zertia |
| **Монреаль** | Cologix, Equinix | Airgate Technologies, Inc. Технологии аптум, Рожерс, Зирро |
| **Нью-Йорк** |Equinix, Megaport | Алтице Business, самые Castle, спектр предприятий, Вебаир |
| **Париж** | Equinix | Proximus |
| **Квебек** | Megaport | Fibrenoire |
| **Сан-Паулу** | Equinix | Venha Pra Nuvem |
| **Сиэтл;** |Equinix | Alaska Communications |
| **Кремниевая долина** |Коресите, Equinix | Кокс Business, спектр предприятий, Виндстреам, X2nsat Inc. |
| **Сингапур** |Equinix |1CLOUDSTAR, Бикс, "телекоммуникационность", "Телекоммуникации", ограниченная телекоммуникация, американская телекоммуникации, Объединенная информация (УИХ) |
| **Slough** | Equinix | HSO|
| **Сидней** | Megaport | Macquarie Telecom Group|
| **Токио** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Торонто** | Equinix, Megaport | Технологии аиргате Inc., Беанфиелд Метроконнект, Аптум Technologies, Иведха Inc, Рожерс, Синктел, Зирро|
| **Вашингтон, округ Колумбия** |Equinix | Алтице бизнес, Бикс, Кокс Business, самые Castle, ГТТ коммуникации Inc, Эпсилон, ограниченная телекоммуникации, Масерги, Виндстреам |

## <a name="expressroute-system-integrators"></a>Системные интеграторы ExpressRoute
Возможность частного подключения, соответствующего вашим потребностям, будет зависеть от масштаба сети. Чтобы упростить переход на ExpressRoute, вы можете обратиться к одному из системных интеграторов, указанных в таблице ниже.

| **Континент** | **Системные интеграторы** |
| --- | --- |
| **Азия** |Avanade Inc., OneAs1a |
| **Австралия** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Европа** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Северная Америка** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **Южная Америка** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Следующие шаги
* Дополнительные сведения об ExpressRoute см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).
* Убедитесь, что выполнены все необходимые условия. Ознакомьтесь с разделом [Предварительные требования и контрольный список для ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Карта расположения"
