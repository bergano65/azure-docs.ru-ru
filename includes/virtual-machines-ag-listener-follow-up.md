---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097706"
---
После создания прослушивателя группы доступности, возможно, придется настроить параметры кластера RegisterAllProvidersIP и HostRecordTTL для ресурса прослушивателя. Эти параметры могут снизить время переподключения после отработки отказа, что может препятствовать истечению времени ожидания подключения. Дополнительные сведения об этих параметрах, а также образец кода см. в разделе [Ключевое слово и связанные функции MultiSubnetFailover](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).

<!-- Update_Description: update meta properties -->