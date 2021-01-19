---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f03688012100c31865fb25087f7b4049c087b43e
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98047556"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Домены Сетки событий Azure должны использовать приватный канал](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |Приватный канал Azure позволяет подключить виртуальную сеть к службам Azure без общедоступного IP-адреса в исходном или целевом расположении. Платформа приватного канала обрабатывает подключение между потребителем и службами через магистральную сеть Azure. Сопоставление частных конечных точек с доменами Сетки событий, а не всей службой также обеспечивает защиту от утечки данных. Дополнительные сведения см. по адресу [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints). |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridDomains_EnablePrivateEndpoint_Audit.json) |
|[Разделы Сетки событий Azure должны использовать приватный канал](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |Приватный канал Azure позволяет подключить виртуальную сеть к службам Azure без общедоступного IP-адреса в исходном или целевом расположении. Платформа Приватного канала поддерживает подключение между потребителем и службами через магистральную сеть Azure. Сопоставив частные конечные точки с разделами вместо всей службы, вы также обеспечите защиту от рисков утечки данных. См. дополнительные сведения: [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints). |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridTopics_EnablePrivateEndpoint_Audit.json) |
