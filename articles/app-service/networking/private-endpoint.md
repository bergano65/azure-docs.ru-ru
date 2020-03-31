---
title: Подключение к веб-приложению с помощью Azure Private Endpoint
description: Подключение к веб-приложению с помощью Azure Private Endpoint
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/18/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: c2717b1f29af39c6fdc4602b11acba131d959f03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534394"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Использование частных конечных точек для веб-приложения Azure (Предварительный просмотр)

> [!Note]
> Предварительный просмотр доступен в Восточной США и Западе США 2 регионов для всех PremiumV2 Windows и Linux Web Apps и упругие премиум функции. 

Вы можете использовать Private Endpoint для вашего web-приложения Azure, чтобы клиенты, находящимися в вашей частной сети, могли получить доступ к приложению через Private Link. Частная конечная точка использует IP-адрес из вашего пространства адреса Azure VNet. Сетевой трафик между клиентом в вашей частной сети и Веб-приложением пересекает VNet и частную ссылку на магистральной сети Microsoft, исключая воздействие общедоступного Интернета.

Использование частной конечных точек для веб-приложения позволяет:

- Защитите web-приложение, настройвая конечную точку службы, устраняя общедоступное воздействие.
- Безопасное подключение к Web App из закрытых сетей, которые подключаются к VNet с помощью VPN или ExpressRoute частных пиринг.

Если вам просто нужно безопасное соединение между VNet и веб-приложением, то конечная точка службы является самым простым решением. Если вам также нужно попасть в веб-приложение из предварительного через шлюз Azure, региональный VNet или глобально заглянул VNet, Private Endpoint является решением.  

Для получения дополнительной [Service Endpoints][serviceendpoint]информации см.

## <a name="conceptual-overview"></a>Общие сведения

Частная конечная точка — это специальный сетевой интерфейс (NIC) для вашего веб-приложения Azure в подсети в виртуальной сети (VNet).
При создании частной конечный точки для веб-приложения она обеспечивает безопасное подключение между клиентами в вашей частной сети и веб-приложением. Частной конечной точке присваивается IP-адрес из диапазона IP-адресов вашего VNet.
Соединение между частной конечной точкой и веб-приложением использует безопасную [частную ссылку.][privatelink] Частная конечная точка используется только для входящих потоков в веб-приложение. Исходящие потоки не будут использовать эту частную конечную точку, но вы можете вводить исходящие потоки в свою сеть в другой подсети через [функцию интеграции VNet.][vnetintegrationfeature]

Поднет, где вы подключите частную конечную точку может иметь другие ресурсы в нем, вам не нужно выделенный пустой Subnet.
Вы также можете развернуть частную конечную точку в другом регионе, чем Web App. 

> [!Note]
>Функция интеграции VNet не может использовать ту же подсеть, что и Private Endpoint, это ограничение функции интеграции VNet.

С точки зрения безопасности:

- При включании частных конечных точек в веб-приложение вы отключаете все общедоступные приложения.
- Вы можете включить несколько частных конечных точек в других VNets и Subnets, включая VNets в других регионах.
- IP-адрес Private Endpoint NIC должен быть динамическим, но оставаться неизменным до тех пор, пока вы не удалите частную конечную точку.
- NIC частной конечной точки не может иметь NSG, связанный.
- Подсеть, в размещении в котором находится частная конечная точка, может иметь связанную с NSG, но необходимо отключить соблюдение политик сети для частной конечных точек: [см.][disablesecuritype] В результате вы не можете отфильтровать с помощью любой NSG доступ к вашей частной точке.
- При включании частной конечной точки к веб-приложению конфигурация [ограничений доступа][accessrestrictions] в Web-приложении не оценивается.
- Вы можете уменьшить риск эксфильтрации данных из VNet, удалив все правила NSG, где назначение является тегом Интернет или услуги Azure. Но добавление Web App Private Endpoint в подсеть позволит вам достичь любого веб-приложения, размещенного в той же отметке развертывания и подверженных интернету.

В web HTTP журналы вашего веб-приложения, вы найдете исправления источника клиента. Это реализовано с помощью протокола TCP Proxy, перенаправляющего свойство ИС клиента в Веб-приложение. Для получения дополнительной информации [см. Получение информации о подключении с помощью TCP Proxy v2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Web App Private Endpoint глобальный обзор](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Поскольку эта функция находится в предварительном просмотре, мы не изменяем запись DNS во время предварительного просмотра. Вам необходимо самостоятельно управлять записью DNS на частном DNS-сервере или в частной зоне Azure DNS.
Если вам нужно использовать пользовательское имя DNS, необходимо добавить пользовательское имя в web-приложение. Во время предварительного просмотра пользовательское имя должно быть проверено, как и любое пользовательское имя, с использованием общедоступного разрешения DNS. Для получения дополнительной информации можно ознакомиться с [пользовательской проверкой DNS.][dnsvalidation]

## <a name="pricing"></a>Цены

Дополнительные сведения о ценах см. на [странице цен на службу "Приватный канал" Azure][pricing].

## <a name="limitations"></a>Ограничения

Мы регулярно совершенствуем функцию Private Link и Private Endpoint, проверяем [эту статью][pllimitations] на наличие актуальной информации об ограничениях.

## <a name="next-steps"></a>Дальнейшие действия

Развертывание частной конечной точки для веб-приложения через портал [см., как подключиться к веб-приложению][howtoguide]




<!--Links-->
[serviceendpoint]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[privatelink]: https://docs.microsoft.com/azure/private-link/private-link-overview
[vnetintegrationfeature]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[disablesecuritype]: https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy
[accessrestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain
[pllimitations]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal
