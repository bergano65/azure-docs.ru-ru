---
title: Отладка заголовков HTTP для Azure CDN от Майкрософт | Документация Майкрософт
description: Заголовки запросов отладочного кэша содержат дополнительные сведения о политике кэша, примененной к запрошенному ресурсу. Эти заголовки относятся к Azure CDNу от Майкрософт.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: magattus
ms.openlocfilehash: be6655c2c5e6b2acca82b4a1b20a3cdf84e0251b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707530"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>HTTP-заголовок отладки для Azure CDN от Майкрософт
В заголовке `X-Cache`отладочного ответа содержатся сведения о том, на каком уровне стека CDN было получено содержимое. Этот заголовок относится к Azure CDNу от Майкрософт.

### <a name="response-header-format"></a>Формат заголовка ответа

Header | Описание
-------|------------
Кэш X: TCP_HIT | Этот заголовок возвращается, когда содержимое обслуживается из пограничной кэш CDN. 
Кэш X: TCP_REMOTE_HIT | Этот заголовок возвращается, когда содержимое обслуживается из регионального кэша CDN (уровень экранирования источника).
Кэш X: TCP_MISS | Этот заголовок возвращается при промахе кэша, и содержимое обрабатывается из источника. 


