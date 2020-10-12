---
title: включить файл
description: включить файл
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 01796985a05f88b59786be7bbe0b06907cf0bc25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88854119"
---
| Ресурс                                | Ограничение        |
|-----------------------------------------|------------------------------|
| Префиксы адресов виртуальной сети                   | 600 на VPN-шлюз          |
| Совокупные маршруты BGP                    | 4 000 на VPN-шлюз        |
| Префиксы адресов шлюза локальной сети  | 1000 на шлюз локальной сети               |
| Подключения типа "сеть — сеть"                         | [Зависит от SKU шлюза](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku)|
| Подключения типа "точка — сеть"                         | [Зависит от SKU шлюза](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku) |
| Ограничение маршрута P2S — IKEv2                 | 256 для Windows, отличных от Windows **/** 25           |
| Ограничение маршрута P2S — Опенвпн               | 1000                         |
| Макс. flows                              | 100 КБ для VpnGw1/AZ  **/**  512 КБ для VpnGw2-4/AZ|