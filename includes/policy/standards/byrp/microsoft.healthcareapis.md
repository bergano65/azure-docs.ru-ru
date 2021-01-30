---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 11e14a435f6ea33cf9f4f8a7dd3804c95187c9bf
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99106264"
---
## <a name="cmmc-level-3"></a>CMMC уровня 3

Сведения о том, как доступные встроенные политики Azure для всех служб Azure сопоставляются с этим стандартом соответствия, см. в разделе [Политика Azure: соответствие нормативным требованиям — уровень кммк 3](../../../../articles/governance/policy/samples/cmmc-l3.md).
Дополнительные сведения об этом стандарте соответствия см. в разделе [сертификация модели кибербезопасности дата_вступления_в_силу (кммк)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Домен |Идентификатор элемента управления |Заголовок элемента управления |Политика<br /><sub>(портал Azure)</sub> |Версия политики<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Управление доступом |AC. 1,001 |Ограничьте доступ информационной системы к полномочным пользователям, процессам, действующим от имени полномочных пользователей, и устройствам (включая другие информационные системы). |[Средства CORS не должны разрешать всем доменам доступ к API для FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Управление доступом |AC. 1.002 |Ограничьте доступ информационной системы к типам транзакций и функций, которые разрешено выполнять разрешенным пользователям. |[Средства CORS не должны разрешать всем доменам доступ к API для FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Управление доступом |AC. 2.016 |Управление потоком контролируемой несекретной информации в соответствии с утвержденными авторизациями |[Средства CORS не должны разрешать всем доменам доступ к API для FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Управление конфигурацией |CM. 3.068 |Ограничение, отключение или предотвращение использования ненужных программ, функций, портов, протоколов и служб. |[Средства CORS не должны разрешать всем доменам доступ к API для FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Защита системы и средств передачи данных |SC. 3.177 |Применяйте шифрование, проверенное FIPS, при использовании для защиты конфиденциальности НЕСЕКРЕТНАЯ. |[Служба "Azure API для FHIR" должна использовать для шифрования неактивных данных ключ, управляемый клиентом (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|Защита системы и средств передачи данных |SC. 3.183 |По умолчанию запрещает трафик сетевого взаимодействия и разрешает трафик сетевого взаимодействия по исключениям (т. е. запретить все, разрешить по исключению). |[Средства CORS не должны разрешать всем доменам доступ к API для FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |

