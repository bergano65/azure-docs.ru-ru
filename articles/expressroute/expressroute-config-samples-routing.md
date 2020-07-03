---
title: 'Azure ExpressRoute: примеры конфигурации маршрутизатора'
description: Эта страница содержит примеры конфигурации для маршрутизаторов серий Cisco ASA и Juniper MX.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: osamaz
ms.openlocfilehash: 3603bc45b920dc62eb8bf6f2eb8557f98e21638e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024818"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Примеры конфигурации маршрутизатора для настройки и управления маршрутизацией
На этой странице представлены примеры конфигурации интерфейса и маршрутизации для маршрутизаторов серии "Cisco IOS-XE" и "Juniper MX" при работе с Azure ExpressRoute.

> [!IMPORTANT]
> Примеры на этой странице предназначены исключительно для руководства. Для поиска подходящих конфигураций в соответствии с вашими потребностями необходимо обратиться к группе "продажи и техническая группа поставщика" и группе разработчиков сети. Корпорация Майкрософт не будет поддерживать проблемы, связанные с конфигурациями, перечисленными на этой странице. Обратитесь к поставщику устройства за проблемами в поддержке.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Параметры MTU и TCP MSS в интерфейсах маршрутизатора
Максимальный размер передаваемых данных (MTU) для интерфейса ExpressRoute — 1500, то есть типичный MTU по умолчанию для интерфейса Ethernet на маршрутизаторе. Если маршрутизатор не имеет другого значения MTU по умолчанию, то и значение в интерфейсе маршрутизатора можно не указывать.

В отличие от VPN-шлюза Azure, не нужно указывать максимальный размер сегмента (MSS) TCP для канала ExpressRoute.

Примеры конфигурации маршрутизатора, приведенные в этой статье, относятся ко всем одноранговым узлам. Для получения дополнительных сведений о маршрутизации обратитесь к статьям [Сеансы пиринга ExpressRoute](expressroute-circuit-peerings.md) и [Требования ExpressRoute к маршрутизации](expressroute-routing.md).


## <a name="cisco-ios-xe-based-routers"></a>Маршрутизаторы на основе Cisco IOS-XE
Примеры в этом разделе относятся к любому маршрутизатору с семейством ОС IOS-XE.

### <a name="configure-interfaces-and-subinterfaces"></a>Настройка интерфейсов и подинтерфейсов
Вам потребуется один подинтерфейс для каждого пиринга на каждом маршрутизаторе, подключенном к корпорации Майкрософт. Для подинтерфейса можно определить идентификатор виртуальной ЛС или пару с накоплением идентификаторов виртуальных ЛС и IP-адрес.

**Определение интерфейса Dot1Q**

Этот образец предоставляет определение подинтерфейса для подинтерфейса с одним ИДЕНТИФИКАТОРом виртуальной ЛС. Идентификатор виртуальной локальной сети уникален в пределах одного пиринга. Последний октет адреса IPv4 всегда будет нечетным числом.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**Определение интерфейса QinQ**

Этот образец предоставляет определение подинтерфейса для подинтерфейса с двумя идентификаторами виртуальной ЛС. Внешний идентификатор виртуальной ЛС (s-Tag), если он используется, остается одинаковым для всех узлов. Идентификатор внешней виртуальной локальной сети (c-тег) уникален для каждого пиринга. Последний октет адреса IPv4 всегда будет нечетным числом.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="set-up-ebgp-sessions"></a>Настройка сеансов eBGP
Для каждого пиринга необходимо настроить сеанс BGP с корпорацией Майкрософт. Настройте сеанс BGP, используя следующий пример. Если адрес IPv4, используемый для подинтерфейса, был a. b. c. d, IP-адрес соседа BGP (Майкрософт) будет иметь значение a. b. c. d + 1. Последний октет IPv4-адреса соседа BGP всегда будет четным числом.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Настройка префиксов для объявления в сеансе BGP
Настройте маршрутизатор для объявления выбранных префиксов в корпорацию Майкрософт, используя следующий пример.

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
Используйте карты маршрутов и списки префиксов для фильтрации префиксов, распространяемых в сеть. См. Следующий пример и убедитесь, что настроены соответствующие списки префиксов.

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

### <a name="configure-bfd"></a>Настройка БФД

Вы настроите БФД в двух местах: один на уровне интерфейса, а другой — на уровне BGP. Ниже приведен пример для интерфейса QinQ. 

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
Примеры в этом разделе относятся к любому маршрутизатору серии MX Juniper.

### <a name="configure-interfaces-and-subinterfaces"></a>Настройка интерфейсов и подинтерфейсов

**Определение интерфейса Dot1Q**

Этот образец предоставляет определение подинтерфейса для подинтерфейса с одним ИДЕНТИФИКАТОРом виртуальной ЛС. Идентификатор виртуальной локальной сети уникален в пределах одного пиринга. Последний октет адреса IPv4 всегда будет нечетным числом.

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

Этот образец предоставляет определение подинтерфейса для подинтерфейса с двумя идентификаторами виртуальной ЛС. Внешний идентификатор виртуальной ЛС (s-Tag), если он используется, остается одинаковым для всех узлов. Идентификатор внешней виртуальной локальной сети (c-тег) уникален для каждого пиринга. Последний октет адреса IPv4 всегда будет нечетным числом.

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
Для каждого пиринга необходимо настроить сеанс BGP с корпорацией Майкрософт. Настройте сеанс BGP, используя следующий пример. Если адрес IPv4, используемый для подинтерфейса, был a. b. c. d, IP-адрес соседа BGP (Майкрософт) будет иметь значение a. b. c. d + 1. Последний октет IPv4-адреса соседа BGP всегда будет четным числом.

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

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Настройка префиксов для объявления в сеансе BGP
Настройте маршрутизатор для объявления выбранных префиксов в корпорацию Майкрософт, используя следующий пример.

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


### <a name="route-policies"></a>Политики маршрутизации
Для фильтрации префиксов, распространяемых в сеть, можно использовать карты маршрутов и списки префиксов. Ознакомьтесь со следующим примером и убедитесь, что настроены соответствующие списки префиксов.

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

### <a name="configure-bfd"></a>Настройка БФД
Настройте БФД в разделе протокол BGP.

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


## <a name="next-steps"></a>Дальнейшие шаги
Дополнительные сведения см. в разделе [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).



