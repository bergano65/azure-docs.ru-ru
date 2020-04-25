---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/24/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f922c10d42406827e3b64d212d06253ba8f2a0e1
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82144987"
---
|Имя |Описание |Действие |Версия |GitHub |
|---|---|---|---|---|
|[В реестрах контейнеров должно использоваться шифрование с помощью ключа, управляемого клиентом (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Аудит реестров контейнеров, для которых не включено шифрование с помощью ключей, управляемых клиентом (CMK). Дополнительные сведения о шифровании с помощью CMK см. на странице [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK). |Audit, Disabled |1.0.0 (предварительная версия) |[Ссылка](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json)
|[В реестрах контейнеров не должен быть разрешен неограниченный сетевой доступ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Аудит реестров контейнеров, для которых не настроены правила сети (правила IP-адресов или виртуальной сети) и по умолчанию разрешен весь сетевой доступ. Реестры контейнеров, для которых настроено по крайней мере одно правило IP-адресов или брандмауэра либо виртуальная сеть, считаются соответствующими этой политике. Дополнительные сведения о правилах сетевого доступа в Реестре контейнеров см. на странице [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet). |Audit, Disabled |1.0.0 (предварительная версия) |[Ссылка](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json)
