---
title: Базовый план безопасности политики Azure для производительности системы безопасности Azure
description: В базовом плане безопасности политики Azure содержатся практические рекомендации и ресурсы для реализации рекомендаций по безопасности, указанных в статье о производительности системы безопасности Azure.
author: msmbaldwin
ms.service: azure-policy
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6c6bd3ab87013aba04f43c95b3a986a64f2bc394
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89231475"
---
# <a name="azure-policy-security-baseline-for-azure-security-benchmark"></a>Базовый план безопасности политики Azure для производительности системы безопасности Azure

Этот базовый план безопасности применяет рекомендации из [теста безопасности Azure](../../../security/benchmarks/overview.md) к политике Azure. Azure Security Benchmark содержит рекомендации по обеспечению безопасности облачных решений в Azure. Содержимое группируются по **доменам соответствия** и **элементам управления безопасностью** , определенным в производительности системы безопасности Azure, и связанным рекомендациям, применимым к политике Azure. **Элементы управления** , неприменимые к политике Azure, были исключены. Сведения о том, как полностью сопоставить политику Azure с тестовым показателем безопасности Azure, см. в [полном файле сопоставления базовых показателей безопасности политики Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Сведения о сопоставлении элементов управления производительности Azure с встроенными определениями политик с помощью встроенной инициативы см. в разделе [соответствие нормативным требованиям: производительность системы безопасности Azure](../samples/azure-security-benchmark.md).

Политика Azure использует термин _владение_ вместо _ответственности_. Дополнительные сведения о _владельце_см. в статье [определения политик](./definition-structure.md#type) и [Общие обязанности политики Azure в облаке](../../../security/fundamentals/shared-responsibility.md).


## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

*Дополнительные сведения см. в статье [Управление безопасностью: ведение журналов и мониторинг](../../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Включение журналов аудита для ресурсов Azure

**Руководство**. Политика Azure использует журналы действий, которые включаются автоматически, для включения источника событий, даты, пользователя, метки времени, исходных адресов, адресов назначения и других полезных элементов.

* [Как получить журналы и метрики платформы с помощью Azure Monitor](../../../azure-monitor/platform/diagnostic-settings.md)

* [Общие сведения о ведении журналов и различных типах журналов в Azure](../../../azure-monitor/platform/platform-logs-overview.md)


**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

## <a name="identity-and-access-control"></a>Идентификаторы и управление доступом

*Дополнительные сведения см. в статье [Управление безопасностью: идентификаторы и управление доступом](../../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="33-use-dedicated-administrative-accounts"></a>3.3. Применение выделенных административных учетных записей

**Руководство**. Создайте стандартные операционные процедуры для использования выделенных административных учетных записей. Используйте учетную запись Центра безопасности Azure и управление доступом для мониторинга количества административных учетных записей. 

Вы также можете разрешить JIT-доступ и достаточное для него решение с помощью [Azure AD privileged Identity Management](../../../active-directory/privileged-identity-management/pim-configure.md) привилегированных ролей или [Azure Resource Manager](../../../azure-resource-manager/management/overview.md).


**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6. Используйте выделенные компьютеры (рабочие станции с привилегированным доступом) для всех административных задач

**Руководство**. Используйте рабочие станции привилегированного доступа (PAW) с настроенной многофакторной проверкой подлинности для входа в ресурсы Azure и их настройки.

* [Рабочие станции с привилегированным доступом](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Включение MFA в Azure](../../../active-directory/authentication/howto-mfa-getstarted.md)


**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="data-protection"></a>Защита данных

*Дополнительные сведения см. в статье [Управление безопасностью: защита данных](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6. Контроль доступа к ресурсам с помощью RBAC

**Руководство**. Использование управления доступом на основе ролей Azure (Azure RBAC) для управления доступом к политике Azure.

* [Разрешения RBAC в Azure в политике Azure](../overview.md#rbac-permissions-in-azure-policy)

* [Настройка Azure RBAC](../../../role-based-access-control/role-assignments-portal.md)


**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Включение в журнал и создание оповещений по изменениям критических ресурсов Azure

**Руководство**. Использование Azure Monitor с журналами действий для создания оповещений о том, когда изменения выполняются в политике Azure.

* [Создание оповещений для событий журнала действий Azure](../../../azure-monitor/platform/alerts-activity-log.md)


**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

## <a name="inventory-and-asset-management"></a>Инвентаризация и управление ресурсами

*Дополнительные сведения см. в статье [Управление безопасностью: инвентаризация и управление ресурсами](../../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="62-maintain-asset-metadata"></a>6.2. Ведение метаданных активов

**Руководство**. Применяйте к ресурсам Azure теги, чтобы логически классифицировать их на основе метаданных. Используйте действие _изменение_ политики Azure для включения и соблюдения соответствия требованиям и управления тегами.

* [Учебник. Создание политик и управление ими](../tutorials/create-and-manage.md)

* [Руководство по Администрирование системы управления тегами](../tutorials/govern-tags.md)


**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: определение и обслуживание инвентаризации утвержденных ресурсов Azure

**Руководство**. Создание инвентаризации утвержденных определений политик и назначений политик в соответствии с потребностями Организации.

**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5. Отслеживание неутвержденных ресурсов Azure

**Руководство**. Использование политики Azure для ограничения типа ресурсов, которые могут быть созданы в подписках.

* [Настройка Политики Azure и управление ею](../tutorials/create-and-manage.md)


**Мониторинг Центра безопасности Azure**: Неприменимо

**Ответственность**: Customer

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь со статьей [Тесты производительности системы безопасности Azure](../../../security/benchmarks/overview.md).
- Дополнительные сведения о [базовой конфигурации безопасности Azure](../../../security/benchmarks/security-baselines-overview.md).
