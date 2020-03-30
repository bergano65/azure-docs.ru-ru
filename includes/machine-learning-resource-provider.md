---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75893972"
---
При создании рабочего пространства Машинного обучения Azure или ресурса, используемого рабочим пространством, может возникнуть ошибка, аналогичная следующим сообщениям:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

Большинство поставщиков ресурсов регистрируются автоматически, но не все. Если вы получили это сообщение, вам необходимо зарегистрировать указанного поставщика.

Для получения информации о регистрации поставщиков ресурсов [см.](../articles/azure-resource-manager/templates/error-register-resource-provider.md)