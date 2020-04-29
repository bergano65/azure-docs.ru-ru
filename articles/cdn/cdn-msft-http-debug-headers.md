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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81260420"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>HTTP-заголовок отладки для Azure CDN от Майкрософт
В заголовке `X-Cache`отладочного ответа содержатся сведения о том, на каком уровне стека CDN было получено содержимое. Этот заголовок относится к Azure CDNу от Майкрософт.

### <a name="response-header-format"></a>Формат заголовка ответа

Заголовок | Описание
-------|------------
Кэш X: TCP_HIT | Этот заголовок возвращается, когда содержимое обслуживается из пограничной кэш CDN. 
Кэш X: TCP_REMOTE_HIT | Этот заголовок возвращается, когда содержимое обслуживается из регионального кэша CDN (уровень экранирования источника).
Кэш X: TCP_MISS | Этот заголовок возвращается при промахе кэша, и содержимое обрабатывается из источника. 


