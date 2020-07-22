---
title: Управление безопасностью Azure — управление запасами и активами
description: Управление безопасностью Azure инвентаризации и управления активами
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: aece7278d134145e00b027c184f3817d5566e1e5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81408358"
---
# <a name="security-control-inventory-and-asset-management"></a>Управление безопасностью: Инвентаризация и управление активами

Рекомендации по управлению запасами и активами касаются проблем, связанных с активностью управления (инвентаризацией, трассировкой и исправлением) всех ресурсов Azure, чтобы получить доступ только к авторизованным ресурсам, а несанкционированные и неуправляемые ресурсы идентифицируются и удаляются.

## <a name="61-use-automated-asset-discovery-solution"></a>6,1. Использование автоматизированного решения для обнаружения ресурсов

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 6.1 | 1,1, 1,2, 1,3, 1,4, 9,1, 12,1 | Customer |

Используйте граф ресурсов Azure для запроса или обнаружения всех ресурсов (например, вычислений, хранилища, сети, портов и протоколов и т. д.) в ваших подписках.  Убедитесь в том, что в вашем клиенте есть соответствующие разрешения (на чтение) и вы можете перечислить все подписки Azure, а также ресурсы в ваших подписках.

Хотя классические ресурсы Azure можно обнаружить через Resource Graph, настоятельно рекомендуется в дальнейшем создавать и использовать ресурсы Azure Resource Manager.

- [Как создавать запросы с помощью Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Как просматривать подписки Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Общие сведения об Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="62-maintain-asset-metadata"></a>6.2. Ведение метаданных активов

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 6.2 | 1.5 | Customer |

Примените теги к ресурсам Azure, предоставив метаданные для логической организации их в таксономию.

- [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="63-delete-unauthorized-azure-resources"></a>6.3. Удаление неавторизованных ресурсов Azure

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 6.3 | 1.6 | Customer |

Используйте теги, группы управления и отдельные подписки, если это необходимо, для Организации и мониторинга ресурсов. Регулярно сверяйте ресурсы, чтобы своевременно удалять неавторизованные ресурсы из подписки.

- [Создание дополнительных подписок Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Создание групп управления](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: определение и обслуживание инвентаризации утвержденных ресурсов Azure

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 6.4 | 2.1 | Customer |

Создайте инвентаризацию утвержденных ресурсов Azure и утвержденного программного обеспечения для ресурсов вычислений в соответствии с потребностями Организации.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5. Отслеживание неутвержденных ресурсов Azure

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 6,5 | 2.3, 2.4 | Customer |

Используйте политику Azure, чтобы ограничить тип ресурсов, которые могут быть созданы в ваших подписках.

Используйте Azure Resource Graph для запроса или обнаружения ресурсов в подписках.  Убедитесь в том, что все ресурсы Azure, представленные в среде, утверждены.

- [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Как создавать запросы с помощью Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6. Отслеживание неутвержденных программных приложений в рамках ресурсов вычислений

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 6.6 | 2.3, 2.4 | Customer |

Используйте инвентаризацию виртуальных машин Azure, чтобы автоматизировать сбор сведений обо всех программах на виртуальных машинах. Имя программного обеспечения, версия, издатель и время обновления доступны на портал Azure. Чтобы получить доступ к дате установки и другим сведениям, включите диагностику на уровне гостя и перенесите журналы событий Windows в рабочую область Log Analytics.

- [Включение инвентаризации виртуальных машин Azure](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7. Удаление неутвержденных ресурсов Azure и программных приложений

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 6.7 | 2.5 | Customer |

Используйте мониторинг целостности файлов (Отслеживание изменений) и инвентаризации виртуальных машин в центре безопасности Azure для обнаружения всех программ, установленных на виртуальных машинах. Вы можете реализовать собственный процесс удаления неавторизованного программного обеспечения. Кроме того, можно использовать стороннее решение для обнаружения неутвержденного программного обеспечения.

- [Использование мониторинга целостности файлов](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring)

- [Общие сведения об Azure Отслеживание изменений](https://docs.microsoft.com/azure/automation/change-tracking)

- [Включение инвентаризации виртуальных машин Azure](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

## <a name="68-use-only-approved-applications"></a>6.8. Использование только утвержденных приложений

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 6,8 | 2.6 | Customer |

Используйте адаптивные элементы управления приложениями в центре безопасности Azure, чтобы гарантировать выполнение только авторизованного программного обеспечения и блокирование выполнения всех несанкционированных программ на виртуальных машинах Azure.

- [Как использовать адаптивные элементы управления приложениями в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="69-use-only-approved-azure-services"></a>6.9. Использование только утвержденных служб Azure

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 6.9 | 2.6 | Customer |

Используйте политику Azure, чтобы ограничить службы, которые можно подготавливать в вашей среде.

- [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Как отказаться от определенного типа ресурса с помощью Политики Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

## <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: ведение инвентаризации утвержденных наименований программного обеспечения

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 6.10 | 2.7 | Customer |

Используйте адаптивные элементы управления приложениями в центре безопасности Azure, чтобы указать, к каким типам файлов правилу может применяться правило.

Реализуйте сторонние решения, если это не соответствует требованиям.

- [Как использовать адаптивные элементы управления приложениями в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: ограничьте возможность пользователей работать с Azure Resource Manager

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 6.11 | 2.9 | Customer |

Используйте условный доступ Azure, чтобы ограничить возможность пользователей взаимодействовать с диспетчером ресурсов Azure, настроив "блокировать доступ" для приложения "Microsoft Azure Management".

- [Настройка условного доступа для блокировки доступа к диспетчеру ресурсов Azure](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12. Ограничение возможности пользователей выполнять сценарии в ресурсах вычислений

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 6,12 | 2.9 | Customer |

В зависимости от типа сценариев вы можете использовать конфигурации операционной системы или сторонние ресурсы, чтобы ограничить возможность пользователей выполнять сценарии в ресурсах вычислений Azure.  Можно также использовать адаптивные элементы управления приложениями центра безопасности Azure, чтобы гарантировать выполнение только авторизованного программного обеспечения и блокирование выполнения всех несанкционированных программ на виртуальных машинах Azure.

- [Управление выполнением скриптов PowerShell в средах Windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

- [Как использовать адаптивные элементы управления приложениями в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13. Физическое или логическое разделение приложений с высоким риском

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 6,13 | 2.9 | Customer |

Программное обеспечение, необходимое для бизнес-операций, но которое может повысить риск для Организации, должно быть изолировано в собственной виртуальной машине и (или) виртуальной сети, а также быть достаточно защищено с помощью брандмауэра Azure или группы безопасности сети.

- [Создание виртуальной сети](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Создание NSG с конфигурацией безопасности](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)


## <a name="next-steps"></a>Дальнейшие действия

- См. следующий контроль безопасности: [Безопасная конфигурация](security-control-secure-configuration.md)