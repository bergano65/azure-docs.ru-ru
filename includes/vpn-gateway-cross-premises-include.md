---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/16/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 14793d7f787780bfc8604e4af11eb05f1ff0d937
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170614"
---
|  | **Точка-сеть** | **Сеть-сеть** | **ExpressRoute** |
| --- | --- | --- | --- |
| **Поддерживаемые службы Azure** |Облачные службы и виртуальные машины |Облачные службы и виртуальные машины |[Список служб](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **Типичные пропускные способности** |В зависимости от SKU шлюза |Обычно < 1 Гбит/с (совокупная) |50 Мбит/с, 100 Мбит/с, 200 Мбит/с, 500 Мбит/с, 1 Гбит/с, 2 Гбит/с, 5 Гбит/с, 10 Гбит/с |
| **Поддерживаемые протоколы** |SSTP (Secure Sockets Tunneling Protocol) и IPsec |IPsec |Прямое подключение через виртуальные сети, технологии виртуальных частных сетей NSP (MPLS, VPLS) |
| **Маршрутизация** |На основе маршрутов (динамическая) |Мы поддерживаем маршрутизацию на основе политик (PolicyBased, статическая маршрутизация) и маршрутов (RouteBased, VPN с динамической маршрутизацией). |BGP |
| **Устойчивость подключения** |Активное-пассивное |"Активный — пассивный" или "активный —активный" |Активное-активное |
| **Типичный случай использования** |Создание прототипов, сценарии разработки, тестирования и лабораторного использования для облачных служб и виртуальных машин |Сценарии разработки, тестирования и лабораторного использования, а также маломасштабные рабочие нагрузки для облачных служб и виртуальных машин |Доступ ко всем службам Azure (проверенный список), критически важные рабочие нагрузки и рабочие нагрузки корпоративного уровня, архивация, большие данные, Azure в качестве сайта аварийного восстановления |
| **СОГЛАШЕНИЕ ОБ УРОВНЕ ОБСЛУЖИВАНИЯ** |[Соглашение об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/) |[Соглашение об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/) |[Соглашение об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/) |
| **Цены** |[Цены](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Цены](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Цены](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Техническая документация** |[Документация по VPN-шлюзам](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Документация по VPN-шлюзам](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Документация по ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) |
| **ЧАСТО ЗАДАВАЕМЫЕ ВОПРОСЫ** |[VPN-шлюз: вопросы и ответы](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[VPN-шлюз: вопросы и ответы](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Часто задаваемые вопросы об ExpressRoute](../articles/expressroute/expressroute-faqs.md) |
