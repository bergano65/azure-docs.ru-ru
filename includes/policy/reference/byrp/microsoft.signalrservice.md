---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f6d1eeaada4f93ac19dafc0895d0faf3f53eac95
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050646"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Служба Azure SignalR должна использовать закрытую ссылку](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |Частная связь Azure позволяет подключить виртуальную сеть к службам Azure без общедоступного IP-адреса в источнике или месте назначения. Платформа частной связи обрабатывает подключение между потребителем и службами через магистральную сеть Azure. Сопоставляя частные конечные точки с ресурсами SignalR вместо всей службы, вы также будете защищаться от рисков утечки данных. Дополнительные сведения см. по адресу: [https://aka.ms/asrs/privatelink](https://aka.ms/asrs/privatelink) . |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
