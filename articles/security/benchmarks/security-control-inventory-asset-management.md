---
title: Контроль безопасности Azure - Инвентаризация и управление активами
description: Инвентаризация безопасности и управление активами
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 2f24012a69a7673426644a8ded6162cd1021938e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75930061"
---
# <a name="security-control-inventory-and-asset-management"></a>Контроль безопасности: инвентаризация и управление активами

Рекомендации по инвентаризации и управлению активами сосредоточены на решении вопросов, связанных с активным управлением (инвентаризация, отслеживание и исправление) всех ресурсов Azure, чтобы получить доступ только авторизованным ресурсам, а несанкционированные и неуправляемые ресурсы определены и удалены.

## <a name="61-use-azure-asset-discovery"></a>6.1: Используйте обнаружение активов Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6.1 | 1.1, 1.2, 1.3, 1.4, 9.1, 12.1 | Customer |

Используйте график ресурсов Azure для запроса/обнаружения всех ресурсов (таких как вычисления, хранение, сеть, порты и протоколы и т.д.) в подписке(ы).  Убедитесь в подходящих (читай) разрешениях в арендаторе и перечислите все подписки Azure, а также ресурсы в подписках.

Хотя классические ресурсы Azure могут быть обнаружены с помощью диаграммы ресурсов, настоятельно рекомендуется создавать и использовать ресурсы Azure Resource Manager в будущем.

Как создавать запросы с помощью графика ресурсов Azure:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Как просмотреть подписку на Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Поймите Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

## <a name="62-maintain-asset-metadata"></a>6.2: Поддержание метаданных активов

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6.2 | 1.5 | Customer |

Примените теги к ресурсам Azure, дающим метаданные, чтобы логически организовать их в таксономию.

Как создавать и использовать теги:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="63-delete-unauthorized-azure-resources"></a>6.3: Удалить несанкционированные ресурсы Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6.3 | 1.6 | Customer |

Используйте пометки, группы управления и отдельные подписки, где это уместно, для организации и отслеживания активов. Регулярно согласовывать инвентаризацию и своевременно исключать несанкционированные ресурсы из подписки.

Как создать дополнительные подписки Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Как создать группы управления:

https://docs.microsoft.com/azure/governance/management-groups/create

Как создавать и использовать теги:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Ведение перечня утвержденных ресурсов Azure и названий программного обеспечения

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6.4 | 2.1 | Customer |

Определите утвержденные ресурсы Azure и утвержденное программное обеспечение для вычислений ресурсов.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Мониторинг неодобренных ресурсов Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6,5 | 2.3, 2.4 | Customer |

Используйте политику Azure, чтобы наложить ограничения на тип ресурсов, которые могут быть созданы в вашей подписке( s.

Используйте график ресурсов Azure для запроса/обнаружения ресурсов в рамках подписки(ы). &nbsp;Убедитесь, что все ресурсы Azure, присутствующие в среде, утверждены.

Как настроить и управлять политикой Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Как создавать запросы с помощью Графика Azure:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Мониторинг неодобренных программных приложений в вычислительных ресурсах

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6.6 | 2.3/2.4 | Customer |

Используйте виртуальную машину Azure Inventory для автоматизации сбора информации обо всех программах на виртуальных машинах. Название программного обеспечения, версия, издатель створен и обновление доступно на портале Azure. Чтобы получить доступ к дате установки и другой информации, включите диагностику на уровне гостей и включите журналы событий Windows в рабочее пространство для анализа журналов.

Как включить инвентаризацию виртуальной машины Azure:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Удалить неодобренные ресурсы и программные приложения Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6.7 | 2.5 | Customer |

Используйте мониторинг целостности файлов Центра безопасности Azure (отслеживание изменений) и виртуальный инвентарь машин для идентификации всего программного обеспечения, установленного на виртуальных машинах. Вы можете реализовать свой собственный процесс для удаления несанкционированного программного обеспечения. Вы также можете использовать стороннее решение для выявления неодобренного программного обеспечения.

Как использовать мониторинг целостности файлов:

https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring

Понять отслеживание изменений Azure:

https://docs.microsoft.com/azure/automation/change-tracking

Как включить инвентаризацию виртуальных машин Azure:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="68-use-only-approved-applications"></a>6.8: Используйте только одобренные приложения

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6,8 | 2.6 | Customer |

Используйте адаптивные средства Azure Security Center, чтобы гарантировать, что только авторизованное программное обеспечение выполняется, а все несанкционированное программное обеспечение блокируется от выполнения на виртуальных машинах Azure.

Как использовать адаптивные средства Управления адаптивного приложения Центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="69-use-only-approved-azure-services"></a>6.9: Используйте только утвержденные службы Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6.9 | 2.6 | Customer |

Используйте политику Azure, чтобы ограничить, какие службы можно предоставлять в своей среде.

Как настроить и управлять политикой Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Как отказать в определенном типе ресурсов с помощью политики Azure:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

## <a name="610-implement-approved-application-list"></a>6.10: Реализация утвержденного списка заявок

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6.1 | 2.7 | Customer |

Используйте адаптивные элементы управления адаптивного приложения Azure Security Center, чтобы указать, к каким типам файлов правило может или не может применяться.

Реализация решения третьей стороны, если это не соответствует требованию.

Как использовать адаптивные средства Управления адаптивного приложения Центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6.11: Ограничьте возможности пользователей взаимодействовать с менеджером ресурсов Azure с помощью скриптов

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6.11 | 2.8 | Customer |

Используйте Azure Conditional Access, чтобы ограничить возможности пользователей &quot;взаимодействовать&quot; с &quot;менеджером&quot; ресурсов Azure, нанастройки доступа к блоку для приложения управления Microsoft Azure.

Как настроить условный доступ, чтобы заблокировать доступ к менеджеру ресурсов Azure:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Ограничьте возможности пользователей по выполнению скриптов в вычислительных ресурсах

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6.12 | 2.8 | Customer |

Используйте определенные конфигурации операционной системы или сторонние ресурсы, чтобы ограничить возможности пользователей выполнять скрипты в ресурсах Azure.

Например, как контролировать выполнение скриптов PowerShell в средах Windows:

https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Физически или логически сегрегировать приложения высокого риска

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6.13 | 2,9 | Customer |

Программное обеспечение, необходимое для бизнес-операций, но может понести более высокий риск для организации, должно быть изолировано в рамках собственной виртуальной машины и/или виртуальной сети и достаточно защищено либо Брандмауэром Azure, либо группой сетевой безопасности.

Как создать виртуальную сеть:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Как создать NSG с конфигурацией безопасности:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="next-steps"></a>Дальнейшие действия

Посмотреть следующий элемент управления безопасностью: [Безопасная конфигурация](security-control-secure-configuration.md)
