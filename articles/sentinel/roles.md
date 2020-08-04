---
title: Разрешения в Azure Sentinel | Документация Майкрософт
description: В этой статье объясняется, как Azure Sentinel использует управление доступом на основе ролей для назначения разрешений пользователям и определяет разрешенные действия для каждой роли.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2020
ms.author: yelevin
ms.openlocfilehash: b907fead145847d54b8b1de196586b65101b22ed
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534011"
---
# <a name="permissions-in-azure-sentinel"></a>Разрешения в Azure Sentinel

Azure Sentinel использует [Управление доступом на основе ролей (RBAC)](../role-based-access-control/role-assignments-portal.md) для предоставления [встроенных ролей](../role-based-access-control/built-in-roles.md)   , которые могут быть назначены пользователям, группам и службам в Azure.

Используйте RBAC для создания и назначения ролей в группе операций безопасности, чтобы предоставить соответствующий доступ к Azure Sentinel. Различные роли обеспечивают точный контроль над тем, что могут видеть пользователи Sentinel. Роли Azure можно назначать в рабочей области Azure Sentinel напрямую (см. Примечание ниже) или в подписке или группе ресурсов, к которой принадлежит Рабочая область, который будет наследоваться в Azure.

## <a name="roles-for-working-in-azure-sentinel"></a>Роли для работы в Azure Sentinel

### <a name="azure-sentinel-specific-roles"></a>Роли для конкретных маркеров Azure

Существует три выделенных встроенных ролей-маркеров Azure.

**Все встроенные роли Azure Sentinel предоставляют доступ на чтение данных в рабочей области Sentinel Azure.**

- [Читатель Sentinel Azure](../role-based-access-control/built-in-roles.md#azure-sentinel-reader) может просматривать данные, инциденты, книги и другие Azure Sentinel ресурсы.

- [Ответчик Sentinel Azure](../role-based-access-control/built-in-roles.md#azure-sentinel-responder) может, помимо прочего, управлять инцидентами (назначить, отклонить и т. д.).

- [Корреспондентский участник Azure](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor) может, помимо прочего, создавать и редактировать книги, правила аналитики и другие ресурсы Sentinel Azure.

> [!NOTE]
>
> - Для достижения лучших результатов эти роли должны быть назначены **группе ресурсов** , содержащей рабочую область "Sentinel" Azure. Таким образом, роли будут применяться ко всем ресурсам, развернутым для поддержки Sentinel Azure, так как эти ресурсы также должны размещаться в той же группе ресурсов.
>
> - Другой вариант — назначить роли непосредственно в **рабочей области** Azure Sentinel. В этом случае необходимо также назначить те же роли в **ресурсе решения** секуритинсигхтс в этой рабочей области. Вам может потребоваться назначить их другим ресурсам, и вам придется постоянно управлять назначениями ролей для ресурсов.

### <a name="additional-roles-and-permissions"></a>Дополнительные роли и разрешения

Пользователям с определенными требованиями к заданиям может потребоваться назначить дополнительные роли или определенные разрешения для выполнения своих задач.

- Работа с модули PlayBook для автоматизации реагирования на угрозы

    Для автоматического реагирования на угрозу Azure Sentinel использует **модули PlayBook** . Модули PlayBook построены на **Azure Logic Apps**и являются отдельным ресурсом Azure. Вам может потребоваться назначить определенные члены группы операций безопасности возможность использовать Logic Apps для операций оркестрации, автоматизации и реагирования (ВЗЛЕТЕЛ). Вы можете использовать роль [участника приложения логики](../role-based-access-control/built-in-roles.md#logic-app-contributor) или роль [оператора приложения логики](../role-based-access-control/built-in-roles.md#logic-app-operator) , чтобы назначить явное разрешение для использования модули PlayBook.

- Подключение источников данных к Sentinel Azure

    Чтобы добавить **соединители данных**для пользователя, необходимо назначить пользователю разрешения на запись в рабочей области "Sentinel" Azure. Кроме того, обратите внимание на необходимые дополнительные разрешения для каждого соединителя, перечисленные на соответствующей странице соединителя.

Параллельное сравнение см. в [таблице ниже](#roles-and-allowed-actions).

### <a name="other-roles-you-might-see-assigned"></a>Другие роли, которые могут быть назначены

При назначении ролей Azure, зависящих от Sentinel, вы можете использовать другие роли Azure и Log Analytics Azure, которые могли быть назначены пользователям в других целях. Следует иметь в виду, что эти роли предоставляют более широкий набор разрешений, включая доступ к рабочей области Azure Sentinel и другим ресурсам:

- **Роли Azure:** [владелец](../role-based-access-control/built-in-roles.md#owner), [участник](../role-based-access-control/built-in-roles.md#contributor)и [читатель](../role-based-access-control/built-in-roles.md#reader). Роли Azure предоставляют доступ ко всем ресурсам Azure, включая рабочие области Log Analytics и Azure Sentinel Resources.

- **Роли log Analytics:** [log Analytics участник](../role-based-access-control/built-in-roles.md#log-analytics-contributor) и [модуль чтения log Analytics](../role-based-access-control/built-in-roles.md#log-analytics-reader). Роли Log Analytics предоставляют доступ к рабочим областям Log Analytics. 

Например, пользователь, которому назначена роль " **читатель Azure Sentinel** ", но не роль " **корреспондентский участник Azure** ", по-прежнему сможет изменять элементы в Azure Sentinel, если ему назначена роль **участника** на уровне Azure. Поэтому, если вы хотите предоставить разрешения пользователю только в Azure Sentinel, следует тщательно удалить предыдущие разрешения этого пользователя, убедившись, что вы не нарушаете необходимый доступ к другому ресурсу.

## <a name="roles-and-allowed-actions"></a>Роли и разрешенные действия

В следующей таблице перечислены роли и разрешенные действия в Azure Sentinel. 

| Роль | Создание и запуск модули PlayBook| Создание и изменение книг, аналитических правил и других ресурсов Sentinel Azure | Управление инцидентами (отклонение, назначение и т. д.) | Просмотр данных, инцидентов, книг и других ресурсов Sentinel Azure |
|---|---|---|---|---|
| Читатель Azure Sentinel | -- | -- | -- | &#10003; |
| Респондент Azure Sentinel | -- | -- | &#10003; | &#10003; |
| Участник Azure Sentinel | -- | &#10003; | &#10003; | &#10003; |
| Участник Sentinel Azure и участник приложения логики | &#10003; | &#10003; | &#10003; | &#10003; |

## <a name="custom-roles-and-advanced-rbac"></a>Пользовательские роли и расширенный RBAC

- В дополнение к или вместо использования встроенных ролей Azure можно создать пользовательские роли Azure для Azure Sentinel. Пользовательские роли Azure для маркеров Azure создаются так же, как и другие [пользовательские роли RBAC Azure](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) , основанные на [конкретных разрешениях для Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) и [Azure log Analyticsных ресурсах](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights).

- Вы можете использовать Log Analytics расширенный контроль доступа на основе ролей для данных в рабочей области Sentinel Azure. Сюда входят и RBAC на основе типов данных, и RBAC, ориентированный на ресурсы. Дополнительные сведения о ролях Log Analytics см. [в статье Управление данными и рабочими областями в Azure Monitor](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions).

## <a name="next-steps"></a>Дальнейшие действия

В этом документе вы узнали, как работать с ролями в Azure Sentinel Users и какую роль разрешает выполнять пользователи.

* [Блог Sentinel Azure](https://aka.ms/azuresentinelblog). Записи блога, посвященные безопасности и соответствию требованиям в Azure.
