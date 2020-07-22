---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75893972"
---
При создании рабочей области машинного обучения Azure или ресурса, используемого рабочей областью, может появиться сообщение об ошибке, аналогичное приведенному ниже.

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

Многие, но не все поставщики ресурсов регистрируются автоматически. При появлении этого сообщения необходимо зарегистрировать упомянутый поставщик.

Сведения о регистрации поставщиков ресурсов см. в статье [Устранение ошибок регистрации поставщика ресурсов](../articles/azure-resource-manager/templates/error-register-resource-provider.md).