---
title: Решение Azure VMware от CloudSimple - Растяжение сети уровня 2 на закрытом облаке
description: Описывает, как настроить VPN уровня 2 между NSX-T в облачном частном облаке и в предварительном автономном клиенте NSX Edge
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1f5ff48f4d5a658a1bbb4e6b9fb4b3f0f3fb190f
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81602691"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>Перенос рабочих нагрузок с помощью растянутых сетей уровня 2

В этом руководстве вы узнаете, как использовать 2-й слой VPN (L2VPN), чтобы растянуть сеть слоя 2 от вашей предварительной среды до облачного частного облака. Это решение позволяет перемещать рабочие нагрузки, работающие в среде VMware, в частное облако в Azure в рамках одного и того же адресного пространства подсети без необходимости повторного IP-нагрузок.

L2VPN на основе растяжения слоя 2 сетей может работать с или без NSX основе сетей в вашей предварительной среде VMware. Если у вас нет сетей на основе NSX для рабочих нагрузок на месте, вы можете использовать автономный шлюз NSX Edge Services.

> [!NOTE]
> Данное руководство охватывает сценарий, при котором на местах и центры обработки данных Private Cloud подключены через сайт к сайту VPN.

## <a name="deployment-scenario"></a>Сценарий развертывания

Чтобы растянуть локальная сеть с помощью L2VPN, необходимо настроить сервер L2VPN (направление маршрутизаторNS NSX-T Tier0) и клиент L2VPN (клиент-источник).  

В этом сценарии развертывания ваше приватное облако подключено к вашей предварительной среде через VPN-туннель сайта к сайту, который позволяет подсетям на местах и подсетям vMotion общаться с подсетями Private Cloud и подсетями vMotion. Эта договоренность необходима для Cross vCenter vMotion (xVC-vMotion). Маршрутизатор NSX-T Tier0 используется в качестве сервера L2VPN в частном облаке.

Автономный NSX Edge развертывается в вашей предварительной среде в качестве клиента L2VPN и впоследствии в паре с сервером L2VPN. Конечная точка туннеля GRE создается с каждой стороны и настроена для «растяжения» напредся сети слоя 2 до вашего частного облака. Эта конфигурация изображена на следующей фигуре.

![Сценарий развертывания](media/l2vpn-deployment-scenario.png)

Чтобы узнать больше о миграции с помощью L2 VPN, [см.](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704)

## <a name="prerequisites-for-deploying-the-solution"></a>Предпосылки для развертывания решения

Убедитесь, что перед развертыванием и настройкой решения будут приведены следующие данные:

* В предприимная версия vSphere составляет 6.7U1 или 6.5P03.
* Лицензия на vSphere находится на уровне Enterprise Plus (для vSphere Distributed Switch).
* Определите рабочую нагрузку Layer 2 сети, которая будет растянута на ваше приватное облако.
* Определите сеть уровня 2 в вашей предварительной среде для развертывания клиентского прибора L2VPN.
* [Частное облако уже создано.](create-private-cloud.md)
* Версия автономного прибора NSX-T Edge совместима с версией NSX-T Manager (NSX-T 2.3.0), используемой в вашей среде Private Cloud.
* Группа порта хобота была создана в on-premises vCenter с поддельными передачами включенными.
* Публичный IP-адрес был зарезервирован для использования для автономного IP-адреса клиента NSX-T, и 1:1 NAT имеется для перевода между двумя адресами.
* Переадресировка DNS устанавливается на закрытых DNS-серверах для az.cloudsimple.io домена для отославания на серверы Private Cloud DNS.
* RTT задержка меньше или равна 150 мс, как это требуется для vMotion для работы на двух сайтах.

## <a name="limitations-and-considerations"></a>Ограничения и рекомендации

Следующие таблицы поддерживаются версиями vSphere и типами сетевого адаптера.  

| vСфера версия | Тип исходного кода vSwitch | Виртуальный драйвер NIC | Целевой vSwitch Тип | Поддержка |
------------ | ------------- | ------------ | ------------- | ------------- 
| Все | Dvs | Все | Dvs | Да |
| vСфера 6.7UI или выше, 6.5P03 или выше | Dvs | VMXNET3 | N-VDS | Да |
| vСфера 6.7UI или выше, 6.5P03 или выше | Dvs | E1000 | N-VDS | [Не поддерживается на VWware](https://kb.vmware.com/s/article/56991) |
| vSphere 6.7UI или 6.5P03, NSX-V или версии ниже NSX-T2.2, 6.5P03 или выше | Все | Все | N-VDS | [Не поддерживается на VWware](https://kb.vmware.com/s/article/56991) |

По состоянию на VMware NSX-T 2.3 релиз:

* Логический переключатель на стороне private Cloud, которая растягивается на помещение через L2VPN, не может быть направлен одновременно. Растянутый логический переключатель не может быть подключен к логическому маршрутизатору.
* L2VPN и на основе маршрутов IPSEC VPN могут быть настроены только с помощью вызовов API.

Для получения дополнительной информации смотрите [виртуальные частные сети](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) в документации VMware.

### <a name="sample-l2-vpn-deployment-addressing"></a>Пример адресации развертывания L2 VPN

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>На территории сети, где развернут автономный ESG (L2 VPN клиент)

| **Элемент** | **Значение** |
|------------|-----------------|
| Сетевое имя | MGMT_NET_VLAN469 |
| Виртуальная локальная сеть | 469 |
| CIDR| 10.250.0.0/24 |
| IP-адрес прибора Standalone Edge | 10.250.0.111 |
| Прибор прибора STAND NAT IP-адрес | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>Локальная сеть будет растянута

| **Элемент** | **Значение** |
|------------|-----------------|
| Виртуальная локальная сеть | 472 |
| CIDR| 10.250.3.0/24 |

### <a name="private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>Схема Private Cloud IP для маршрутизатора NSX-T Tier0 (L2 VPN serve)

| **Элемент** | **Значение** |
|------------|-----------------|
| Интерфейс Loopback | 192.168.254.254/32 |
| Интерфейс туннеля | 5.5.5.1/29 |
| Логический переключатель (растягивается) | Stretch_LS |
| Интерфейс Loopback (IP-адрес NAT) | 104.40.21.81 |

### <a name="private-cloud-network-to-be-mapped-to-the-stretched-network"></a>Сеть private Cloud будет отображаться с растянутой сетью

| **Элемент** | **Значение** |
|------------|-----------------|
| Виртуальная локальная сеть | 712 |
| CIDR| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>Получить логический идентификатор маршрутизатора, необходимый для L2VPN

Следующие шаги показывают, как получить логический идентификатор tier0 DR логический экземпляр маршрутизатора для служб IPsec и L2VPN. Идентификатор логических маршрутизаторов необходим позже при реализации L2VPN.

1. `https://*nsx-t-manager-ip-address*` Вопийте в NSX-T Manager и выберите**обзор**поставщика **сетевых** > **маршрутизаторов.** > **Provider-LR** >  Для **режима высокой доступности**выберите **Active-Standby.** Это действие открывает всплывающее окно, которое показывает Edge VM, на котором в настоящее время активен маршрутизатор Tier0.

    ![Выберите активный режим ожидания](media/l2vpn-fetch01.png)

2. Выберите**края****узлов** >  **ткани** > . Обратите внимание на IP-адрес управления активного Edge VM (Edge VM1), указанный на предыдущем этапе.

    ![IP-адрес управления заметок](media/l2vpn-fetch02.png)

3. Откройте сеанс SSH на IP-адрес управления Edge VM. Запустите ```get logical-router``` команду с **помощью админа** имени пользователя и пароля **CloudSimple 123!**.

    ![получить логический маршрутизатор выход](media/l2vpn-fetch03.png)

4. Если вы не видите запись 'DR-Provider-LR', выполните следующие шаги.

5. Создайте два логических переключателя с поддержкой наложения. Один логический переключатель растягивается на помещения, где находятся миграционные рабочие нагрузки. Другим логическим переключателем является манекен переключатель. Для получения инструкций в документации VMware можно [ознакомиться](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html) с инструкциями.

    ![Создание логического коммутатора](media/l2vpn-fetch04.png)

6. Прикрепите фиктивный переключатель к маршрутизатору Tier1 со ссылкой на локальный IP-адрес или любую неперекрывающуюся подсеть из локальной или частной облачности. В документации VMware [можно добавить порт Downlink на логическом маршрутизаторе Tier-1.](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html)

    ![Прикрепить манекен переключатель](media/l2vpn-fetch05.png)

7. Повторите `get logical-router` команду снова на сессии SSH Edge VM. Отображается UUID логического маршрутизатора 'DR-Provider-LR). Запишите UUID, который необходим при настройке L2VPN.

    ![получить логический маршрутизатор выход](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>Получить идентификатор логического коммутатора, необходимый для L2VPN

1. Войти на [NSX-T Manager.](https://nsx-t-manager-ip-address)
2. Выберите > **коммутаторы переключения** > **Switches**  >  **сетей** > **< «Логический\>переключатель****Обзор**.
3. Сделайте примечание UUID натяжного логического переключателя, который требуется при настройке L2VPN.

    ![получить логический маршрутизатор выход](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>Соображения реуктора и безопасности для L2VPN

Для создания VPN на основе маршрута IPsec между маршрутизатором NSX-T Tier0 и автономным клиентом NSX Edge интерфейс loopback маршрутизатора NSX-T Tier0 должен быть в состоянии общаться с общедоступным IP-адресом отдельного клиента NSX на территории над UDP 500/4500.

### <a name="allow-udp-5004500-for-ipsec"></a>Разрешить UDP 500/4500 для IPsec

1. [Создайте общедоступный IP-адрес](public-ips.md) для интерфейса loopback NSX-T Tier0 на портале CloudSimple.

2. [Создайте таблицу брандмауэра](firewall.md) с правилами состояния, которые позволяют входящим трафиком UDP 500/4500, и прикрепите таблицу брандмауэра к подсети NSX-T HostTransport.

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>Реклама IP-интерфейса loopback в сети подкладок

1. Создайте нулевой маршрут для сети интерфейсов loopback. Вопийте в NSX-T Manager и выберите**маршрутизаторы** > **Routers** >  **сети** > **Поставщик-LR** > **Маршрутизирующих** > **статические маршруты.** Нажмите кнопку **Добавить**. Для **сети,** введите ip-адрес интерфейса loopback. Для **next Hops**нажмите **«Добавить»,** указать «Null» для следующего перехода и сохранить значение по умолчанию 1 для Расстояния админ.

    ![Добавление статического маршрута](media/l2vpn-routing-security01.png)

2. Создайте список префиксов IP. Вопийте в NSX-T Manager и выберите **списки** >  >  >  > реучинных**маршрутизаторов****Сети- LR** > **маршрутизируя****IP-префиксы.****Routing** Нажмите кнопку **Добавить**. Введите имя для определения списка. Для **префиксов,** нажмите **Добавить** дважды. В первой строке введите '0.0.0.0/0' для **сети** и 'Deny' для **действий**. Во второй строке выберите **Any** для **сети** и **разрешения** на **действия.**
3. Прикрепите список префиксов IP к обоим соседям BGP (TOR). Присоединение списка префиксов IP к соседу BGP предотвращает рекламирование маршрута по умолчанию в BGP к коммутаторам TOR. Тем не менее, любой другой маршрут, который включает в себя нулевой маршрут будет рекламировать Loopback интерфейс IP-адрес для коммутаторов TOR.

    ![Создание списка префиксов IP](media/l2vpn-routing-security02.png)

4. Вопийте в NSX-T Manager и выберите **сетевые** >  >  > **маршрутизаторы** > **Provider-LR****Маршрутизируя** > **BGP** > **Соседи.****Routing** Выберите первого соседа. Нажмите **Edit** > **Адрес семьи**. Для семейства IPv4 отредагите столбец **«Из вне фильтра»** и выберите созданный список префиксов IP. Выберите команду **Сохранить**. Повторите этот шаг для второго соседа.

    ![Прикрепите список](media/l2vpn-routing-security03.png) ![ip префикса 1 Attach IP список префикс 2](media/l2vpn-routing-security04.png)

5. Перераспределить нулевой статический маршрут в BGP. Для рекламы маршрута интерфейса loopback в подкладку необходимо перераспределить нулевой статический маршрут в BGP. Вопийте в NSX-T Manager и выберите **Сетевые** >  >  > **маршрутизаторы****Поставщик-LR** > **Маршрутирование** > **Маршрут перераспределения** > **Соседи**.**Routing** Выберите **провайдер-LR-Route_Redistribution** и нажмите **Edit**. Выберите **статический** флажок и нажмите **Сохранить**.

    ![Перераспределить нулевой статический маршрут в BGP](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>Назначайте маршрутный VPN на маршрутизаторе NSX-T Tier0

Используйте следующий шаблон, чтобы заполнить все детали для настройки vpn на основе маршрута на маршрутизаторе NSX-T Tier0. UUID в каждом вызове POST необходимы в последующих вызовах POST. IP-адреса для интерфейсов Loopback и туннелей для L2VPN должны быть уникальными и не пересекаться с сетей на территории или private Cloud.

IP-адреса, выбранные для loopback и туннельного интерфейса, используемого для L2VPN, должны быть уникальными и не пересекаться с сетей на территории или private Cloud. Сеть интерфейса loopback всегда должна быть /32.

```
Loopback interface ip : 192.168.254.254/32
Tunnel interface subnet : 5.5.5.0/29
Logical-router ID : UUID of Tier0 DR logical router obtained in section "Steps to fetch Logical-Router ID needed for L2VPN"
Logical-switch ID(Stretch) : UUID of Stretch Logical Switch obtained earlier
IPSec Service ID :
IKE profile ID :
DPD profile ID :
Tunnel Profile ID :
Local-endpoint ID :
Peer end-point ID :
IPSec VPN session ID (route-based) :
L2VPN service ID :
L2VPN session ID :
Logical-Port ID :
Peer Code :
```

Для всех следующих вызовов API замените IP-адрес своим IP-адресом менеджера NSX-T. Вы можете запустить все эти вызовы API `curl` от клиента POSTMAN или с помощью команд.

### <a name="enable-the-ipsec-vpn-service-on-the-logical-router"></a>Включить VPN-сервис IPSec на логическом маршрутизаторе

```
POST   https://192.168.110.201/api/v1/vpn/ipsec/services/
{
"resource_type": "IPSecVPNService",
"description": "Manage VPN service",
"display_name": "IPSec VPN service",
"logical_router_id": "Logical-router ID",
"ike_log_level": "INFO",
"enabled": true
}
```

### <a name="create-profiles-ike"></a>Создание профилей: IKE

```
POST https://192.168.110.201/api/v1/vpn/ipsec/ike-profiles
 
{
"resource_type": "IPSecVPNIKEProfile",
"description": "IKEProfile for siteA",
"display_name": "IKEProfile siteA",
"encryption_algorithms": ["AES_128"],
"ike_version": "IKE_V2",
"digest_algorithms": ["SHA2_256"],
"sa_life_time":21600,
"dh_groups": ["GROUP14"]
}
```

### <a name="create-profiles-dpd"></a>Создание профилей: DPD

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/dpd-profiles  

{
"resource_type": "IPSecVPNDPDProfile",
"display_name": "nsx-default-dpd-profile",
"enabled": true
}
```

### <a name="create-profiles-tunnel"></a>Создание профилей: Туннель

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/tunnel-profiles
 
{
"resource_type": "IPSecVPNTunnelProfile",
"display_name": "nsx-default-tunnel-profile",
"enable_perfect_forward_secrecy": true,
"encryption_algorithms": ["AES_GCM_128"],
"digest_algorithms": [],
"sa_life_time":3600,
"dh_groups": ["GROUP14"],
"encapsulation_mode": "TUNNEL_MODE",
"transform_protocol": "ESP",
"df_policy": "COPY"
}
```

### <a name="create-a-local-endpoint"></a>Создание локальной конечной точки

``` 
POST https://192.168.110.201/api/v1/vpn/ipsec/local-endpoints
 
{
"resource_type": "IPSecVPNLocalEndpoint",
"description": "Local endpoint",
"display_name": "Local endpoint",
"local_id": "<Public IP of Loopback interface>",
"ipsec_vpn_service_id": {
"target_id": "IPSec VPN service ID"},
"local_address": "<IP of Loopback interface>",
"trust_ca_ids": [],
"trust_crl_ids": []
}
```

### <a name="create-a-peer-endpoint"></a>Создание одноранговой точки

```
POST https://192.168.110.201/api/v1/vpn/ipsec/peer-endpoints

{
"resource_type": "IPSecVPNPeerEndpoint",
"description": "Peer endpoint for site B",
"display_name": "Peer endpoint for site B",
"connection_initiation_mode": "INITIATOR",
"authentication_mode": "PSK",
"ipsec_tunnel_profile_id": "IPSec Tunnel profile ID",
"dpd_profile_id": "DPD profile ID",
"psk":"nsx",
"ike_profile_id": "IKE profile ID",
"peer_address": "<Public IP of Standalone client",
"peer_id": "<Public IP of Standalone client>"
}
```

### <a name="create-a-route-based-vpn-session"></a>Создание VPN-сессии на основе маршрутов

```
POST :  https://192.168.110.201/api/v1/vpn/ipsec/sessions
 
{
"resource_type": "RouteBasedIPSecVPNSession",
"peer_endpoint_id": "Peer Endpoint ID",
"ipsec_vpn_service_id": "IPSec VPN service ID",
"local_endpoint_id": "Local Endpoint ID",
"enabled": true,
"tunnel_ports": [
{
"ip_subnets": [
{
"ip_addresses": [
 "5.5.5.1"
],
"prefix_length": 24
}
  ]
}
]
}
```

## <a name="configure-l2vpn-on-nsx-t-tier0-router"></a>Настройка L2VPN на маршрутизаторе NSX-T Tier0

Заполните следующую информацию после каждого вызова POST. Иудиции необходимы при последующих вызовах POST.

```
L2VPN Service ID:
L2VPN Session ID:
Logical Port ID:
```

### <a name="create-the-l2vpn-service"></a>Создание сервиса L2VPN

Выход следующей команды GET будет пустым, так как конфигурация еще не завершена.

```
GET : https://192.168.110.201/api/v1/vpn/l2vpn/services
```

Для следующей команды POST логическим идентификатором маршрутизатора является UUID логического маршрутизатора Tier0 DR, полученного ранее.

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>Создание сеанса L2VPN

Для следующей команды POST идентификатор службы L2VPN — это идентификатор, который вы только что получили, а идентификатор сессии IPsec VPN — это идентификатор, полученный в предыдущем разделе.

```    
POST: https://192.168.110.201/api/v1/vpn/l2vpn/sessions

{
"l2vpn_service_id" : "L2VPN service ID",
"transport_tunnels" : [
    {
    "target_id" : "IPSec VPN session ID"
    }]
}
```

Эти вызовы создают конечную точку туннеля GRE. Чтобы проверить состояние, запустите следующую команду.

```
edge-2> get tunnel-port
Tunnel      : 44648dae-8566-5bc9-a065-b1c4e5c3e03f
IFUID       : 328
LOCAL       : 169.254.64.1
REMOTE      : 169.254.64.2
ENCAP       : GRE

Tunnel      : cf950ca1-5cf8-5438-9b1a-d2c8c8e7229b
IFUID       : 318
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.152
ENCAP       : GENEVE

Tunnel      : 63639321-87c5-529e-8a61-92c1939799b2
IFUID       : 304
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.156
ENCAP       : GENEVE
```

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>Создание логического порта с указанным идентификатором туннеля

```
    POST https://192.168.110.201/api/v1/logical-ports/

{
"resource_type": "LogicalPort",
"display_name": "Extend logicalSwitch, port for service",
"logical_switch_id": "Logical switch ID",
"admin_state" : "UP",
"attachment": {
"attachment_type":"L2VPN_SESSION",
"id":"L2VPN session ID",
"context" : {
"resource_type" : "L2VpnAttachmentContext",
    "tunnel_id" : 10
}
    }
        }
```

## <a name="obtain-the-peer-code-for-l2vpn-on-the-nsx-t-side"></a>Получение однорангового кода для L2VPN на стороне NSX-T

Получите одноранговой код конечной точки NSX-T. При настройке удаленной конечной точки требуется одноранговый код. L2VPN <> сеанс-id можно получить из предыдущего раздела. Для получения дополнительной [информации](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html)см.

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>Развертывание отдельного клиента NSX-T (в непредполном отклесли)

Перед развертыванием убедитесь, что правила брандмауэра позволяют входящим и исходящим udP 500/4500 трафикса с/на общедоступный IP-адрес CloudSimple, который был зарезервирован ранее для интерфейса цикла маршрутизатора NSX-T T0. 

1. [Скачать автономный клиент NSX Edge](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230) OVF и извлекайте файлы из загруженного пакета в папку.

    ![Скачать автономный клиент NSX Edge](media/l2vpn-deploy-client01.png)

2. Перейти к папке со всеми извлеченными файлами. Выберите все vmdks (NSX-l2t-client-large.mf и NSX-l2t-client-large.ovf для большого размера прибора или NSX-l2t-client-Xlarge.mf и NSX-l2t-client-Xlarge.ovf для дополнительного размера прибора большого размера). Щелкните **Далее**.

    ![Выберите](media/l2vpn-deploy-client02.png) ![шаблон шаблона Выберите](media/l2vpn-deploy-client03.png)

3. Введите имя отдельного клиента NSX-T и нажмите **кнопку Next.**

    ![Введите имя шаблона](media/l2vpn-deploy-client04.png)

4. Нажмите **Далее** по мере необходимости для достижения настроек хранилища данных. Выберите подходящий магазин данных для отдельного клиента NSX-T и нажмите **кнопку Next.**

    ![Выберите хранилище данных](media/l2vpn-deploy-client06.png)

5. Выберите правильные группы портов для Trunk (Trunk PG), Public (Uplink PG) и интерфейс HA (Uplink PG) для отдельного клиента NSX-T. Щелкните **Далее**.

    ![Выберите группы портов](media/l2vpn-deploy-client07.png)

6. Заполните следующие сведения на экране **шаблона Customize** и нажмите **далее:**

    Расширить L2T:

    * **Адрес одноранговой раны**. Введите IP-адрес, зарезервированный на портале Azure CloudSimple для интерфейса NSX-T Tier0 Loopback.
    * **Одноранговый код**. Вставьте одноранговой код, полученный на последнем этапе развертывания L2VPN Server.
    * **Подинтерфейсы VLAN (ИДент туннеля)**. Введите идентификатор VLAN, чтобы быть растянутым. В скобках () введите идентификатор туннеля, который был предварительно настроен.

    Расширить интерфейс Uplink:

    * **DNS IP-адрес**. Введите предварительный IP-адрес DNS.
    * **Шлюз по умолчанию**.  Введите шлюз по умолчанию VLAN, который будет выступать в качестве шлюза по умолчанию для этого клиента.
    * **IP-адрес**. Введите IP-адрес автономного клиента.
    * **Длина префикса**. Введите длину префикса в uplink VLAN/subnet.
    * **CLI админ / включить / корень пароль пользователя**. Установите пароль для учетной записи admin/enable/root.

      ![Настройка](media/l2vpn-deploy-client08.png)
      ![шаблона Настройка шаблона - больше](media/l2vpn-deploy-client09.png)

7. Просмотрите настройки и нажмите **Кнопка Готово**.

    ![Полная конфигурация](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>Настройка порта раковины

Если на одном из VPN-сайтов нет развернутых NSX, можно настроить L2 VPN, развернув автономный NSX Edge на этом сайте. Автономный NSX Edge развертывается с помощью файла OVF на хосте, который не управляется NSX. Это развертывает nX Edge Services Gateway для работы в качестве клиента L2 VPN.

Если автономный багажник vNIC подключен к vSphere Distributed Switch, для функции L2 VPN требуется либо беспорядочный режим, либо порт раковины. Использование беспорядочного режима может привести к дубликату пингов и дублировать ответы. По этой причине используйте режим порчи раковины в автономной конфигурации L2 VPN NSX Edge. В документации VMware [можно ознакомиться с портом раковины.](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html)

## <a name="ipsec-vpn-and-l2vpn-verification"></a>Проверка IPsec VPN и L2VPN

Используйте следующие команды для проверки сеансов IPsec и L2VPN с автономных сеансов NSX-T Edge.

```
nsx-l2t-edge> show service ipsec
-----------------------------------------------------------------------
vShield Edge IPSec Service Status:
IPSec Server is running.
AESNI is enabled.
Total Sites: 1, 1 UP, 0 Down
Total Tunnels: 1, 1 UP, 0 Down
----------------------------------
Site:  10.250.0.111_0.0.0.0/0-104.40.21.81_0.0.0.0/0
Channel: PeerIp: 104.40.21.81    LocalIP: 10.250.0.111  Version: IKEv2  Status: UP
Tunnel: PeerSubnet: 0.0.0.0/0    LocalSubnet: 0.0.0.0/0   Status: UP
----------------------------------
```

```
nsx-l2t-edge> show service l2vpn
L2 VPN is running
----------------------------------------
L2 VPN type: Client/Spoke

SITENAME                       IPSECSTATUS          VTI                  GRE
1ecb00fb-a538-4740-b788-c9049e8cb6c6 UP                   vti-100              l2t-1
```

Используйте следующие команды для проверки сеансов IPsec и L2VPN с маршрутизатора NSX-T Tier0.

```
edge-2> get ipsecvpn session
Total Number of Sessions: 1

IKE Session ID : 3
UUID           : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Type           : Route

Local IP       : 192.168.254.254      Peer IP        : 192.227.85.167
Local ID       : 104.40.21.81         Peer ID        : 192.227.85.167
Session Status : Up

Policy Rules
    VTI UUID       : 4bf96e3b-e50b-49cc-a16e-43a6390e3d53
    ToRule ID      : 560874406            FromRule ID    : 2708358054
    Local Subnet   : 0.0.0.0/0            Peer Subnet    : 0.0.0.0/0
    Tunnel Status  : Up
```

```
edge-2> get l2vpn session
Session       : f7147137-5dd0-47fe-9e53-fdc2b687b160
Tunnel        : b026095b-98c8-5932-96ff-dda922ffe316
IPSEC Session : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Status        : UP
```

Используйте следующие команды для проверки порта раковины на хостеле ESXi, где автономный клиент NSX-T VM находится в предварительной среде.

```
 [root@esxi02:~] esxcfg-vswitch -l |grep NSX
  53                  1           NSXT-client-large.eth2
  225                1           NSXT-client-large.eth1
  52                  1           NSXT-client-large.eth0
```

```
[root@esxi02:~] net-dvs -l | grep "port\ [0-9]\|SINK\|com.vmware.common.alias"
                com.vmware.common.alias = csmlab2DS ,   propType = CONFIG
        port 24:
        port 25:
        port 26:
        port 27:
        port 13:
        port 19:
        port 169:
        port 54:
        port 110:
        port 6:
        port 107:
        port 4:
        port 199:
        port 168:
        port 201:
        port 0:
        port 49:
        port 53:
        port 225:
                com.vmware.etherswitch.port.extraEthFRP =   SINK
        port 52:
```
