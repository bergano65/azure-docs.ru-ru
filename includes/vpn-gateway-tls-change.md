---
title: Включить имя файла
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2ed141847f923a847443d2293e850519357cdae2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "67185178"
---
Начиная с 1 июля 2018 года прекращается поддержка TLS 1.0 и TLS 1.1 в VPN-шлюзе Azure. VPN-шлюз будет поддерживать только TLS 1.2. Затрагиваются только подключения "точка — сеть". Подключения "сеть — сеть" не затрагиваются. Если вы используете TLS для VPN-подключений "точка — сеть" на клиентах Windows 10, не нужно предпринимать никаких действий. Если вы используете TLS для подключений "точка — сеть" клиентов Windows 7 и Windows 8, обратитесь к разделу [Собственная аутентификация Azure с использованием сертификата для подключений типа "точка — сеть"](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S) за инструкциями по обновлению.