---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: eac282190469d2425443fadd92e5a852e85168a1
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99558502"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Общедоступные панели мониторинга не должны иметь плитки с поддержкой markdown, содержащие встроенное содержимое](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Запрет на создание общедоступной панели мониторинга с плитками с поддержкой Markdown, которые содержат встроенное содержимое, и требование к сохранению содержимого в виде файла Markdown, развернутого на сервере Интернета. Используя для плитки Markdown встроенное содержимое, вы лишитесь возможности управлять шифрованием содержимого. Однако вы сможете выполнить шифрование, двукратное шифрование и даже использовать собственные ключи, настроив собственное хранилище. При включении этой политики пользователи смогут использовать только предварительную версию REST API общедоступных панелей мониторинга за 01.09.2020 или более позднюю версию. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
