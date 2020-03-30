---
title: Обзор сетевых сервисов Azure
description: Узнайте о сетевых сервисах Azure и их возможностях - службах подключения, службах защиты приложений, службах доставки приложений и сетевом мониторинге.
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: kumud
ms.openlocfilehash: 90eddea839d2f6ae5235ac6a391b40dd667ab401
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257841"
---
# <a name="azure-networking-services-overview"></a>Обзор сетевых сервисов Azure

Сетевые службы в Azure предоставляют различные сетевые возможности, которые можно использовать совместно или по отдельности. Щелкните одну из приведенных ниже ключевых возможностей, чтобы подробнее узнать о ней.
- [**Услуги подключения**](#connect): Подключение ресурсов Azure и ресурсов с помощью любых или комбинаций этих сетевых сервисов в Azure - Virtual Network (VNet), Virtual WAN, ExpressRoute, VPN Gateway, Виртуальной сети NAT Gateway, Azure DNS, сервиса пиринга и Azure Bastion.
- [**Услуги по защите приложений**](#protect) Защитите свои приложения, используя любые или комбинации этих сетевых служб в Azure - Private Link, DDoS-защиту, брандмауэр, группы сетевой безопасности, веб-приложения Firewall и виртуальные сетевые конечные точки.
- [**Услуги доставки заявок**](#deliver) Доставка приложений в сети Azure, используя любые или комбинации этих сетевых служб в Azure - Сеть доставки контента (CDN), служба передних дверей Azure, диспетчер трафика, шлюз приложений, анализатор Интернета и балансер нагрузки.
- [**Мониторинг сети**](#monitor) - Мониторинг сетевых ресурсов, используя любые или комбинации этих сетевых служб в Azure - Network Watcher, ExpressRoute Monitor, Azure Monitor или VNet Terminal Access Point (TAP).

## <a name="connectivity-services"></a><a name="connect"></a>Службы подключения
 
В этом разделе описаны службы, обеспечивающие подключение ресурсов Azure, подключение от персональной сети к ресурсам Azure и ветка для подключения филиала в Azure - Virtual Network (VNet), Virtual WAN, ExpressRoute, VPN Gateway, Виртуальная сеть NAT Gateway, Azure DNS, служба Пиринга Azure и Azure Bastion.

|Служба|Зачем использовать?|Сценарии|
|---|---|---|
|[Виртуальная сеть](#vnet)|Позволяет ресурсам Azure безопасно общаться друг с другом, с Интернетом и снес.| <p>[Фильтрация сетевого трафика](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[Маршрутизация сетевого трафика](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[Ограничение сетевого доступа к ресурсам](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[Подключение виртуальных сетей](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[Expressroute](#expressroute)|Расширяет свои сети в облаке Майкрософт по частному подключению, при содействии поставщика подключения.|<p>[Создание и изменение канала ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[Создание и изменение пиринга для канала ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[Связывание виртуальной сети с каналом ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[Настройка и управление фильтрами маршрутов для схем ExpressRoute](../expressroute/how-to-routefilter-portal.md)</p>|
|[VPN шлюз](#vpngateway)|Отправляет зашифрованный трафик между виртуальной сетью Azure и локальным местоположением через общедоступный Интернет.|<p>[Соединения от места к сайту](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[Соединения VNet-to-VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[Соединения от точки к месту](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[Виртуальная глобальная сеть](#virtualwan)|Оптимизирует и автоматизирует подключение ветки к Azure и через нее. Регионы Azure служат в качестве концентраторов, к которым вы можете подключить ветви.|<p>[Соединения от места к месту,](../virtual-wan/virtual-wan-site-to-site-portal.md) [соединения ExpressRoute](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[Azure DNS](#dns)|Размещает dNS-домены, обеспечивающие разрешение имен с помощью инфраструктуры Microsoft Azure.|<p>[Размещение домена в Azure DNS](../dns/dns-delegate-domain-azure-dns.md)</p><p>[Создание записей DNS для веб-приложения](../dns/dns-web-sites-custom-domain.md)</p> <p>[Создание псевдонима для менеджера трафика](../dns/tutorial-alias-tm.md)</p> <p>[Создание псевдонима для публичного IP-адреса](../dns/tutorial-alias-pip.md)</p> <p>[Создание записи псевдонима для записи ресурсов зоны](../dns/tutorial-alias-rr.md)</p>|
|[Бастион Azure](#bastion)|Настраивайте безопасное и бесперебойное подключение RDP или SSH к виртуальным машинам непосредственно на портале Azure по протоколу SSL. При подключении через Azure Bastion ваши виртуальные машины не нуждаются в общедоступном IP-адресе|<p>[Создание хоста Azure Bastion](../bastion/bastion-create-host-portal.md)</p><p>[Подключитесь с помощью SSH к Linux VM](../bastion/bastion-connect-vm-ssh.md)</p><p>[Подключение с помощью RDP к Windows VM](../bastion/bastion-connect-vm-rdp.md)</p>|
|[Виртуальная сеть NAT Gateway](#nat)|Создайте шлюз NAT для обеспечения исходящего подключения для виртуальной машины.|<p>[Создание шлюза NAT](../virtual-network/quickstart-create-nat-gateway-portal.md)</p>|
|[Служба пиринга Azure (Предварительный просмотр)](#azurepeeringservice)|Сотрудничайте с поставщиками услуг для оптимальной и надежной маршрутизации в облако Майкрософт по общедоступной сети.|<p>[Регистрация службы пиринга Azure](../peering-service/azure-portal.md)</p>|
||||


### <a name="virtual-network"></a><a name="vnet"></a>Виртуальная сеть

Виртуальная сеть (VNet) Azure — это стандартный строительный блок для вашей частной сети в Azure. Вы можете использовать VNets для:
- **Общайтесь между ресурсами Azure:** Вы можете развертывать виртуальные в виртуальной сети ресурсы Azure, такие как системы обслуживания приложений Azure, служба Azure Kubernetes (AKS) и наборы виртуальной шкалы масштабов azure. Полный список ресурсов Azure, которые можно развернуть в виртуальной сети, см. в статье [Интеграция виртуальной сети для служб Azure](../virtual-network/virtual-network-for-azure-services.md).
- **Общайтесь друг с другом:** Вы можете подключать виртуальные сети друг к другу, позволяя ресурсам в любой виртуальной сети общаться друг с другом, используя виртуальную сеть пиринга. Виртуальные сети, которые вы подключаете, могут находиться в одном или в разных регионах Azure. Дополнительные сведения см. в статье [Пиринг между виртуальными сетями](../virtual-network/virtual-network-peering-overview.md).
- **Общайтесь с Интернетом**: Все ресурсы в VNet могут общаться исходящих в Интернет, по умолчанию. Можно также установить входящее подключение к ресурсу, присвоив ему общедоступный IP-адрес, или общедоступный экземпляр Load Balancer. Вы также можете использовать [общедоступные IP-адреса](../virtual-network/virtual-network-public-ip-address.md) или public [Load Balancer](../load-balancer/load-balancer-overview.md) для управления исходящими соединениями.
- **Общайтесь с сетами:** Вы можете подключить свои компьютеры и сети к виртуальной сети с помощью [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) или [ExpressRoute.](../expressroute/expressroute-introduction.md)

Для получения дополнительной информации смотрите [Что такое виртуальная сеть Azure?.](../virtual-network/virtual-networks-overview.md)

### <a name="expressroute"></a><a name="expressroute"></a>Expressroute
ExpressRoute позволяет расширить свои сети в облаке Майкрософт через частное соединение, при содействии поставщика связи. Это подключение является закрытым. Трафик не проходит через Интернет. ExpressRoute позволяет устанавливать подключения к облачным службам Майкрософт, таким как Microsoft Azure, Office 365 и Dynamics 365.  Для получения дополнительной информации, смотрите [Что такое ExpressRoute?](../expressroute/expressroute-introduction.md).

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN шлюз
VPN Gateway помогает создавать зашифрованные кросс-предплеченные соединения с виртуальной сетью из мест расположения в помещениях или создавать зашифрованные соединения между VNets. Для VPN Gateway есть различные конфигурации, такие как, от места к сайту, точка к сайту, или VNet к VNet.
Следующая диаграмма иллюстрирует несколько VPN-соединений сайта к сайту в одной и той же виртуальной сети.

![Соединения VPN шлюза от сайта к сайту Azure](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

Для получения дополнительной информации о различных типах VPN-соединений, [см.](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="virtual-wan"></a><a name="virtualwan"></a>Виртуальная глобальная сеть
Виртуальная глобальная сеть Azure — это сетевая служба, которая обеспечивает оптимизированное и автоматизированное подключение типа "ветвь — ветвь" через Azure. Регионы Azure служат в качестве концентраторов, к которым вы можете подключить ветви. Вы можете использовать магистральную сеть Azure также для подключения ветвей и воспользоваться преимуществами подключения ветвей к виртуальной сети. Azure Virtual WAN объединяет многие облачные службы подключения Azure, такие как VPN от сайта к сайту, ExpressRoute, VPN от точки к месту. Подключение к виртуальным сетям Azure устанавливается с помощью подключения по виртуальной сети. Для получения дополнительной [What is Azure virtual WAN?](../virtual-wan/virtual-wan-about.md)информации см.

![Схема Виртуальной глобальной сети](./media/networking-overview/virtualwan1.png)

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Azure DNS является службой размещения доменов DNS, осуществляющей разрешение имен на базе инфраструктуры Microsoft Azure. Размещая домены в Azure, вы можете управлять своими записями DNS с помощью тех же учетных данных, API и инструментов и оплачивать использование, как и другие службы Azure. Для получения дополнительной [What is Azure DNS?](../dns/dns-overview.md)информации см.

### <a name="azure-bastion"></a><a name="bastion"></a>Бастион Azure
Служба Бастион Azure — это новая полностью управляемая платформой PaaS-служба, которая подготавливается в виртуальной сети. В ней предоставляется безопасное и бесперебойное подключение RDP или SSH к виртуальным машинам непосредственно на портале Azure по протоколу SSL. При подключении с помощью Бастиона Azure виртуальным машинам не требуются общедоступные IP-адреса. Для получения дополнительной информации, [см. Что такое Azure Бастион?](../bastion/bastion-overview.md).

![Архитектура Лазурного бастиона](./media/networking-overview/architecture.png)

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>Виртуальная сеть NAT Gateway
Виртуальная сеть NAT (перевод адресов сети) упрощает подключение к Интернету только для виртуальных сетей. При настройке в подсети все исходящие подключения используют указанные статические общедоступные IP-адреса. Исходящие подключения возможны без балансоостатков нагрузки или общедоступных IP-адресов, непосредственно прикрепленных к виртуальным машинам. Для получения дополнительной информации, смотрите [Что такое виртуальная сеть NAT шлюз?](../virtual-network/nat-overview.md) 

![Виртуальная сеть NAT шлюз](./media/networking-overview/flow-map.png)

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a>Служба пиринга Azure
Служба Azure Peering улучшает подключение клиентов к облачным службам Майкрософт, таким как Office 365, Dynamics 365, программному обеспечению как сервису (SaaS), Azure или любым службам Майкрософт, доступным через общедоступный Интернет. Для получения дополнительной [What is Azure Peering Service?](../peering-service/about.md)информации см.

## <a name="application-protection-services"></a><a name="protect"></a>Услуги по защите приложений

В этом разделе описаны сетевые службы в Azure, которые помогают защитить ваши сетевые ресурсы - Защита ваших приложений, использующих любые или комбинации этих сетевых служб в Azure - Private Link, DDoS-защиты, брандмауэра, группы сетевой безопасности, Веб Приложение Firewall, и виртуальные конечные точки сети.

|Служба|Зачем использовать?|Сценарий|
|---|---|---|
|[Защита от атак DDoS](#ddosprotection) |Высокая доступность для ваших приложений с защитой от избыточных сборов IP-трафика|[Управление защитой DDOS Azure](../virtual-network/manage-ddos-protection.md)|
|[Веб-приложение брандмауэр](#waf)|<p>[Azure WAF с Application Gateway](../web-application-firewall/ag/ag-overview.md) обеспечивает региональную защиту организаций в публичном и частном адресном пространстве</p><p>[Azure WAF с передней дверью](../web-application-firewall/afds/afds-overview.md) обеспечивает защиту на краю сети до общедоступных конечных точек.</p>|<p>[Настройка правил защиты ботов](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[Настройка кода отклика](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[Настройка правил ограничения ИС](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[Правило ограничения скорости настройки](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Брандмауэр Azure](#firewall)|Брандмауэр Azure — это управляемая облачная служба сетевой безопасности, которая защищает ресурсы виртуальной сети Azure. Это высокодоступная служба с полным отслеживанием состояния и неограниченными возможностями облачного масштабирования.|<p>[Развертывание брандмауэра Azure в Vnet](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[- Развертывание брандмауэра Azure в гибридной сети](../firewall/tutorial-hybrid-ps.md)</p> <p>[Фильтр входящий трафик с Azure Firewall DNAT](../firewall/tutorial-firewall-dnat.md)</p>|
|[Группы сетевой безопасности](#nsg)|Полный гранулированный контроль конечных узлов в VM/subnet для всех потоков сетевого трафика|[Фильтрация сетевого трафика с помощью групп безопасности сети](../virtual-network/tutorial-filter-network-traffic.md)|
|[Конечные точки виртуального сетевого обслуживания](#serviceendpoints)|Позволяет ограничить доступ к сети некоторым ресурсам службы Azure виртуальной сетевой подсети|[Настройка конечных точек служб виртуальной сети](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
[Приватный канал](#privatelink)|Позволяет получить доступ к службам Azure PaaS (например, база данных azure Storage и S'L Database), а Azure размещает услуги, принадлежащие клиенту/партнеру, через частную конечную точку в виртуальной сети.|<p>[Создание частной конечной точки](../private-link/create-private-endpoint-portal.md)</p><p>[Создание службы частной ссылки](../private-link/create-private-link-service-portal.md)</p>|
|||
### <a name="ddos-protection"></a><a name="ddosprotection"></a>Защита от DDoS 
[Azure DDoS Protection](../virtual-network/manage-ddos-protection.md) обеспечивает контрмеры против самых сложных DDoS-угроз. Служба предоставляет расширенные возможности dDoS-смягчения для вашего приложения и ресурсов, развернутых в виртуальных сетях. Кроме того, клиенты, использующие Azure DDoS Protection, имеют доступ к поддержке быстрого реагирования DDoS для привлечения DDoS-экспертов во время активной атаки.

![Защита от атак DDoS](./media/networking-overview/ddos-protection.png)

### <a name="web-application-firewall"></a><a name="waf"></a>Веб-приложение брандмауэр

[Azure Web Application Firewall](../web-application-firewall/overview.md) (WAF) обеспечивает защиту веб-приложений от общих веб-эксплойтов и уязвимостей, таких как инъекция s-L, и перекрестного скрипта сайта. Azure WAF обеспечивает защиту от 10 лучших уязвимостей OWASP с помощью управляемых правил. Кроме того, клиенты могут также настроить пользовательские правила, которые являются правилами управления клиентом, чтобы обеспечить дополнительную защиту на основе диапазона IP-адреса источника, и запрашивать атрибуты, такие как заголовки, файлы cookie, поля данных или параметры строки запроса.

Клиенты могут развернуть [Azure WAF с помощью портала приложений,](../application-gateway/waf-overview.md) который обеспечивает региональную защиту сущностей в общедоступном и частном адресном пространстве. Клиенты также могут развернуть [Azure WAF с front Door,](../frontdoor/waf-overview.md) который обеспечивает защиту на краю сети до общедоступных конечных точек.

![Брандмауэр веб-приложения](./media/networking-overview/waf-overview.png)


### <a name="azure-firewall"></a><a name="firewall"></a>Брандмауэр Azure
Брандмауэр Azure — это управляемая облачная служба сетевой безопасности, которая защищает ресурсы виртуальной сети Azure. Используя Azure Firewall, можно централизованно создавать, применять и регистрировать политики приложений и сетевых подключений в рамках подписки и виртуальных сетей. Брандмауэр Azure использует статический общедоступный IP-адрес для виртуальных сетевых ресурсов, позволяя внешним брандмауэрам идентифицировать трафик, исходящий из виртуальной сети. 

Для получения дополнительной информации о брандмауэре Azure можно ознакомиться с [документацией Брандмауэра Azure.](../firewall/overview.md)

![Общие сведения о брандмауэре](./media/networking-overview/firewall-threat.png)

### <a name="network-security-groups"></a><a name="nsg"></a>Группы сетевой безопасности
Отфильтровать трафик, поступающий из ресурсов Azure и обратно, в виртуальной сети можно с помощью группы безопасности сети. Дополнительные сведения см. в [обзоре безопасности](../virtual-network/security-overview.md).

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Конечные точки обслуживания
Конечные точки служб виртуальной сети расширяют пространство частных адресов и возможности идентификации вашей виртуальной сети в службах Azure благодаря прямому соединению. Конечные точки позволяют защищать критически важные ресурсы служб Azure в пределах отдельных виртуальных сетей. Трафик, поступающий из виртуальной сети в службу Azure, всегда остается в магистральной сети Microsoft Azure. Дополнительные сведения см. в статье [Конечные точки служб для виртуальной сети](../virtual-network/virtual-network-service-endpoints-overview.md).

![Конечные точки службы для виртуальной сети](./media/networking-overview/vnet-service-endpoints-overview.png)

### <a name="azure-private-link"></a><a name="privatelink"></a>Приватный канал Azure
[Azure Private Link](../private-link/private-link-overview.md) позволяет получить доступ к службам Azure PaaS (например, база данных azure Storage и S'L), а Azure размещает услуги, принадлежащие клиентам/партнерам, через частную конечную точку в виртуальной сети.
Трафик между виртуальной сетью и службой перемещается по магистральной сети Майкрософт. Разоблачение вашего сервиса в общественном интернете больше не является необходимым. Вы можете создать свой собственный частный сервис ссылки в виртуальной сети и доставить его своим клиентам.

![Обзор закрытой конечной точки](./media/networking-overview/private-endpoint.png)


## <a name="application-delivery-services"></a><a name="deliver"></a>Услуги доставки заявок

В этом разделе описаны сетевые службы azure, которые помогают предоставлять приложения - Network Watcher, ExpressRoute Monitor, Azure Monitor или VNet Terminal Access Point (TAP).

|Служба|Зачем использовать?|Сценарий|
|---|---|---|
|[Сеть доставки контента](#cdn)|Обеспечивает пользователи контентом с высокой пропускной способностью. CDN хранят кэшированное содержимое на крайних серверах в местах, близких к конечным пользователям, чтобы свести к минимуму задержку|<p>[Добавление CDN в веб-приложение](../cdn/cdn-add-to-web-app.md)</p> <p>[- Доступ к каплям для хранения с помощью пользовательского домена Azure CDN по httpS](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[Добавьте пользовательский домен в конечную точку Azure CDN](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[Настройка HTTPS на пользовательском домене Azure CDN](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate)</p>|
|[Azure Front Door Service](#frontdoor)|Позволяет определять, управлять и контролировать глобальную скорость для веб-трафика путем оптимизации на лучшую производительность и мгновенное глобальное сбой для высокой доступности.|<p>[Добавьте пользовательский домен в службу передних дверей Azure](../frontdoor/front-door-custom-domain.md)</p> <p>[Настройка протокола HTTPS на личном домене Front Door](../frontdoor/front-door-custom-domain-https.md)</p><p>[Настройка политики геофильтрации веб-приложений Брандмауэр](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[Менеджер трафика](#trafficmanager)|Распределяет трафик на основе DNS в службы в разных регионах Azure, обеспечивая при этом высокую доступность и отзывчивость|<p> [Перенаправление трафика для обеспечения минимальной задержки](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[Перенаправление трафика к приоритетной конечной точке](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [Управление трафиком с помощью весовых коэффициентов конечных точек](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[Трафик маршрута на основе географического положения конечной точки](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [Маршрутизация трафика через подсеть пользователя](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[Балансизатор нагрузки](#loadbalancer)|Обеспечивает региональную балансировку нагрузки путем перехивания трафика через зоны доступности и в ваши VNets. Обеспечивает внутреннюю балансировку нагрузки путем перехивания трафика по всему и между ресурсами для создания регионального приложения.|<p> [Балансировка интернет-трафика на виртуальных машинах](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[Трафик и баланс нагрузки через Виртуальные виртуальные сети](../load-balancer/tutorial-load-balancer-basic-internal-portal.md)<p>[Порт-форвардный трафик в конкретный порт на конкретных ВМ](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [Настройка балансировки нагрузки и исходящих правил](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[Шлюз приложений](#applicationgateway)|Шлюз приложений Azure — это подсистема балансировки нагрузки веб-трафика, предназначенная для управления трафиком веб-приложений.|<p>[Маршрутизация веб-трафика с помощью Шлюза приложений](../application-gateway/quick-create-portal.md)</p><p>[Настройка шлюза приложений с завершением SSL-запросов](../application-gateway/create-ssl-portal.md)</p><p>[Создание шлюза приложений с перенаправлением на основе URL-пути](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="content-delivery-network"></a><a name="cdn"></a>Сеть доставки контента
Azure CDN предлагает разработчикам глобальное решение для быстрой доставки больших объемов содержимого пользователям путем кэширования содержимого на стратегически расположенных физических узлах по всему миру. Для получения дополнительной информации о [Azure Content Delivery Network](../cdn/cdn-overview.md) Azure CDN см.

![Azure CDN](./media/networking-overview/cdn-overview.png)

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Служба передних дверей Azure
Azure Front Door Service позволяет определять, управлять и отслеживать глобальную маршрутизацию для вашего веб-трафика посредством оптимизации для обеспечения наилучшей производительности и мгновенной глобальной отработки отказа для обеспечения высокой доступности. С помощью службы Front Door вы можете преобразовать глобальные (с поддержкой нескольких регионов) пользовательские и корпоративные приложения в современные, надежные, высокопроизводительные и персонализированные приложения, интерфейсы API и содержимое, которые охватывают глобальную аудиторию с помощью Azure. Для получения дополнительной [Azure Front Door](../frontdoor/front-door-overview.md)информации см.


### <a name="traffic-manager"></a><a name="trafficmanager"></a>Менеджер трафика

Диспетчер трафика Azure — это подсистема балансировки нагрузки трафика на основе DNS, которая позволяет оптимально распределять трафик между службами во всех регионах Azure, обеспечивая высокий уровень доступности и скорости реагирования. Диспетчер трафика предоставляет ряд методов регулировки трафика для распределения трафика, таких как приоритет, взвешенный, производительность, географический, мульти-стоимость или подсеть. Для получения дополнительной информации о методах traffic routing, [Traffic Manager routing methods](../traffic-manager/traffic-manager-routing-methods.md)см.

На следующей диаграмме показана конечная приоритетная скорость с помощью диспетчера трафика:

![Диспетчер трафика Azure: маршрутизация трафика по приоритету](./media/networking-overview/priority.png)

Для получения дополнительной информации об менеджере трафика [смотрите, что такое менеджер трафика Azure?](../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>Балансизатор нагрузки
Azure Load Balancer обеспечивает высокую производительность и балансировку нагрузки уровня 4 с низкой задержкой для всех протоколов UDP и TCP. Она управляет и входящими, и исходящими подключениями. Вы можете настроить общедоступные и внутренние конечные точки с балансировкой нагрузки, а также определить правила для сопоставления входящих подключений к внутреннему пулу, используя параметры проверки состояний TCP и HTTP, чтобы управлять доступностью служб. Дополнительные сведения о Load Balancer см. в [этой статье](../load-balancer/load-balancer-overview.md).

На схеме ниже показано многоуровневое приложение с доступом к Интернету, использующее внешние и внутренние балансировщики нагрузки:

![Пример баланса загрузки Azure](./media/networking-overview/load-balancer.png)


### <a name="application-gateway"></a><a name="applicationgateway"></a>Шлюз приложений
Шлюз приложений Azure — это подсистема балансировки нагрузки веб-трафика, предназначенная для управления трафиком веб-приложений. Это контроллер доставки приложений (ADC) как услуга, предлагающая различные возможности балансировки нагрузки 7 уровня 7 для ваших приложений. Для получения дополнительной [What is Azure Application Gateway?](../application-gateway/overview.md)информации см.

На следующей диаграмме показана маршрутизирующая маршрутизм url-адреса с помощью шлюза приложения.

![Пример шлюза приложения](./media/networking-overview/figure1-720.png)

## <a name="network-monitoring-services"></a><a name="monitor"></a>Услуги сетевого мониторинга
В этом разделе описаны сетевые службы в Azure, которые помогают контролировать сетевые ресурсы - Network Watcher, ExpressRoute Monitor, Azure Monitor и Virtual Network TAP.

|Служба|Зачем использовать?|Сценарий|
|---|---|---|
|[Наблюдатель за сетями](#networkwatcher)|Помогает отслеживать и устрашать проблемы подключения, помогает диагностировать проблемы VPN, NSG и разгрома, захватывать пакеты на Вашем ВМ, автоматизирует запуск диагностических средств с помощью функций Azure и логических приложений|<p>[Диагностика проблем с фильтрацией трафика виртуальной машины](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[Диагностика проблем с маршрутизацией трафика виртуальной машины](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[Мониторинг связи между VMs](../network-watcher/connection-monitor.md)</p><p>[Руководство по диагностике проблем с обменом данными между сетями](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[Руководство. Регистрация потока входящего и исходящего сетевого трафика виртуальной машины с помощью портала Azure](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[Монитор ЭкспрессРут](#expressroutemonitor)|Обеспечивает мониторинг производительности, доступности и использования сети в режиме реального времени, помогает с автоматическим открытием топологии сети, обеспечивает более быструю изоляцию неисправностей, обнаруживает переходные проблемы сети, помогает анализировать историческую производительность сети характеристики, поддерживает мультиподписку|<p>[Настройка решения "Монитор производительности сети" для ExpressRoute](../expressroute/how-to-npm.md)</p><p>[Мониторинг, метрики и оповещения в ExpressRoute](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure Monitor](#azuremonitor)|Помогает понять, как работают приложения, и упреждающе определяет проблемы, затрагивающие их, и ресурсы, от которых они зависят.|<p>[Метрики и оповещения диспетчера трафика](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[Диагностика монитора Azure для балансоровой стандартной нагрузки](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[Мониторинг журналов и метрик Брандмауэра Azure](../firewall/tutorial-diagnostics.md)</p><p>[Мониторинг и ведение журнала для брандмауэра веб-приложения Azure](../frontdoor/waf-front-door-monitor.md)</p>|
|[Виртуальная сеть TAP](#vnettap)|Обеспечивает непрерывную потоковую передачу сетевого трафика виртуальной машины на сборщик пакетов, позволяет решения по управлению производительностью сети и приложения и инструменты анализа безопасности|[Создание ресурса VNet TAP](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="network-watcher"></a><a name="networkwatcher"></a>Наблюдатель за сетями
Наблюдатель за сетями Azure предоставляет инструменты для мониторинга, диагностики, просмотра метрик и включения или отключения журналов для ресурсов в виртуальной сети Azure. Для получения дополнительной информации, [см. Что такое сеть Watcher?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>Монитор ЭкспрессРут
Чтобы узнать о том, как просматривать метрики схем ExpressRoute, диагностические журналы и оповещения, смотрите [мониторинг, метрики и оповещения ExpressRoute.](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json)
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
Служба Azure Monitor обеспечивает максимальную доступность и производительность приложений, предоставляя полноценное решение для сбора, анализа и обработки данных телеметрии из облачных и локальных сред. Она поможет вам понять, как выполняются приложения, а также заранее определить проблемы, влияющие на них, и ресурсы, от которых они зависят. Для получения дополнительной [Azure Monitor Overview](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json)информации см.
### <a name="virtual-network-tap"></a><a name="vnettap"></a>Виртуальная сеть TAP
TAP (точка доступа к терминалу) виртуальной сети Azure позволяет непрерывно передавать сетевой трафик виртуальной машины в сборщик сетевых пакетов или средство аналитики. Инструмент для коллектора или аналитики предоставляется [партнером по виртуальному прибору сети.](https://azure.microsoft.com/solutions/network-appliances/) 

Ниже показано, как работает TAP виртуальной сети. 

![Как работает TAP виртуальной сети](./media/networking-overview/virtual-network-tap-architecture.png)

Для получения дополнительной информации, смотрите [Что такое виртуальная сеть TAP](../virtual-network/virtual-network-tap-overview.md).

## <a name="next-steps"></a>Дальнейшие действия

- Создайте первую виртуальную сеть и подключите к ней несколько виртуальных машин, выполнив действия, описанные в [этой статье](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Подключите компьютер к VNet, выполнив этапы в [статье «Настройка по месту подключения к сайту».](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
- Реализуйте балансировку интернет-трафика на общедоступные серверы, выполнив действия, описанные в статье [Создание подсистемы балансировки нагрузки для Интернета на портале Azure](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
 
 
   
