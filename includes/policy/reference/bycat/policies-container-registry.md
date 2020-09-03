---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8920752a6adfe5f3259c4bf701173d2d817aa7da
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291026"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[В реестрах контейнеров должно использоваться шифрование с помощью ключа, управляемого клиентом (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Аудит реестров контейнеров, для которых не включено шифрование с помощью ключей, управляемых клиентом (CMK). Дополнительные сведения о шифровании с помощью CMK см. на странице [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK). |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[В реестрах контейнеров не должен быть разрешен неограниченный сетевой доступ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Аудит реестров контейнеров, для которых не настроены правила сети (правила IP-адресов или виртуальной сети) и по умолчанию разрешен весь сетевой доступ. Реестры контейнеров, для которых настроено по крайней мере одно правило IP-адресов или брандмауэра либо виртуальная сеть, считаются соответствующими этой политике. Дополнительные сведения о правилах сетевого доступа в Реестре контейнеров см. на странице [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet). |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Реестры контейнеров должны использовать приватные каналы](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Аудит реестров контейнеров, которые не содержат по меньшей мере одного утвержденного подключения к частной конечной точке. Клиенты в виртуальной сети могут безопасно обращаться к ресурсам, которые подключаются к частным конечным точкам через приватные каналы. Дополнительные сведения см. по адресу [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link). |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
