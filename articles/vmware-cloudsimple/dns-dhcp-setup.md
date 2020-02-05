---
title: Решения Azure VMware (AVS). Настройка службы DNS и DHCP для частного облака AVS
description: Описание настройки DNS и DHCP для приложений и рабочих нагрузок, работающих в среде частного облака AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2a5cae868f2d8f3689f05dd9d466715ab2008a3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024693"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-avs-private-cloud"></a>Настройка приложений и рабочих нагрузок DNS и DHCP в частном облаке AVS

Приложениям и рабочим нагрузкам, выполняемым в среде частного облака AVS, требуется разрешение имен и службы DHCP для назначения адресов подстановки и IP-адреса. Для предоставления этих служб требуется правильная инфраструктура DHCP и DNS. Вы можете настроить виртуальную машину для предоставления этих служб в среде частного облака AVS. 

## <a name="prerequisites"></a>Технические условия

* Группа распределенных портов с настроенной виртуальной ЛС
* Настройка маршрута к локальным или DNS-серверам в Интернете
* Шаблон виртуальной машины или ISO-образ для создания виртуальной машины

## <a name="linux-based-dns-server-setup"></a>Установка DNS-сервера на основе Linux

Linux предлагает различные пакеты для настройки DNS-серверов. Ниже приведен [Пример настройки из дигиталоцеан](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) с инструкциями по настройке DNS-сервера BIND с открытым исходным кодом.

## <a name="windows-based-setup"></a>Установка на основе Windows

В этих разделах Майкрософт описывается настройка сервера Windows Server в качестве DNS-сервера и в качестве DHCP-сервера.

* [Windows Server как DNS-сервер](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [Windows Server как DHCP-сервер](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
