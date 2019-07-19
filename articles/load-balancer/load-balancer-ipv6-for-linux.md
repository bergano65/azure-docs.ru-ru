---
title: Настройка DHCPv6 для виртуальных машин Linux
titlesuffix: Azure Load Balancer
description: Узнайте, как настроить DHCPv6 для виртуальных машин Linux.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, Azure Load Balancer, двойной стек, общедоступный IP-адрес, встроенная поддержка Ipv6, мобильное устройство, Интернет вещей
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: allensu
ms.openlocfilehash: 3e987b6718ead6b7014ec302d1a186dabef11126
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274915"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Настройка DHCPv6 для виртуальных машин Linux


В некоторых образах виртуальных машин Linux в Azure Marketplace протокол DHCPv6 не настроен по умолчанию. Для поддержки IPv6 в используемом дистрибутиве ОС Linux необходимо настроить DHCPv6. Различные дистрибутивы Linux настраивают DHCPv6 различными способами, потому что они используют разные пакеты.

> [!NOTE]
> В недавно выпущенных образах SUSE Linux и CoreOS в Azure Marketplace протокол DHCPv6 предварительно настроен. При использовании этих образов дополнительные изменения не требуются.

В этом документе описывается, как включить DHCPv6, чтобы виртуальная машина Linux получила IPv6-адрес.

> [!WARNING]
> Неправильное изменение файлов конфигурации сети может привести к тому, что виртуальная машина утратит доступ к сети. Рекомендуется сначала протестировать изменения конфигурации на нерабочих системах. Приведенные в этой статье инструкции были протестированы на последних версиях образов Linux в Azure Marketplace. Подробные инструкции см. в документации к своей версии Linux.

## <a name="ubuntu"></a>Ubuntu

1. Измените файл */etc/dhcp/dhclient6.conf* и добавьте следующую строку:

        timeout 10;

2. Замените конфигурацию сети для интерфейса eth0 следующей конфигурацией:

   * В **Ubuntu 12.04 и 14.04** измените файл */etc/network/interfaces.d/eth0.cfg*. 
   * В **Ubuntu 16.04** измените файл */etc/network/interfaces.d/50-cloud-init.cfg*.

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Обновите IPv6-адрес:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```
Начиная с Ubuntu 17,10, механизмом сетевой конфигурации по умолчанию является [нетплан]( https://netplan.io).  При установке или создании экземпляра НЕТПЛАН считывает конфигурацию сети из файлов конфигурации YAML в этом расположении:/{Либ, т. е. Run}/нетплан/*. YAML.

Для каждого интерфейса Ethernet в конфигурации необходимо включить инструкцию *dhcp6: true* .  Пример:
  
        network:
          version: 2
          ethernets:
            eno1:
              dhcp6: true

Во время раннего запуска нетплан "модуль подготовки отчетов" записывает конфигурацию в/Run для передачи управления устройствами в указанную сетевую управляющую программу для справочных сведений об НЕТПЛАН https://netplan.io/reference, см. в разделе.
 
## <a name="debian"></a>Debian

1. Измените файл */etc/dhcp/dhclient6.conf* и добавьте следующую строку:

        timeout 10;

2. Измените файл */etc/network/interfaces*, добавив следующую конфигурацию:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Обновите IPv6-адрес:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS и Oracle Linux

1. Измените файл */etc/sysconfig/network*, добавив следующий параметр:

        NETWORKING_IPV6=yes

2. Измените файл */etc/sysconfig/network-scripts/ifcfg-eth0*, добавив следующие два параметра:

        IPV6INIT=yes
        DHCPV6C=yes

3. Обновите IPv6-адрес:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 и openSUSE 13

В недавно выпущенных образах SUSE Linux Enterprise Server (SLES) и openSUSE в Azure протокол DHCPv6 предварительно настроен. При использовании этих образов дополнительные изменения не требуются. Если у вас есть виртуальная машина, использующая более старый или пользовательский образ SUSE, то сделайте следующее.

1. При необходимости установите пакет `dhcp-client` :

    ```bash
    sudo zypper install dhcp-client
    ```

2. Измените файл */etc/sysconfig/network/ifcfg-eth0*, добавив следующий параметр:

        DHCLIENT6_MODE='managed'

3. Обновите IPv6-адрес:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 и openSUSE Leap

В недавно выпущенных образах SLES и openSUSE в Azure протокол DHCPv6 предварительно настроен. При использовании этих образов дополнительные изменения не требуются. Если у вас есть виртуальная машина, использующая более старый или пользовательский образ SUSE, то сделайте следующее.

1. Измените файл */etc/sysconfig/network/ifcfg-eth0* и замените параметр `#BOOTPROTO='dhcp4'` следующим значением:

        BOOTPROTO='dhcp'

2. В файл */etc/sysconfig/network/ifcfg-eth0* добавьте следующий параметр:

        DHCLIENT6_MODE='managed'

3. Обновите IPv6-адрес:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

В недавно выпущенных образах CoreOS в Azure протокол DHCPv6 предварительно настроен. При использовании этих образов дополнительные изменения не требуются. Если у вас есть виртуальная машина, использующая более старый или пользовательский образ CoreOS, сделайте следующее.

1. Измените файл */etc/systemd/network/10_dhcp.network*:

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Обновите IPv6-адрес:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
