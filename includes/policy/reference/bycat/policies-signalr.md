---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f6d1eeaada4f93ac19dafc0895d0faf3f53eac95
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98047654"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Служба Azure SignalR должна использовать приватный канал](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |Приватный канал Azure позволяет подключить виртуальную сеть к службам Azure без общедоступного IP-адреса в исходном или целевом расположении. Платформа Приватного канала поддерживает подключение между потребителем и службами через магистральную сеть Azure. Сопоставление частных конечных точек с ресурсами SignalR вместо всей службы обеспечивает защиту от утечки данных. См. дополнительные сведения: [https://aka.ms/asrs/privatelink](https://aka.ms/asrs/privatelink). |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
