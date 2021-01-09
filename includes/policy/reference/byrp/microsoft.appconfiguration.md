---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0688e5e0703f74ee48e37119edcece465e51b5e5
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98043888"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Служба "Конфигурация приложений" должна использовать управляемый клиентом ключ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967a4b4b-2da9-43c1-b7d0-f98d0d74d0b1) |Ключи, управляемые клиентом, обеспечивают улучшенную защиту данных, позволяя вам управлять своими ключами шифрования. Это часто необходимо для удовлетворения требований по обеспечению соответствия. |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/CustomerManagedKey_Audit.json) |
|[Конфигурация приложения должна использовать закрытую ссылку](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Частная связь Azure позволяет подключить виртуальную сеть к службам Azure без общедоступного IP-адреса в источнике или месте назначения. Платформа частной связи обрабатывает подключение между потребителем и службами через магистральную сеть Azure. Сопоставляя частные конечные точки с экземплярами конфигурации приложения, а не со всей службой, вы также будете защищаться от рисков утечки данных. Дополнительные сведения см. по адресу: [https://aka.ms/appconfig/private-endpoint](https://aka.ms/appconfig/private-endpoint) . |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
