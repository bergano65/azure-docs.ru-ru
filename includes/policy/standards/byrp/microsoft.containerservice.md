---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/22/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e7750049ea7c2e504aec325c39b2760e93e3c9dd
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351799"
---
## <a name="azure-security-benchmark"></a>Azure Security Benchmark

[Azure Security Benchmark](../../../../articles/security/benchmarks/overview.md) содержит рекомендации по обеспечению безопасности облачных решений в Azure. Сведения о том, как эта служба полностью сопоставляется с Azure Security Benchmark, см. [на этой странице](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Дополнительную информацию о том, как доступные встроенные компоненты Политики Azure для всех служб Azure сопоставляются с этим стандартом соответствия, см. в статье [Подробные сведения о встроенной инициативе о соответствии нормативным требованиям теста производительности системы безопасности Azure](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Домен |Идентификатор элемента управления |Заголовок элемента управления |Политика<br /><sub>(портал Azure)</sub> |Версия политики<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Безопасность сети |1,1 |Защита ресурсов в виртуальной сети с помощью групп безопасности сети или Брандмауэра Azure |[В службах Kubernetes нужно определить разрешенные диапазоны IP-адресов](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |[1.0.1 (предварительная версия)](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
|Защита данных |4.6 |Контроль доступа к ресурсам с помощью RBAC |[В службах Kubernetes следует использовать управление доступом на основе ролей (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.1 (предварительная версия)](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|Управление уязвимостями |5,3 |Развертывание автоматизированного решения для управления исправлениями ПО сторонних производителей |[Службы Kubernetes должны быть обновлены до версии с устраненными уязвимостями](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |[1.0.1 (предварительная версия)](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |
|Настройка безопасности |7.3 |Сохранение безопасных конфигураций для ресурсов Azure |[ [предварительная версия]: Нужно определить политики безопасности объекта pod в службах Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3abeb944-26af-43ee-b83d-32aaf060fb94) |[1.0.0 (предварительная версия)](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnablePSP_KubernetesService_Audit.json) |
|Настройка безопасности |7.9 |Реализация автоматизированного мониторинга конфигурации для служб Azure |[ [предварительная версия]: Нужно определить политики безопасности объекта pod в службах Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3abeb944-26af-43ee-b83d-32aaf060fb94) |[1.0.0 (предварительная версия)](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnablePSP_KubernetesService_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark"></a>Тест производительности CIS для платформ Microsoft Azure

Дополнительную информацию о том, как доступные встроенные компоненты Политики Azure для всех служб Azure сопоставляются с этим стандартом соответствия, см. в статье [Подробные сведения о встроенной инициативе о соответствии нормативным требованиям теста производительности CIS для платформ Microsoft Azure](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md).
Дополнительные сведения об этом стандарте соответствия требованиям см. в статье [Тест производительности CIS для платформ Microsoft Azure](https://www.cisecurity.org/benchmark/azure/).

|Домен |Идентификатор элемента управления |Заголовок элемента управления |Политика<br /><sub>(портал Azure)</sub> |Версия политики<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Прочие вопросы по безопасности |8.5 |Включение управления доступом на основе ролей (RBAC) в службе Azure Kubernetes |[В службах Kubernetes следует использовать управление доступом на основе ролей (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.1 (предварительная версия)](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="nist-sp-800-171-r2"></a>NIST SP 800-171 R2

Дополнительную информацию о том, как доступные встроенные компоненты Политики Azure для всех служб Azure сопоставляются с этим стандартом соответствия, см. в статье [Сведения о встроенной инициативе по соответствию требованиям стандарта NIST SP 800-171 R2](../../../../articles/governance/policy/samples/nist-sp-800-171-r2.md).
Дополнительные сведения об этом стандарте соответствия см. на странице описания [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final) (специальной публикации Национального института стандартов и технологий).

|Домен |Идентификатор элемента управления |Заголовок элемента управления |Политика<br /><sub>(портал Azure)</sub> |Версия политики<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Целостность системы и данных |3.14.1 |Своевременное выявление и исправление системных уязвимостей, а также составление отчетов о них |[Службы Kubernetes должны быть обновлены до версии с устраненными уязвимостями](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |[1.0.1 (предварительная версия)](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |

