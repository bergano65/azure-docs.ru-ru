---
title: Отладка заголовков HTTP для Azure CDN от Майкрософт | Документация Майкрософт
description: Заголовки запросов отладочного кэша содержат дополнительные сведения о политике кэша, примененной к запрошенному ресурсу. Эти заголовки относятся к Azure CDNу от Майкрософт.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: magattus
ms.openlocfilehash: 297c65c1cd89163b8663819f844dc6c2a83fd1bf
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814072"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>HTTP-заголовок отладки для Azure CDN от Майкрософт
В заголовке `X-Cache`отладочного ответа содержатся сведения о том, на каком уровне стека CDN было получено содержимое. Этот заголовок относится к Azure CDNу от Майкрософт.

### <a name="response-header-format"></a>Формат заголовка ответа

Header | Описание
-------|------------
Кэш X: TCP_HIT | Этот заголовок возвращается, когда содержимое обслуживается из пограничной кэш CDN. 
Кэш X: TCP_REMOTE_HIT | Этот заголовок возвращается, когда содержимое обслуживается из регионального кэша CDN (уровень экранирования источника).
Кэш X: TCP_MISS | Этот заголовок возвращается при промахе кэша, и содержимое обрабатывается из источника. 


