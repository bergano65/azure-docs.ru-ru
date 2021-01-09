---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f03688012100c31865fb25087f7b4049c087b43e
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050918"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[В доменах службы "Сетка событий Azure" должна использоваться частная ссылка](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |Частная связь Azure позволяет подключить виртуальную сеть к службам Azure без общедоступного IP-адреса в источнике или месте назначения. Платформа частной связи обрабатывает подключение между потребителем и службами через магистральную сеть Azure. Сопоставляя частные конечные точки с доменами службы "Сетка событий", а не со всей службой, вы также будете защищаться от рисков утечки данных. Дополнительные сведения см. по адресу: [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints) . |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridDomains_EnablePrivateEndpoint_Audit.json) |
|[В разделах службы "Сетка событий Azure" должна использоваться частная ссылка](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |Частная связь Azure позволяет подключить виртуальную сеть к службам Azure без общедоступного IP-адреса в источнике или месте назначения. Платформа частной связи обрабатывает подключение между потребителем и службами через магистральную сеть Azure. Сопоставляя частные конечные точки с разделами, а не со всей службой, вы также будете защищаться от рисков утечки данных. Дополнительные сведения см. по адресу: [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints) . |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridTopics_EnablePrivateEndpoint_Audit.json) |
