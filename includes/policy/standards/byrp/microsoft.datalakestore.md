---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f01b50fe181feb1c399b7dc9bd436a708605c297
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92314859"
---
## <a name="azure-security-benchmark"></a>Azure Security Benchmark

[Azure Security Benchmark](../../../../articles/security/benchmarks/overview.md) содержит рекомендации по обеспечению безопасности облачных решений в Azure. Сведения о том, как эта служба полностью сопоставляется с Azure Security Benchmark, см. [на этой странице](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Дополнительную информацию о том, как доступные встроенные компоненты Политики Azure для всех служб Azure сопоставляются с этим стандартом соответствия, см. в статье [Подробные сведения о встроенной инициативе о соответствии нормативным требованиям теста производительности системы безопасности Azure](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Домен |Идентификатор элемента управления |Заголовок элемента управления |Политика<br /><sub>(портал Azure)</sub> |Версия политики<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Ведение журналов и мониторинг |2.3 |Включение журналов аудита для ресурсов Azure |[В Azure Data Lake Store должны быть включены журналы диагностики](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Дополнительную информацию о том, как доступные встроенные компоненты Политики Azure для всех служб Azure сопоставляются с этим стандартом соответствия, см. в статье со [сведениями о встроенной инициативе по обеспечению соответствия стандарту HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Дополнительные сведения об этом стандарте соответствия см. на странице описания [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Домен |Идентификатор элемента управления |Заголовок элемента управления |Политика<br /><sub>(портал Azure)</sub> |Версия политики<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Ведение журнала аудита |1202.09aa1System.1 — 09.aa |Защищенная запись аудита создается для всех действий в системе (создание, чтение, обновление, удаление), связанных с соответствующей информацией. |[В Azure Data Lake Store должны быть включены журналы диагностики](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|Управление съемными носителями |0304.09o3Organizational.1 — 09.o |Организация ограничивает использование съемных носителей с возможностью записи и персональных съемных носителей в системах организации. |[Требовать шифрование учетных записей Data Lake Store](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7ff3161-0087-490a-9ad9-ad6217f4f43a) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |

