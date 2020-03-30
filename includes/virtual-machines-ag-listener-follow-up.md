---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67185234"
---
После создания прослушивателя группы доступности, возможно, придется настроить параметры кластера RegisterAllProvidersIP и HostRecordTTL для ресурса прослушивателя. Эти параметры могут снизить время переподключения после отработки отказа, что может препятствовать истечению времени ожидания подключения. Дополнительные сведения об этих параметрах, а также образец кода см. в разделе [Ключевое слово и связанные функции MultiSubnetFailover](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).

