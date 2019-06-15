---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66165393"
---
После создания прослушивателя группы доступности, возможно, придется настроить параметры кластера RegisterAllProvidersIP и HostRecordTTL для ресурса прослушивателя. Эти параметры могут снизить время переподключения после отработки отказа, что может препятствовать истечению времени ожидания подключения. Дополнительные сведения об этих параметрах, а также образец кода см. в разделе [Ключевое слово и связанные функции MultiSubnetFailover](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).

