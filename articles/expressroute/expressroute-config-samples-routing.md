---
title: 'Azure ExpressRoute: образцы конфигурации маршрутизатора'
description: Эта страница содержит примеры конфигурации для маршрутизаторов серий Cisco ASA и Juniper MX.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: osamaz
ms.openlocfilehash: 3603bc45b920dc62eb8bf6f2eb8557f98e21638e
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024818"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Примеры конфигурации маршрутизатора для настройки и управления маршрутизацией
Эта страница предоставляет образцы конфигурации интерфейса и маршрутизации для маршрутизаторов серии Cisco IOS-XE и Juniper MX при работе с Azure ExpressRoute.

> [!IMPORTANT]
> Образцы на этой странице являются чисто для руководства. Вы должны работать с отделом продаж/технической команды поставщика и вашей сетевой командой, чтобы найти соответствующие конфигурации для удовлетворения ваших потребностей. Корпорация Майкрософт не будет поддерживать проблемы, связанные с конфигурациями, перечисленными на этой странице. Обратитесь к поставщику устройств для получения проблем с поддержкой.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Параметры MTU и TCP MSS в интерфейсах маршрутизатора
Максимальная трансмиссия (MTU) для интерфейса ExpressRoute составляет 1500, что является типичным MTU по умолчанию для интерфейса Ethernet на маршрутизаторе. Если маршрутизатор не имеет другого значения MTU по умолчанию, то и значение в интерфейсе маршрутизатора можно не указывать.

В отличие от VPN шлюза Azure, максимальный размер сегмента TCP (MSS) для схемы ExpressRoute не требуется указывать.

Образцы конфигурации маршрутизатора в этой статье применяются ко всем пирингам. Для получения дополнительных сведений о маршрутизации обратитесь к статьям [Сеансы пиринга ExpressRoute](expressroute-circuit-peerings.md) и [Требования ExpressRoute к маршрутизации](expressroute-routing.md).


## <a name="cisco-ios-xe-based-routers"></a>Маршрутизаторы на основе Cisco IOS-XE
Образцы в этом разделе применяются к любому маршрутизатору, работая с семейством IOS-XE OS.

### <a name="configure-interfaces-and-subinterfaces"></a>Настройка интерфейсов и субинтерфейсов
Вам понадобится один субинтерфейс на одно вглядываясь в каждый маршрутизатор, который вы подключаете к Microsoft. Субинтерфейс можно идентифицировать с помощью ИДента VLAN или сложенной пары идентификаторов VLAN и IP-адреса.

**Определение интерфейса Dot1Q**

Этот пример содержит определение субинтерфейса для субинтерфейса с одним идентификатором VLAN. Идентификатор виртуальной локальной сети уникален в пределах одного пиринга. Последний октет адреса IPv4 всегда будет нечетным числом.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**Определение интерфейса QinQ**

Этот пример содержит определение субинтерфейса для субинтерфейса с двумя иди что-то vLAN. Внешний Идентифик VLAN (s-tag), если он используется, остается неизменным во всех пирингах. Идентификатор внешней виртуальной локальной сети (c-тег) уникален для каждого пиринга. Последний октет адреса IPv4 всегда будет нечетным числом.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="set-up-ebgp-sessions"></a>Настройка сеансов eBGP
Для каждого внимательного вглядывания необходимо настроить сеанс BGP с корпорацией Майкрософт. Навлажайте сеанс BGP, используя следующий пример. Если адрес IPv4, который вы использовали для вашего субинтерфейса, был a.b.c.d, то IP-адрес соседа BGP (Microsoft) будет a.b.c.d'1. Последний октет IPv4-адреса соседа BGP всегда будет четным числом.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Настройка префиксов, которые будут рекламироваться во время сессии BGP
Назначьте маршрутизатор для рекламы выбранных префиксов корпорации Майкрософт с помощью следующего образца.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="route-maps"></a>Карты маршрутизации
Используйте маршрутные карты и списки префиксов для фильтрации префиксов, распространяемых в вашей сети. Ознакомьтесь со следующим примером и убедитесь, что у вас есть соответствующие списки префиксов.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !

### <a name="configure-bfd"></a>Настройка BFD

Вы будете настраивать BFD в двух местах: один на уровне интерфейса и другой на уровне BGP. Примером здесь является интерфейс «Зин». 

    interface GigabitEthernet<Interface_Number>.<Number>
     bfd interval 300 min_rx 300 multiplier 3
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>
    
    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> fall-over bfd
     exit-address-family
    !


## <a name="juniper-mx-series-routers"></a>Маршрутизаторы серии Juniper MX
Образцы в этом разделе применяются к любому маршрутизатору серии Juniper MX.

### <a name="configure-interfaces-and-subinterfaces"></a>Настройка интерфейсов и субинтерфейсов

**Определение интерфейса Dot1Q**

Этот пример содержит определение субинтерфейса для субинтерфейса с одним идентификатором VLAN. Идентификатор виртуальной локальной сети уникален в пределах одного пиринга. Последний октет адреса IPv4 всегда будет нечетным числом.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


**Определение интерфейса QinQ**

Этот пример содержит определение субинтерфейса для субинтерфейса с двумя иди что-то vLAN. Внешний Идентифик VLAN (s-tag), если он используется, остается неизменным во всех пирингах. Идентификатор внешней виртуальной локальной сети (c-тег) уникален для каждого пиринга. Последний октет адреса IPv4 всегда будет нечетным числом.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="set-up-ebgp-sessions"></a>Настройка сеансов eBGP
Для каждого внимательного вглядывания необходимо настроить сеанс BGP с корпорацией Майкрософт. Навлажайте сеанс BGP, используя следующий пример. Если адрес IPv4, который вы использовали для вашего субинтерфейса, был a.b.c.d, то IP-адрес соседа BGP (Microsoft) будет a.b.c.d'1. Последний октет IPv4-адреса соседа BGP всегда будет четным числом.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Настройка префиксов, которые будут рекламироваться во время сессии BGP
Назначьте маршрутизатор для рекламы выбранных префиксов корпорации Майкрософт с помощью следующего образца.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter 
    <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="route-policies"></a>Политика маршрута
Карты маршрутов и списки префиксов можно использовать для фильтрации префиксов, распространяемых в вашей сети. Ознакомьтесь со следующим примером и убедитесь, что у вас есть соответствующие списки префиксов.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
                prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="configure-bfd"></a>Настройка BFD
Налажить BFD только по разделу протокола BGP.

    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
                bfd-liveness-detection {
                       minimum-interval 3000;
                       multiplier 3;
                }
            }                               
        }                                   
    }


## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения см. в разделе [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).



