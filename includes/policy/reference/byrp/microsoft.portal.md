---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c3c9e698aeddbe4818e7b77596eed81e60cb38e5
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2020
ms.locfileid: "95002168"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Общие панели мониторинга не должны содержать плитки Markdown со встроенным содержимым](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Запретить создание общей панели мониторинга, которая содержит встроенное содержимое в плитках Markdown, и обеспечить сохранение содержимого в виде файла Markdown, размещенного в сети. Если вы используете встроенное содержимое на плитке Markdown, вы не сможете управлять шифрованием содержимого. Настроив собственное хранилище, вы можете зашифровать, дважды шифровать и даже приносить свои собственные ключи. При включении этой политики пользователи смогут использовать общие панели мониторинга 2020-09-01-Preview или более поздней версии REST API. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
