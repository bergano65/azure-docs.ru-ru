---
title: Решение VMware для Azure, Клаудсимпле — Настройка DNS и DHCP для частного облака
description: Описание настройки DNS и DHCP для приложений и рабочих нагрузок, работающих в среде частного облака Клаудсимпле.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ef1266b783034cf18dc2b3ea4be5ebc01bc12c70
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77024693"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>Настройка приложений и рабочих нагрузок DNS и DHCP в частном облаке Клаудсимпле

Приложениям и рабочим нагрузкам, выполняемым в среде частного облака, требуется разрешение имен и службы DHCP для назначения адресов и поиска по IP-адресам.  Для предоставления этих служб требуется определенная инфраструктура DHCP и DNS.  Вы можете настроить виртуальную машину для предоставления этих служб в среде частного облака.  

## <a name="prerequisites"></a>Предварительные условия

* Группа распределенных портов с настроенной виртуальной ЛС
* Настройка маршрута к локальным или DNS-серверам в Интернете
* Шаблон виртуальной машины или ISO-образ для создания виртуальной машины

## <a name="linux-based-dns-server-setup"></a>Установка DNS-сервера на основе Linux

Linux предлагает различные пакеты для настройки DNS-серверов.  Ниже приведен [Пример настройки из дигиталоцеан](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) с инструкциями по настройке DNS-сервера BIND с открытым исходным кодом.

## <a name="windows-based-setup"></a>Установка на основе Windows

В этих разделах Майкрософт описывается настройка сервера Windows Server в качестве DNS-сервера и в качестве DHCP-сервера.

* [Windows Server как DNS-сервер](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [Windows Server как DHCP-сервер](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
