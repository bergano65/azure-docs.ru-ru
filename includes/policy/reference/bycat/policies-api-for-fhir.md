---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ab5955517331744a3b3ea7d2e8929e2651e24373
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99215218"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Служба "Azure API для FHIR" должна использовать для шифрования неактивных данных ключ, управляемый клиентом (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |Используйте ключи, управляемые клиентом, чтобы управлять шифрованием неактивных данных, хранящихся в службе "Azure API для FHIR", если это предусмотрено нормативными требованиями. Кроме того, ключи, управляемые клиентом,обеспечивают двойное шифрование, добавляя второй уровень шифрования поверх заданного по умолчанию способа (с помощью ключей, управляемых службой). |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|[Служба "Azure API для FHIR" должна использовать частный канал](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Служба "Azure API для FHIR" должна иметь по крайней мере одно утвержденное подключение к частной конечной точке. Клиенты в виртуальной сети могут безопасно обращаться к ресурсам, которые подключаются к частным конечным точкам через приватные каналы. Дополнительные сведения см. по адресу [https://aka.ms/fhir-privatelink](https://aka.ms/fhir-privatelink). |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[Средства CORS не должны разрешать всем доменам доступ к API для FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |Средства общего доступа к ресурсам независимо от источника (CORS) не должны разрешать доступ к вашему API для FHIR. Чтобы защитить API для FHIR, запретите доступ для всех доменов и явно укажите домены, которым разрешено подключаться. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
