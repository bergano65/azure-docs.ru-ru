---
title: Тестирование безопасности Azure v2 — управление ресурсами
description: Управление ресурсами производительности Azure с производительностью 2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 845c4f3194f35c68306e74734e5ecc6109d9c103
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318030"
---
# <a name="security-control-v2-asset-management"></a>Управление безопасностью версии 2: Управление активами

Управление активами охватывает элементы управления, обеспечивающие видимость и управление безопасностью ресурсов Azure. Сюда входят рекомендации по разрешениям для персонала по обеспечению безопасности, доступу к инвентаризации активов и управлению утверждениями для служб и ресурсов (инвентаризации, трассировки и исправления).

## <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: обеспечение контроля над рисками для ресурсов в группе безопасности

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| AM – 1 | 1,1, 1,2 | CM-8, PM-5 |

Убедитесь, что группам безопасности предоставлены разрешения читателя по безопасности в клиенте и подписках Azure, чтобы они могли отслеживать риски безопасности с помощью центра безопасности Azure. 

В зависимости от структуры обязанностей группы безопасности, наблюдение за угрозами безопасности может быть обязанностью централизованной группы безопасности или локальной группы. С другой стороны, ценные сведения и риски безопасности всегда должны быть объединены централизованно внутри Организации. 

Разрешения на чтение безопасности можно расширить для всего клиента (корневой группы управления) или в области группы управления или конкретных подписок. 

Примечание. для наблюдения за рабочими нагрузками и службами могут потребоваться дополнительные разрешения. 

- [Общие сведения о роли читателя безопасности](../../role-based-access-control/built-in-roles.md#security-reader)

- [Общие сведения о Группы управления Azure](../../governance/management-groups/overview.md)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов** (дополнительные[сведения](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Безопасность инфраструктуры и конечных точек](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Управление соответствием требованиям безопасности](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Обеспечьте доступ группы безопасности к инвентаризации и метаданным активов

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| AM – 2 | 1,1, 1,2, 1,4, 1,5, 9,1, 12,1 | CM-8, PM-5 |

Убедитесь, что группы безопасности имеют доступ к постоянно обновленной инвентаризации ресурсов в Azure. Группы безопасности часто нуждаются в этой инвентаризации, чтобы оценить потенциальную угрозу для новых рисков, а также в качестве входных данных для непрерывного улучшения безопасности. 

Функция инвентаризации Центра безопасности Azure и граф ресурсов Azure позволяют запрашивать и обнаруживать все ресурсы в подписках, включая службы Azure, приложения и сетевые ресурсы.  

Логически упорядочивайте активы в соответствии с классификацией вашей организации с помощью тегов, а также других метаданных в Azure (имя, описание и категория).  

- [Создание запросов с помощью обозревателя графа ресурсов Azure](../../governance/resource-graph/first-query-portal.md)

- [Управление инвентаризацией активов в центре безопасности Azure](../../security-center/asset-inventory.md)

- [Дополнительные сведения о разметке ресурсов см. в разделе Описание принятия решений по именованию и маркировке.](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов** (дополнительные[сведения](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Безопасность инфраструктуры и конечных точек](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Управление соответствием требованиям безопасности](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-3-use-only-approved-azure-services"></a>AM-3. Используйте только утвержденные службы Azure

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| AM – 3 | 2.3, 2.4 | CM-7, CM-8 |

Используйте политику Azure для аудита и ограничения служб, которые пользователи могут подготавливать в вашей среде. Используйте граф ресурсов Azure для запроса и обнаружения ресурсов в своих подписках.  Можно также использовать Azure Monitor, чтобы создать правила для активации оповещений при обнаружении неутвержденной службы.

- [Настройка и управление политикой Azure](../../governance/policy/tutorials/create-and-manage.md)

- [Как отказаться от определенного типа ресурса с помощью Политики Azure](/azure/governance/policy/samples/not-allowed-resource-types)

- [Создание запросов с помощью обозревателя графа ресурсов Azure](../../governance/resource-graph/first-query-portal.md)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов** (дополнительные[сведения](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Управление соответствием требованиям безопасности](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Управление состоянием](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

## <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: обеспечение безопасности управления жизненным циклом активов

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| AM – 4 | 2,3, 2,4, 2,5 | CM-7, CM-8, CM-10, CM-11 |

Установите или обновите политики безопасности, которые устраняют процессы управления жизненным циклом активов для потенциально высоких изменений. К таким изменениям относятся: поставщики удостоверений и доступ, чувствительность к данным, конфигурация сети и назначение прав администратора.

Удалите ресурсы Azure, когда они больше не нужны.

- [Удаление группы ресурсов и ресурса Azure](../../azure-resource-manager/management/delete-resource-group.md)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов** (дополнительные[сведения](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Безопасность инфраструктуры и конечных точек](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Управление состоянием](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Управление соответствием требованиям безопасности](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: ограничение возможности пользователей взаимодействовать с Azure Resource Manager

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| AM – 5 | 2,9 | AC-3 |

Используйте условный доступ Azure AD, чтобы ограничить возможность пользователей взаимодействовать с Azure Resource Manager путем настройки "блокировать доступ" для приложения "Управление Microsoft Azure".

- [Настройка условного доступа для блокировки доступа к диспетчеру ресурсов Azure](../../role-based-access-control/conditional-access-azure-management.md)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов** (дополнительные[сведения](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Управление состоянием](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Безопасность инфраструктуры и конечных точек](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6. использование только утвержденных приложений в ресурсах вычислений

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| ОТ AM ДО 6 | 2,6, 2,7 | AC-3, CM-7, CM-8, CM-10, CM-11 |

Убедитесь, что выполняется только авторизованное программное обеспечение, и все несанкционированные программы блокируются на виртуальных машинах Azure.

Используйте адаптивные элементы управления приложением Azure Security Center (ASC) для обнаружения и создания списка разрешений приложения. Можно также использовать адаптивные элементы управления приложениями ASC, чтобы гарантировать выполнение только авторизованного программного обеспечения и блокирование выполнения всех несанкционированных программ на виртуальных машинах Azure.

Используйте Отслеживание изменений и инвентаризацию службы автоматизации Azure для автоматизации сбора данных инвентаризации с виртуальных машин Windows и Linux. Имя программного обеспечения, версия, издатель и время обновления доступны на портал Azure. Чтобы получить дату установки программного обеспечения и другие сведения, включите диагностику на уровне гостя и направьте журналы событий Windows в Log Analytics рабочую область.

В зависимости от типа сценариев можно использовать конфигурации операционной системы или сторонние ресурсы, чтобы ограничить возможность пользователей выполнять сценарии в ресурсах вычислений Azure. 

Кроме того, можно использовать стороннее решение для обнаружения и определения неутвержденного программного обеспечения.

- [Как использовать адаптивные элементы управления приложениями в центре безопасности Azure](../../security-center/security-center-adaptive-application.md)

- [Общие сведения о Отслеживание изменений и инвентаризации службы автоматизации Azure](../../automation/change-tracking.md)

- [Управление выполнением скриптов PowerShell в средах Windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов** (дополнительные[сведения](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Безопасность инфраструктуры и конечных точек](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Управление состоянием](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Управление соответствием требованиям безопасности](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

