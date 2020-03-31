---
title: Отошибок заголовки HTTP для Azure CDN от Microsoft (ru) Документы Майкрософт
description: Заголовки запросов кэша debug предоставляют дополнительную информацию о политике кэша, применяемой к запрашиваемому активу. Эти заголовки специфичны для Azure CDN от корпорации Майкрософт.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68814072"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Отображение заголовка HTTP для Azure CDN от Microsoft
Отладка ответ заголовок, `X-Cache`, предоставляет подробную информацию о том, какой слой стека CDN содержание было подано от. Эта заголовок специфична для Azure CDN от корпорации Майкрософт.

### <a name="response-header-format"></a>Формат заголовка ответа

Заголовок | Описание
-------|------------
X-Cache: TCP_HIT | Этот заголовок возвращается, когда содержимое подается из кэша края CDN. 
X-Cache: TCP_REMOTE_HIT | Эта заголовок возвращается, когда содержимое подается из регионального кэша CDN (слой щита Origin)
X-Кэш: TCP_MISS | Этот заголовок возвращается, когда происходит промах кэша, и содержимое подается из Origin. 


