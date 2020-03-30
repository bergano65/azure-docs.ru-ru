---
title: Интернет пиринг - часто задаваемые вопросы
titleSuffix: Azure
description: Интернет пиринг - часто задаваемые вопросы
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775502"
---
# <a name="internet-peering---faqs"></a>Интернет пиринг - часто задаваемые вопросы

Вы можете просмотреть информацию ниже для общих вопросов.

**В чем разница между Интернет-пирингом и пиринговым сервисом?**

Peering Service — это служба, которая намерена предоставлять корпоративным IP-соединениям для корпоративных клиентов общедоступные IP-соединения. Интернет корпоративного класса включает подключение через провайдеров, которые имеют высокую пропускную связь с корпорацией Майкрософт и избыточность для подключения HA. Кроме того, пользовательский трафик оптимизирован для задержки до ближайшего Microsoft Edge. Peering Service основывается на пиринговой связи с оператором-партнером. Вглядываясь подключения с партнером должны быть прямые пиринг, а не Exchange пиринг. Прямое пиринг должно иметь локальную и геоизбыточность.

**Что такое наследие пиринг?**

Вглядывающее соединение, настроенное с помощью Azure PowerShell, управляется как ресурс Azure. Вглядывающиеся соединения, созданные в прошлом, хранятся в нашей системе как устаревшие пиринги, которые можно преобразовать для управления в качестве ресурса Azure.

**При вызове New-AzPeeringDirectConnectionObject какие IP-адреса даются устройствам Microsoft и Peer?**

При вызове New-AzPeeringDirectConnectionObject cmdlet вводится адрес /31 (a.b.c.d/31) или адрес /30 (a.b.c.d/30). Первый IP-адрес (a.b.c.d'0) отдается устройству Peer, а второй IP-адрес (a.b.c.d'1) — устройству Майкрософт.

**Что такое параметры MaxPrefixesAdvertisedIPv4 и MaxPrefixesAdvertisedIPv6 в New-AzPeeringDirectConnectionObject cmdlet?**

Параметры MaxPrefixesAdvertisedIPv4 и MaxPrefixesAdvertisedIPv6 представляют собой максимальное количество префиксов IPv4 и IPv6, которые peer хочет принять. Эти параметры могут быть изменены в любое время.