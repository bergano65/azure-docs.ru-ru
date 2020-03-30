---
title: Решение Azure VMware от CloudSimple - Настройка рабочей нагрузки DNS и DHCP для приватного облака
description: Описывает, как настроить DNS и DHCP для приложений и рабочих нагрузок, работающих в среде CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ef1266b783034cf18dc2b3ea4be5ebc01bc12c70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024693"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>Настройка dNS и DHCP-приложений и рабочих нагрузок в облачном частном облаке

Приложения и рабочие нагрузки, работающие в среде Private Cloud, требуют разрешения имен и dhCP-сервисов для поиска и назначения IP-адресов.  Для предоставления этих услуг требуется надлежащая инфраструктура DHCP и DNS.  Вы можете настроить виртуальную машину для предоставления этих услуг в среде Private Cloud.  

## <a name="prerequisites"></a>Предварительные требования

* Распределенная группа портов с vLAN настроена
* Настройка маршрута на предприимские или интернет-dNS-серверы
* Виртуальный шаблон машины или ISO для создания виртуальной машины

## <a name="linux-based-dns-server-setup"></a>Установка DNS-сервера на основе Linux

Linux предлагает различные пакеты для настройки DNS-серверов.  Вот [пример настройки от DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) с инструкциями для создания сервера BIND DNS с открытым исходным кодом.

## <a name="windows-based-setup"></a>Настройка на базе Windows

Эти темы Microsoft описывают, как настроить сервер Windows в качестве DNS-сервера и как сервер DHCP.

* [Windows Server как DNS-сервер](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [Windows Server как DHCP Server](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
