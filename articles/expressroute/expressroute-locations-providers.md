---
title: Расположения и поставщики услуг подключения Azure ExpressRoute | Документация Майкрософт
description: В этой статье приведена подробная информация о расположениях, где предлагаются услуги, и способах подключения к регионам Azure. Эта таблица отсортирована по расположению.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: cherylmc
ms.openlocfilehash: 83ee20203cdd6a5afefd9d7e6e6e884fd142cf3c
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901743"
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

| **Геополитический регион** | **Регионы Azure** | **Расположения ExpressRoute** |
| --- | --- | --- |
| **Государственные организации Австралии** | Центральная Австралия, Центральная Австралия 2 |Канберра, Канберра 2 |
| **Европа** | Центральная Франция, Южная Франция, Северная Европа, Западная Европа, западная часть Соединенного Королевства, южная часть Соединенного Королевства |Амстердам, Amsterdam2, Копенгаген, Дублин, Франкфурт, Лондон, London2, Марселе, Ньюпорт (), Париж, Стокгольм, Цюрих, Мюнхене |
| **Северная Америка** | Восточная часть США, западная часть США, восточная часть США 2, западная часть США 2, центральная часть США, центрально-южная часть США, центрально-северная часть США, центрально-западная часть США, Центральная Канада, Восточная Канада |Атланта, Чикаго, Далласе, Денвер, Лас-, Лос-Анджелес, Майами, Нью-Йорк, Сан Сан Антонио, Сиэтл, впадина, Silicon Valley2, Вашингтон (округ Колумбия), штат Вашингтон, округ DC2, Монреаль, Квебек, Москва, Торонто |
| **Азия** | Восточная Азия, Юго-Восточная Азия |Гонконг, САР, Куала, Сингапур, Сингапур 2, Тайбэй |
| **Индия** | Западная Индия, Центральная Индия, Южная Индия |Ченнаи, Ченнаи 2, Мумбаи, Мумбаи 2 |
| **Япония** | Западная Япония, Восточная Япония |Осака, Токио |
| **Океания** | Восточная Австралия, Юго-Восточная Австралия |Г., Мельбурн, Перт, Сидней | 
| **Южная Корея** | Центральная Корея, Южная Корея |Пусан, Сеул|
| **ЧАСТЬ ОАЭ** | Центральная часть ОАЭ, Север ОАЭ | Дубаи, Dubai2 |
| **ЮАР** | Южно-Африканская Республика, Юго-Африканская Республика, Северная Африка |Кейптаун, Йоханнесбург |
| **Южная Америка** | Южная часть Бразилии |Сан-Паулу |

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

* **Локальные регионы Azure** — это те, к которым может получить доступ [Локальная служба ExpressRoute](expressroute-faqs.md) в каждом одноранговом расположении. **н/д** указывает, что локальная сеть ExpressRoute недоступна в этом расположении.

* **Зона** относится к [ценам](https://azure.microsoft.com/pricing/details/expressroute/).


### <a name="production-azure"></a>Рабочая среда Azure
| **Расположение** | **Address** | **Зона** | **Локальные регионы Azure** | **Прямое обращение к ER** | **Поставщики услуг** |
| --- | --- | --- | --- | --- | --- |
| **Амстердам** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | Западная Европа | 10G, 100G | Aryaka Networks, AT & T NetBond, Британская телекоммуникации, Colt, Equinix, Еунетворкс, ГÉАНТ, в облаке, Interxion, КПН, IX REACH, связь уровня 3, Orange, NTT связь, оранжевый, Tata Communications, Телефоника, Telenor, Телиа, Verizon, Zayo |
| **Амстердам 2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | Западная Европа | 10G, 100G | CenturyLink Cloud Connect, Colt, DE-ЦИКС, Еунетворкс, Interxion, Vodafone |
| **Атланта** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | Н/Д | Н/Д | Equinix, Megaport |
| **Окленд** | [Олбани группа вокус (NZ)](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | Н/Д | 10 | Деволи, Кордиа, Orange, Spark NZ, Вокус Group NZ |
| **Пусан** | [LG CNS](https://datacenter.lgcns.com/Contents/En/Menu_1/Locations_1.aspx) | 2 | Республика Корея, южный регион | Н/Д | LG CNS |
| **Канберра** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Центральная Австралия | Н/Д | CDC |
| **Канберра 2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Центральная Австралия 2| Н/Д | CDC |
| **Кейптаун** | [Терако CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | Западная часть ЮАР | 10 | Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **Ченнай** | Tata Communications | 2 | Южная Индия | Н/Д | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | 2 | Южная Индия | Н/Д | Airtel |
| **Чикаго** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | Центрально-северная часть США | 10G, 100G | Aryaka сети, в & T NetBond, CenturyLink Cloud Connect, Кологикс, Comcast, Коресите, Equinix, в облаке, Internet2, связи уровня 3, Orange, Паккетфабрик, ПККВ Global Limited, спринт, Телиа, Verizon, Zayo |
| **Копенгаген** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | Н/Д | 10 | Interxion |
| **Даллас** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | Н/Д | 10G, 100G | Aryaka Networks, AT & T NetBond, Кологикс, Equinix, Internet2, уровень 3, взаимодействие, Orange, Неутрона сети, Телмекс Унинет, Телиа перевозчик, Telco, Verizon, Zayo|
| **Денвер** | [Коресите DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | Центрально-западная часть США | Н/Д | Коресите, Orange, Zayo |
| **Дубаи** | [пккс](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | Северная часть ОАЭ | Н/Д | Етисалат ОАЭ |
| **Dubai2** | [Du датамена](http://datamena.com/solutions/data-centre) | 3 | Северная часть ОАЭ | Н/Д | Du датамена, Orange, Орикском |
| **Дублин** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Северная Европа | 10G, 100G | Colt, eir, Equinix, Interxion, Megaport |
| **Франкфурт** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | Центрально-Западная Германия | Н/Д | DE-ЦИКС, Interxion, оранжевый |
| **Geneva** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | Западная Швейцария | 10G, 100G | |
| **Гонконг, САР** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Восточная Азия | Н/Д | Aryaka сети, Британская телекоммуникации, CenturyLink Cloud Connect, главный телекоммуникации, Международная телекоммуникации, Глобальная, Equinix, Orange, NTT Communications, оранжевый, ПККВ глобально ограниченный, Tata обмен данными, Телиа, Verizon |
| **Йоханнесбург** | [Терако JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Северная часть ЮАР | Н/Д | Британская телекоммуникации, Интернет-решения — Cloud Connect, жидкостный телекоммуникации, оранжевый, Терако |
| **Куала-Лумпур** | [ВРЕМЯ, дотком Менара цель](https://www.aims.com.my/co-location/points-of-presence.html) | 2 | Н/Д | Н/Д | TIME dotCom |
| **Лас-Вегас** | [Переключение LV](https://www.switch.com/las-vegas) | 1 | Н/Д | Н/Д | CenturyLink Cloud Connect, Megaport |
| **Лондон** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | Южная часть Соединенного Королевства | 10G, 100G | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse — KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **Лондон 2** | [Два Севера для дома](https://www.telehouse.com/global-data-centers/emea/london-data-centers/telehouse-north-two/) | 1 | Южная часть Соединенного Королевства | 10G, 100G | IX-доступ, Equinix, дом — KDDI |
| **Лос-Анджелес** | [Коресите LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | Н/Д | Н/Д | Коресите, Equinix, Orange, Неутрона Networks, NTT, Telco, Zayo |
| **Марсель** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | Южная Франция | Н/Д | DE-ЦИКС, Interxion, Жагуар Network |
| **Мельбурн** | [Некстдк M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Юго-Восточная Австралия | 10G, 100G | Аарнет, Деволи, Equinix, Orange, НЕКСТДК, ОПТУС, Телстра Corporation, ТПГ телекоммуникации |
| **Майами** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | Н/Д | 10 | C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Милан** | [иридеос](https://irideos.it/en/data-centers/) | 1 | Н/Д | 10 | |
| **Монреаль** | [Кологикс MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | Н/Д | Н/Д | Bell для Канады, Кологикс, Orange, Телус, Zayo |
| **Мумбай** | Tata Communications | 2 | Западная Индия | Н/Д | Global Клаудксчанже (ГККС), зависимость ЖИО, Сифи, Tata Communications, Verizon |
| **Мумбаи 2** | Airtel | 2 | Западная Индия | Н/Д | Airtel, Sify, Vodafone Idea |
| **Мюнхене** | [еджеконнекс](https://www.edgeconnex.com/locations/europe/) | 1 | Н/Д | 10G, 100G | |
| **Нью-Йорк** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | Н/Д | 10G, 100G | CenturyLink Cloud Connect, Colt, Коресите, Equinix, в облаке, Orange, пакет, Zayo |
| **Ньюпорт (Уэльс)** | [Next Generation Data](https://www.nextgenerationdata.co.uk) | 1 | Западная часть Соединенного Королевства | Н/Д | Британская телекоммуникации, Colt, связь уровня 3, данные следующего поколения |
| **Осака** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Западная Япония | Н/Д | Colt, Equinix, Интернет-инициатива Япония Inc. — IIJ, NTT Communications, NTT Смартконнект, Softbank " |
| **Париж** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | Центральная Франция | Н/Д | CenturyLink Cloud Connect, Colt, Equinix, в облаке, Interxion, оранжевый, Телиа, Zayo |
| **Перт** | [Некстдк P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | Н/Д | 10 | Orange, Некстдк |
| **Квебек** | [Разных](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Восточная Канада | Н/Д | Bell Canada, Megaport |
| **Сан-Антонио** | [Цирусоне SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | Центрально-южная часть США | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Сан-Паулу** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Южная часть Бразилии | Н/Д | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Сиэтл;** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | Западная часть США 2 | 10G, 100G | Aryaka сети, Equinix, связь уровня 3, Orange, Телус, Zayo |
| **Сеул** | [КИНКС Гасан IDC](https://www.kinx.net/support/location/?lang=en) | 2 | Республика Корея, центральный регион | 10G, 100G | KINX, LG CNS, Sejong Telecom |
| **Кремниевая долина** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | Западная часть США | 10G, 100G | Aryaka Networks, AT & T NetBond, Британская телекоммуникации, CenturyLink Cloud Connect, Colt, Comcast, Коресите, Equinix, в облаке, Internet2, IX REACH, пакет, Паккетфабрик, связь уровня 3, Orange, оранжевый, спринт, Tata Communications, Телиа. Verizon, Zayo |
| **Полупроводниковая Valley2** | [Коресите SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | Западная часть США | 10G, 100G | Colt, Коресите | 
| **Сингапур** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Юго-Восточная Азия | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Сингапур 2** | [Глобальный коммутатор Тай Сенг](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Юго-Восточная Азия | 10G, 100G | Colt, Эпсилон Global Communications, Orange, SingTel |
| **ставанжер** | [Зеленый Mountain DC1](https://greenmountain.no/dc1-stavanger/) | 1 | Н/Д | 10G, 100G | |
| **Стокгольм** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | Н/Д | 10 | Equinix, оператор Телиа |
| **Сидней** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Восточная Австралия | 10G, 100G | Аарнет, AT & T NetBond, Британская телекоммуникации, Деволи, Equinix, Кордиа, Orange, НЕКСТДК, NTT Communications, ОПТУС, оранжевый, Spark NZ, Телстра Corporation, ТПГ Communications, Verizon, Вокус Group NZ |
| **Тайбэй** | Chief Telecom | 2 | Н/Д | 10 | Главный телекоммуникации, Фареастоне |
| **Токио** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Восточная Япония | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Торонто** | [Кологикс TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Центральная Канада | 10G, 100G | В & T NetBond, колокольчик Канады, CenturyLink Cloud Connect, Кологикс, Equinix, IX REACH Orange, Телус, Verizon, Zayo |
| **Вашингтон, округ Колумбия** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | Восточная часть США, Восточная часть США 2 | 10G, 100G | Aryaka сети, AT & T NetBond, Британская телекоммуникации, CenturyLink Cloud Connect, Кологикс, Comcast, Коресите, Equinix, Internet2, между облаком, связь уровня 3, Orange, Неутрона сети, NTT Communications, оранжевый, Паккетфабрик, SES, спринт, Tata Связь, перевозчик Телиа, Verizon, Zayo |
| **Вашингтон, округ Колумбия 2** | [Коресите Рестоне](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | Восточная часть США, Восточная часть США 2 | 10G, 100G | Коресите, ВИАСАТ, Zayo | 
| **Цюрих** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | Н/Д | Н/Д | В облаке, Interxion, компания Swisscom |

 **+** означает "скоро"

### <a name="national-cloud-environments"></a>Национальные облачные среды

### <a name="us-government-cloud"></a>Облако правительства США
| **Расположение** | **Address** | **Локальные регионы Azure**| **Прямое обращение к ER** | **Поставщики услуг** |
| --- | --- | --- | --- | --- |
| **Чикаго** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | Н/Д | 10G, 100G | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Даллас** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | Н/Д | 10G, 100G | Equinix, Megaport, Verizon |
| **Нью-Йорк** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | Н/Д | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [Цирусоне Чандлер](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | US Gov (Аризона) | Н/Д | В & T NetBond, CenturyLink Cloud Connect, Orange |
| **Сан-Антонио** | [Цирусоне SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | US Gov (Техас) | Н/Д | CenturyLink Cloud Connect, Megaport |
| **Кремниевая долина** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | Н/Д | 10G, 100G | Equinix, Level 3 Communications, Verizon |
| **Сиэтл;** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | Н/Д | Н/Д | Equinix, Megaport |
| **Вашингтон, округ Колумбия** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | Восточный регион US DoD, US Gov (Вирджиния) | 10G, 100G | В & T NetBond, CenturyLink Cloud Connect, Equinix, связь уровня 3, Orange, Verizon |

### <a name="china"></a>Китай
| **Расположение** | **Поставщики услуг** |
| --- | --- |
| **Пекин** |China Telecom |
| **Пекин 2** | Телекоммуникации в Китае, GDS |
| **Шанхай** |China Telecom |
| **Шанхай 2** | Телекоммуникации в Китае, GDS |

Дополнительные сведения см. на странице [ExpressRoute в Китае](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Германия
| **Расположение** | **Поставщики услуг** |
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

## <a name="connectivity-through-satellite-operators"></a>Подключение через вспомогательные операторы
Если вы используете удаленную и не имеете возможности оптоволоконного подключения или хотите изучить другие варианты подключения, можно проверить следующие вспомогательные операторы. 

* интелсат
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [виасат](http://www.directcloud.viasatbusiness.com/)

## <a name="c1partners"></a>Подключение через дополнительных поставщиков услуг
| **Расположение** | **Exchange** | **Поставщики услуг подключения** |
| --- | --- | --- |
| **Амстердам** | Equinix, Interxion, Обмен данными уровня 3 | Бикс, Клаудкспресс, Еурофибер, Фаствеб S. p. A, залива Bridge International, Калаам телекоммуникации Бахрейн B. S. C, Маиноне, Нианет, POST-Люксембург, Проксимус, TDC Ерхверв, телекоммуникационной Italia живы, Telekom Deutschland GmbH, Телиа |
| **Атланта** | Equinix| Самые Castle
| **Кейптаун** | Teraco | MTN |
| **Чикаго** | Equinix| Самые Castle, спектр для предприятий, Виндстреам |
| **Даллас** | Equinix, Megaport | Акстел, C3ntro, телекоммуникации, Кокс Business, самые Castle, обнаруженные данные, спектр предприятий, |
| **Франкфурт** | Interxion | Бикс, Циниа, Нианет, КСК AG, Telekom Deutschland GmbH |
| **Гамбург** | Equinix | Cinia |
| **Гонконг, САР** | Equinix | Chief, Macroview Telecom |
| **Йоханнесбург** | Teraco | MTN |
| **Лондон** | Бикс, Equinix, Еунетворкс| Безек International Ltd., Кореазуре, Эпсилон, ограниченная, экспоненциальная E, ХСО, Нексжен сетей, Проксимус, Тамарес телекоммуникации, Заину |
| **Лос-Анджелес** | Equinix |Самые Castle, спектр для предприятий, переtelco |
| **Мадрид** | Уровень3 | Zertia |
| **Монреаль** | Cologix, Equinix | Технологии аиргате, Inc. Аптум, Рожерс, Зирро |
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
## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения об ExpressRoute см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).
* Убедитесь, что выполнены все необходимые условия. См. статью [Предварительные требования и контрольный список для ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Карта расположения"
