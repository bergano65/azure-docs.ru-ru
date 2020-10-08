---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 52997495b5c46c89d3abfbb836f90d2363ee6d4f
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91821118"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[В реестрах контейнеров должно использоваться шифрование с помощью ключа, управляемого клиентом (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Аудит реестров контейнеров, для которых не включено шифрование с помощью ключей, управляемых клиентом (CMK). Azure автоматически шифрует содержимое реестра при хранении с помощью ключей, управляемых службой. Шифрование по умолчанию можно расширить с помощью дополнительного уровня шифрования, используя ключ, который вы создаете и которым управляете в Azure Key Vault. Дополнительные сведения о шифровании с помощью CMK см. на странице [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK). |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[В реестрах контейнеров не должен быть разрешен неограниченный сетевой доступ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Аудит реестров контейнеров, для которых не настроены правила сети или брандмауэра (правила IP-адресов) и по умолчанию разрешен весь сетевой доступ. Ограничение сетевого доступа защищает реестры контейнеров от потенциальных угроз. Реестры контейнеров, для которых настроены по крайней мере одно правило IP-адресов или брандмауэра либо виртуальная сеть, считаются соответствующими этой политике. Дополнительные сведения о сетевых правилах Реестра контейнеров см. здесь: [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) и [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet). |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Реестры контейнеров должны использовать приватные каналы](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Аудит реестров контейнеров, которые не содержат хотя бы одно утвержденное подключение к частной конечной точке. Клиенты в виртуальной сети могут безопасно обращаться к ресурсам, которые подключаются к частным конечным точкам через приватные каналы. Открытый доступ можно отключить, чтобы для подключения к реестру использовались только приватные каналы. Дополнительные сведения см. по адресу [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link). |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
