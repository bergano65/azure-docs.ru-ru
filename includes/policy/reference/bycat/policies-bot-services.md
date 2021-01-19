---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d276774ab21563f584d03056ce670a6d01398908
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98047513"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Конечная точка Службы Bot должна быть допустимым URI HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |Данные могут быть изменены во время передачи. Существуют протоколы, которые обеспечивают шифрование для избежания проблем неправильного использования и незаконного изменения. Чтобы обеспечить взаимодействие ботов только по зашифрованным каналам, задайте в качестве конечной точки допустимый URI HTTPS. Это гарантирует, что протокол HTTPS используется для шифрования передаваемых данных, и часто является требованием для обеспечения соответствия нормативным или отраслевым стандартам. Перейдите по адресу [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines). |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Services/BotService_ValidEndpoint_Audit.json) |
