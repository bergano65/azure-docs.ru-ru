---
title: Управление безопасностью Azure — управление запасами и активами
description: Управление инвентаризацией и контролем активов
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 95c1834e997caae13e650df5b588df898a1e361d
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564183"
---
# <a name="security-control-inventory-and-asset-management"></a>Управление безопасностью: Инвентаризация и управление активами

Рекомендации по управлению запасами и активами позволяют сосредоточиться на проблемах, связанных с активностью управления (инвентаризацией, трассировкой и исправлением) всех ресурсов Azure, чтобы предоставить доступ только авторизованным ресурсам, а неавторизованные и неуправляемые ресурсы определено и удалено.

## <a name="61-use-azure-asset-discovery"></a>6,1. Использование обнаружения ресурсов Azure

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 6.1 | 1,1, 1,2, 1,3, 1,4, 9,1, 12,1 | Customer |

Используйте граф ресурсов Azure для запроса или обнаружения всех ресурсов (например, вычислений, хранилища, сети, портов и протоколов и т. д.) в ваших подписках.  Обеспечьте соответствующие разрешения (чтение) в клиенте и перечислите все подписки Azure, а также ресурсы в ваших подписках.

Хотя классические ресурсы Azure могут быть обнаружены с помощью графа ресурсов, настоятельно рекомендуется создавать и использовать Azure Resource Manager ресурсы, идущие вперед.

Как создавать запросы с помощью графа ресурсов Azure:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Как просмотреть подписки Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Основные сведения об Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

## <a name="62-maintain-asset-metadata"></a>6,2: сохранение метаданных активов

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 6.2 | 1.5 | Customer |

Примените теги к ресурсам Azure, предоставив метаданные для логической организации их в таксономию.

Создание и использование тегов:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="63-delete-unauthorized-azure-resources"></a>6,3: удалите неавторизованные ресурсы Azure

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 6.3 | 1.6 | Customer |

Используйте теги, группы управления и отдельные подписки, если это необходимо, для Организации и мониторинга ресурсов. Регулярно выверка инвентаризации и своевременное удаление неавторизованных ресурсов из подписки.

Как создать дополнительные подписки Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Создание Группы управления:

https://docs.microsoft.com/azure/governance/management-groups/create

Создание и использование тегов:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: ведение инвентаризации утвержденных ресурсов Azure и наименований программного обеспечения

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 6.4 | 2.1 | Customer |

Определите утвержденные ресурсы Azure и утвержденное программное обеспечение для ресурсов вычислений.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: отслеживание неутвержденных ресурсов Azure

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 6,5 | 2.3, 2.4 | Customer |

Используйте политику Azure, чтобы ограничить тип ресурсов, которые могут быть созданы в ваших подписках.

Используйте граф ресурсов Azure для запроса или обнаружения ресурсов в своих подписках. &nbsp;убедитесь, что все ресурсы Azure, представленные в окружении, утверждены.

Как настроить политику Azure и управлять ей:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Как создавать запросы с помощью Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: отслеживание неутвержденных программных приложений в рамках ресурсов вычислений

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 6.6 | 2.3/2.4 | Customer |

Используйте инвентаризацию виртуальных машин Azure, чтобы автоматизировать сбор сведений обо всех программах на виртуальных машинах. Имя программного обеспечения, версия, издатель и время обновления доступны на портал Azure. Чтобы получить доступ к дате установки и другим сведениям, включите диагностику на уровне гостя и перенесите журналы событий Windows в рабочую область Log Analytics.

Как включить инвентаризацию виртуальных машин Azure:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: удаление неутвержденных ресурсов Azure и программных приложений

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 6.7 | 2.5 | Customer |

Используйте мониторинг целостности файлов (Отслеживание изменений) и инвентаризации виртуальных машин в центре безопасности Azure для обнаружения всех программ, установленных на виртуальных машинах. Вы можете реализовать собственный процесс удаления неавторизованного программного обеспечения. Кроме того, можно использовать стороннее решение для обнаружения неутвержденного программного обеспечения.

Как использовать мониторинг целостности файлов:

https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring

Общие сведения о Отслеживание изменений Azure:

https://docs.microsoft.com/azure/automation/change-tracking

Как включить инвентаризацию виртуальных машин Azure:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="68-use-only-approved-applications"></a>6,8: Используйте только утвержденные приложения

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 6,8 | 2.6 | Customer |

Используйте адаптивные элементы управления приложениями в центре безопасности Azure, чтобы гарантировать выполнение только авторизованного программного обеспечения и блокирование выполнения всех несанкционированных программ на виртуальных машинах Azure.

Как использовать адаптивные элементы управления приложениями в центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="69-use-only-approved-azure-services"></a>6,9: Используйте только утвержденные службы Azure

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 6.9 | 2.6 | Customer |

Используйте политику Azure, чтобы ограничить службы, которые можно подготавливать в вашей среде.

Как настроить политику Azure и управлять ей:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Как отказаться от определенного типа ресурса с помощью политики Azure:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

## <a name="610-implement-approved-application-list"></a>6,10: реализация списка утвержденных приложений

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 6.1 | 2.7 | Customer |

Используйте адаптивные элементы управления приложениями в центре безопасности Azure, чтобы указать, к каким типам файлов правилу может применяться правило.

Реализуйте сторонние решения, если это не соответствует требованиям.

Как использовать адаптивные элементы управления приложениями в центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6,11: ограничьте возможность пользователей взаимодействовать с Azure Resource Manager с помощью скриптов

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 6.11 | 2.8 | Customer |

Используйте условный доступ Azure, чтобы ограничить возможность пользователей взаимодействовать с Azure Resource Manager путем настройки&quot; &quot;ного доступа для приложения Microsoft Azure&quot; управления &quot;.

Как настроить условный доступ для блокировки доступа к Azure Resource Manager:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: ограничьте возможность пользователей выполнять сценарии в ресурсах вычислений

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 6,12 | 2.8 | Customer |

Используйте конфигурации операционной системы или сторонние ресурсы, чтобы ограничить возможность пользователей выполнять сценарии в ресурсах вычислений Azure.

Например, управление выполнением скриптов PowerShell в средах Windows: https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: физическое или логическое разделение приложений с высоким риском

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 6,13 | 2,9 | Customer |

Программное обеспечение, необходимое для бизнес-операций, но которое может повысить риск для Организации, должно быть изолировано в собственной виртуальной машине и (или) виртуальной сети, а также быть достаточно защищено с помощью брандмауэра Azure или группы безопасности сети.

Создание виртуальной сети.

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Создание NSG с конфигурацией безопасности:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="next-steps"></a>Дальнейшие действия

См. следующий контроль безопасности: [Безопасная конфигурация](security-control-secure-configuration.md)
