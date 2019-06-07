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
ms.date: 05/31/2019
ms.author: jaredr80
ms.openlocfilehash: c443d4705da83a43af10763f633f340b1eab69d3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66478161"
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
| **Европа** | 1 |Центральная Франция, Южная Франция, Северная Европа, Западная Европа, западная часть Соединенного Королевства, южная часть Соединенного Королевства |Амстердам, Amsterdam2, Дублин, Франкфурт, Лондон, London2, Марселе, ньюпорт (Уэльс), Париж, Цюрих |
| **Северная Америка** | 1 |Восточная часть США, западная часть США, восточная часть США 2, западная часть США 2, центральная часть США, центрально-южная часть США, центрально-северная часть США, центрально-западная часть США, Центральная Канада, Восточная Канада |Атланта, Чикаго, Даллас, Денвер, Лас-Вегас, Лос-Анджелес, Майами, Нью-Йорк, Сан-Антонио, Сиэтл, кремниевая Долина, кремниевая Valley2, DC2 Вашингтон Округ Колумбия, штат Вашингтон, Монреаль, Квебек, Торонто |
| **Азия** | 2 |Восточная Азия, Юго-Восточная Азия |Гонконг (САР), Куала-Лумпур, Сингапур, Сингапур 2, Тайбэй |
| **Австралия** | 2 |Восточная Австралия, Юго-Восточная Австралия |Мельбурн, Перт, Сидней | 
| **Индия** | 2 |Западная Индия, Центральная Индия, Южная Индия |Ченнаи, Ченнаи 2, Мумбаи, Мумбаи 2 |
| **Япония** | 2 |Западная Япония, Восточная Япония |Осака, Токио |
| **Южная Корея** | 2 |Центральная Корея, Южная Корея |Пусан, Сеул|
| **ОБЪЕДИНЕННЫЕ АРАБСКИЕ ЭМИРАТЫ** | 3 | Центральная часть ОАЭ, Северная часть ОАЭ | Дубаи, Dubai2 |
| **ЮАР** | 3 |Запад ЮАР, Север ЮАР |Кейптаун, Йоханнесбург |
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

В таблице ниже приведены расположения для подключения и поставщики услуг в каждом расположении. Поставщики услуг и расположения, в которых они предоставляют услуги, перечислены в разделе [Расположения поставщиков услуг подключения](expressroute-locations.md#locations). 

**Локальных регионах Azure** — те, [ExpressRoute локальной](expressroute-faqs.md) в каждом расположении пиринга можно получить доступ к. **н/д** указывает, что в этом расположении пиринга ExpressRoute локальной недоступна.


### <a name="production-azure"></a>Рабочая среда Azure
| **Местоположение.** | **Владелец однорангового расположения** | **Локальных регионах Azure** | **Поставщики услуг** |
| --- | --- | --- | --- |
| **Амстердам** | Equinix | Западная Европа | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, TeleCity Group, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Амстердам 2** | Interxion | Западная Европа | DE-CIX, Interxion |
| **Атланта** | Equinix | Недоступно | Equinix, Megaport |
| **Пусан** |LG CNS | Корея, юг | LG CNS |
| **Канберра** | CDC | Центральная Австралия | CDC |
| **Канберра 2** | CDC | Центральная Австралия 2| CDC |
| **Кейптаун** | Teraco | Запад ЮАР | Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **Ченнай** | Tata Communications | Южная Индия | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | Южная Индия | Airtel |
| **Чикаго** | Equinix | Центрально-северная часть США | Aryaka Networks, AT&T NetBond, Cologix, Comcast, Coresite, Equinix, InterCloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Даллас** | Equinix | Недоступно | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Денвер** | CoreSite | Западно-центральная часть США | CoreSite, Megaport, Zayo |
| **Дубаи** | Etisalat ОАЭ | Северная часть ОАЭ | Etisalat ОАЭ |
| **Dubai2** | du datamena | Северная часть ОАЭ | du datamena, Orixcom |
| **Дублин** | Equinix | Северная Европа | Colt, eir, Equinix, Interxion, Megaport |
| **Франкфурт** | Interxion | Недоступно | DE-CIX, Interxion |
| **Гонконг (САР)** | Equinix | Восточная Азия | Aryaka Networks, British Telecom, China Telecom Global, Equinix, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Verizon |
| **Йоханнесбург** | Teraco | Север ЮАР | Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **Куала-Лумпур** | TIME dotCom | Недоступно | TIME dotCom |
| **Лас-Вегас** | Параметр | Недоступно | CenturyLink Cloud Connect, Megaport |
| **Лондон** | Equinix | Южная часть Великобритании | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse — KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **Лондон 2** | Telehouse | Южная часть Великобритании | IX Reach, Equinix |
| **Лос-Анджелес** | CoreSite | Недоступно | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **Марсель** |Interxion | Южная Франция | Interxion |
| **Мельбурн** | NextDC | Юго-Восточная часть Австралии | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Майами** | Equinix | Недоступно | C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Монреаль** | Cologix | Недоступно | Bell Canada, Cologix, Telus, Zayo |
| **Мумбай** | Tata Communications | Западная Индия | Глобальный CloudXchange (GCX), Jio зависимость, Sify, Tata Communications, Verizon |
| **Мумбаи 2** | Airtel | Западная Индия | Airtel, Sify, Vodafone Idea |
| **Нью-Йорк** | Equinix | Недоступно | CenturyLink Cloud Connect, Coresite, Equinix, InterCloud, Megaport, пакетов, Zayo |
| **Ньюпорт (Уэльс)** | Next Generation Data | Западная часть Великобритании | British Telecom, Colt, Level 3 Communications, следующего поколения данных |
| **Осака** | Equinix | Западная часть Японии | Equinix, Internet Initiative Japan Inc. (IIJ), NTT Communications, NTT SmartConnect, Softbank |
| **Париж** | Interxion | Центральная Франция | Colt, Equinix, Intercloud, Interxion, оранжевый, Telia перевозчика, Zayo |
| **Перт** | NextDC | Недоступно | Megaport, NextDC |
| **Квебек** | 4Degrees | Восточная Канада | Bell Canada, Megaport |
| **Сан-Антонио** | CyrusOne | Центрально-южная часть США | CenturyLink Cloud Connect, Megaport |
| **Сан-Паулу** | Equinix | Южная часть Бразилии | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Сиэтл;** | Equinix | Западный регион США 2 | Aryaka Networks, Equinix, Level 3 Communications, Megaport, поддержкой, Zayo |
| **Сеул** | KINX | Центральная Корея | KINX, LG CNS, Sejong Telecom |
| **Кремниевая долина** | Equinix | Запад США | Aryaka Networks, AT & T NetBond, British Telecom, CenturyLink Cloud Connect, Comcast, Coresite, Equinix, InterCloud, достигают IX, пакетов, PacketFabric, Level 3 Communications, Megaport, оранжевый, спринта, Tata Communications, Verizon, Zayo |
| **Кремниевая Valley2** | Coresite | Запад США | Coresite | 
| **Сингапур** | Equinix | Юго-Восточная Азия | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Сингапур 2** | Global Switch | Юго-Восточная Азия | Colt, Epsilon Global Communications, Megaport, SingTel |
| **Сидней** | Equinix | Восточная часть Австралии | AARNet, AT&T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Telstra Corporation, TPG Telecom, Verizon |
| **Тайбэй** | Chief Telecom | Недоступно | Руководитель затратами на телекоммуникации, FarEasTone |
| **Токио** | Equinix | Восточная часть Японии | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Торонто** | Cologix | Центральная Канада | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **Вашингтон, округ Колумбия** | Equinix | Восточная часть США, восточная часть США 2 | Aryaka Networks, AT & T NetBond, British Telecom, Cologix, Comcast, Coresite, Equinix, Internet2, InterCloud, Level 3 Communications, Megaport, Neutrona сетей, NTT Communications, Orange, PacketFabric, спринта, Tata Communications, несущей Telia Verizon, Zayo |
| **Вашингтон, округ Колумбия 2** | Coresite | Восточная часть США, восточная часть США 2 |Coresite | 
| **Цюрих** | Interxion | Недоступно | Interxion |

 **+** означает "скоро"

### <a name="national-cloud-environments"></a>Национальные облачные среды

### <a name="us-government-cloud"></a>Облако правительства США
| **Местоположение.** | **Поставщики услуг** |
| --- | --- |
| **Чикаго** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Даллас** |Equinix, Megaport, Verizon |
| **Нью-Йорк** |Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | AT&T NetBond, CenturyLink Cloud Connect, Megaport |
| **Сан-Антонио** | CenturyLink Cloud Connect, Megaport |
| **Кремниевая долина** | Equinix, Level 3 Communications, Verizon |
| **Сиэтл;** | Equinix, Megaport |
| **Вашингтон, округ Колумбия** |AT&T NetBond, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Китай
| **Местоположение.** | **Поставщики услуг** |
| --- | --- |
| **Пекин** |China Telecom |
| **Пекин 2** | Китай затратами на телекоммуникации, GDS |
| **Шанхай** |China Telecom |
| **Шанхай 2** | Китай затратами на телекоммуникации, GDS |

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

## <a name="c1partners"></a>Подключение через дополнительных поставщиков услуг
| **Местоположение.** | **Exchange** | **Поставщики услуг подключения** |
| --- | --- | --- |
| **Амстердам** | Equinix, Telecity | BICS, CloudXpress, Eurofiber, Fastweb S.p.A, Gulf Bridge International, MainOne, Nianet, Post, Proximus, TDC Erhverv, Telecom Italia Sparkle, Telia |
| **Кейптаун** | Teraco | MTN |
| **Чикаго** | Equinix | Windstream Lightower Enterprise спектра |
| **Даллас** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Data Foundry, Spectrum Enterprise, Transtelco |
| **Франкфурт** | Telecity | BICS, Cinia, Nianet, QSC AG |
| **Гамбург** | Equinix | Cinia |
| **Гонконг (САР)** | Equinix | Chief, Macroview Telecom |
| **Йоханнесбург** | Teraco | MTN |
| **Лондон** | BICS, Equinix, euNetworks, Telecity | Bezeq International Ltd., CoreAzure Эпсилон телекоммуникаций Limited, экспоненциального E, HSO, NexGen сетей, Proximus, Tamares затратами на телекоммуникации, Набулси |
| **Лос-Анджелес** | Equinix |Enterprise спектра, Transtelco |
| **Мадрид** | Level3 | Zertia |
| **Монреаль** | Cologix, Equinix | Airgate Technologies, Inc. Cogeco Peer 1, Rogers, Zirro |
| **Нью-Йорк** |Equinix, Megaport | Altice, Lightower, спектра для предприятий, Webair |
| **Париж** | Equinix | Proximus |
| **Квебек** | Megaport | Fibrenoire |
| **Сан-Паулу** | Equinix | Venha Pra Nuvem |
| **Сиэтл;** |Equinix | Alaska Communications |
| **Кремниевая долина** |Equinix | Business, Enterprise спектра Windstream Кокс |
| **Сингапур** |Equinix |1CLOUDSTAR, BICS, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Сидней** | Megaport | Macquarie Telecom Group|
| **Токио** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Торонто** | Equinix | Airgate Technologies, Inc. Cogeco Peer 1, IVedha Inc, Rogers, Thinktel, Zirro|
| **Вашингтон, округ Колумбия** |Equinix | Altice Business, BICS, Cox Business, Gtt Communications Inc, Epsilon Telecommunications Limited, Lightower, Masergy, Windstream |

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
* Убедитесь, что выполнены все необходимые условия. Ознакомьтесь с разделом [Предварительные требования и контрольный список для ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Карта расположения"
