---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893972"
---
При создании рабочей области Машинное обучение Azure или ресурса, используемого рабочей областью, может появиться сообщение об ошибке, аналогичное приведенному ниже.

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

Большинство поставщиков ресурсов регистрируются автоматически, но не все. При появлении этого сообщения необходимо зарегистрировать упомянутый поставщик.

Сведения о регистрации поставщиков ресурсов см. в разделе [Устранение ошибок регистрации поставщика ресурсов](../articles/azure-resource-manager/templates/error-register-resource-provider.md).