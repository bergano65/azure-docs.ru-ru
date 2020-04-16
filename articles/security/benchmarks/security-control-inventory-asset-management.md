---
title: Контроль безопасности Azure - Инвентаризация и управление активами
description: Инвентаризация и управление активами Azure security Control и управление активами
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: aece7278d134145e00b027c184f3817d5566e1e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408358"
---
# <a name="security-control-inventory-and-asset-management"></a>Контроль безопасности: инвентаризация и управление активами

Рекомендации по управлению запасами и активами сосредоточены на решении вопросов, связанных с активным управлением (инвентаризация, отслеживание и исправление) всех ресурсов Azure, чтобы получить доступ и удалить только авторизованные ресурсы, а несанкционированные и неуправляемые ресурсы были выявлены и удалены.

## <a name="61-use-automated-asset-discovery-solution"></a>6.1: Использование автоматизированного решения для обнаружения активов

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6.1 | 1.1, 1.2, 1.3, 1.4, 9.1, 12.1 | Customer |

Используйте график ресурсов Azure для запроса/обнаружения всех ресурсов (таких как вычисления, хранение, сеть, порты и протоколы и т.д.) в подписке(ы).  Убедитесь в подходящих (читай) разрешениях в арендаторе и перечислите все подписки Azure, а также ресурсы в подписках.

Хотя классические ресурсы Azure могут быть обнаружены с помощью диаграммы ресурсов, настоятельно рекомендуется создавать и использовать ресурсы Azure Resource Manager в будущем.

- [Как создавать запросы с помощью графика ресурсов Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Как просмотреть подписку на Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Понять Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="62-maintain-asset-metadata"></a>6.2: Поддержание метаданных активов

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6.2 | 1.5 | Customer |

Примените теги к ресурсам Azure, дающим метаданные, чтобы логически организовать их в таксономию.

- [Как создавать и использовать теги](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="63-delete-unauthorized-azure-resources"></a>6.3: Удалить несанкционированные ресурсы Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6.3 | 1.6 | Customer |

Используйте пометки, группы управления и отдельные подписки, где это уместно, для организации и отслеживания активов. Регулярно согласовывать инвентаризацию и своевременно исключать несанкционированные ресурсы из подписки.

- [Как создать дополнительные подписки Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Как создать группы управления](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Как создавать и использовать теги](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Определить и вести инвентаризацию утвержденных ресурсов Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6.4 | 2.1 | Customer |

Создание перечня утвержденных ресурсов Azure и утвержденного программного обеспечения для вычислений ресурсов в соответствии с нашими организационными потребностями.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Мониторинг неодобренных ресурсов Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6,5 | 2.3, 2.4 | Customer |

Используйте политику Azure, чтобы наложить ограничения на тип ресурсов, которые могут быть созданы в вашей подписке( s.

Используйте график ресурсов Azure для запроса/обнаружения ресурсов в рамках подписки(ы).  Убедитесь, что все ресурсы Azure, присутствующие в среде, утверждены.

- [Как настроить и управлять политикой Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Как создавать запросы с помощью Графика Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Мониторинг неодобренных программных приложений в вычислительных ресурсах

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6.6 | 2.3, 2.4 | Customer |

Используйте виртуальную машину Azure Inventory для автоматизации сбора информации обо всех программах на виртуальных машинах. Название программного обеспечения, версия, издатель створен и обновление доступно на портале Azure. Чтобы получить доступ к дате установки и другой информации, включите диагностику на уровне гостей и включите журналы событий Windows в рабочее пространство для анализа журналов.

- [Как включить инвентаризацию виртуальной машины Azure](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Удалить неодобренные ресурсы и программные приложения Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6.7 | 2.5 | Customer |

Используйте мониторинг целостности файлов Центра безопасности Azure (отслеживание изменений) и виртуальный инвентарь машин для идентификации всего программного обеспечения, установленного на виртуальных машинах. Вы можете реализовать свой собственный процесс для удаления несанкционированного программного обеспечения. Вы также можете использовать стороннее решение для выявления неодобренного программного обеспечения.

- [Как использовать мониторинг целостности файлов](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring)

- [Понимание отслеживания изменений Azure](https://docs.microsoft.com/azure/automation/change-tracking)

- [Как включить инвентаризацию виртуальных машин Azure](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

## <a name="68-use-only-approved-applications"></a>6.8: Используйте только одобренные приложения

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6,8 | 2.6 | Customer |

Используйте адаптивные средства Azure Security Center, чтобы гарантировать, что только авторизованное программное обеспечение выполняется, а все несанкционированное программное обеспечение блокируется от выполнения на виртуальных машинах Azure.

- [Как использовать адаптивные средства управления адаптивной помощью Центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="69-use-only-approved-azure-services"></a>6.9: Используйте только утвержденные службы Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6.9 | 2.6 | Customer |

Используйте политику Azure, чтобы ограничить, какие службы можно предоставлять в своей среде.

- [Как настроить и управлять политикой Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Как отказать в определенном типе ресурсов с помощью политики Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

## <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Ведение перечня утвержденных названий программного обеспечения

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6.10 | 2.7 | Customer |

Используйте адаптивные элементы управления адаптивного приложения Azure Security Center, чтобы указать, к каким типам файлов правило может или не может применяться.

Реализация решения третьей стороны, если это не соответствует требованию.

- [Как использовать адаптивные средства управления адаптивной помощью Центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Ограничьте возможности пользователей взаимодействовать с менеджером ресурсов Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6.11 | 2,9 | Customer |

Используйте Azure Conditional Access, чтобы ограничить возможности пользователей взаимодействовать с менеджером ресурсов Azure, нанастройки "Блок-доступ" для приложения "Microsoft Azure Management".

- [Как настроить условный доступ для блокирования доступа к менеджеру ресурсов Azure](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Ограничьте возможности пользователей по выполнению скриптов в вычислительных ресурсах

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6.12 | 2,9 | Customer |

В зависимости от типа скриптов можно использовать определенные конфигурации операционной системы или сторонние ресурсы, чтобы ограничить возможности пользователей выполнять скрипты в ресурсах Azure.  Вы также можете использовать элементы управления адаптивной приложениями Azure Security Center, чтобы гарантировать, что только авторизованное программное обеспечение выполняется, а все несанкционированное программное обеспечение блокируется от выполнения на виртуальных машинах Azure.

- [Как контролировать выполнение скриптов PowerShell в средах Windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

- [Как использовать адаптивные средства управления адаптивной помощью Центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Физически или логически сегрегировать приложения высокого риска

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 6.13 | 2,9 | Customer |

Программное обеспечение, необходимое для бизнес-операций, но может понести более высокий риск для организации, должно быть изолировано в рамках собственной виртуальной машины и/или виртуальной сети и достаточно защищено либо Брандмауэром Azure, либо группой сетевой безопасности.

- [Как создать виртуальную сеть](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Как создать NSG с конфигурацией безопасности](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)


## <a name="next-steps"></a>Дальнейшие действия

- Смотрите следующий элемент управления безопасностью: [Безопасная конфигурация](security-control-secure-configuration.md)