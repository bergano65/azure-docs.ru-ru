---
title: Отладка заголовков HTTP для Azure CDN от Майкрософт | Документация Майкрософт
description: Заголовки запросов отладочного кэша содержат дополнительные сведения о политике кэша, примененной к запрошенному ресурсу. Эти заголовки относятся к Azure CDNу от Майкрософт.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: allensu
ms.openlocfilehash: 2475bdce3ab8f153cc837601964bf4a2e90a470c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81260420"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>HTTP-заголовок отладки для Azure CDN от Майкрософт
В заголовке отладочного ответа `X-Cache` содержатся сведения о том, на каком уровне стека CDN было получено содержимое. Этот заголовок относится к Azure CDNу от Майкрософт.

### <a name="response-header-format"></a>Формат заголовка ответа

Header | Описание
-------|------------
Кэш X: TCP_HIT | Этот заголовок возвращается, когда содержимое обслуживается из пограничной кэш CDN. 
Кэш X: TCP_REMOTE_HIT | Этот заголовок возвращается, когда содержимое обслуживается из регионального кэша CDN (уровень экранирования источника).
Кэш X: TCP_MISS | Этот заголовок возвращается при промахе кэша, и содержимое обрабатывается из источника. 


