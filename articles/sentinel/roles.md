---
title: Разрешения в Azure Sentinel Документы Майкрософт
description: В этой статье объясняется, как Azure Sentinel использует элементы управления доступом на основе ролей для присвоения разрешений пользователям и определяет допустимые действия для каждой роли.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: angrobe
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: yelevin
ms.openlocfilehash: e7629a53190433c6c331ce372476b0ed768fc5eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587181"
---
# <a name="permissions-in-azure-sentinel"></a>Разрешения в Azure Sentinel

Azure Sentinel использует [элементуправления доступа на основе ролей (RBAC)](../role-based-access-control/role-assignments-portal.md)для предоставления [встроенных ролей,](../role-based-access-control/built-in-roles.md) которые могут быть назначены пользователям, группам и службам в Azure.

Используя RBAC, можно использовать и создавать роли в группе операций безопасности, чтобы предоставить соответствующий доступ к Azure Sentinel. Основываясь на ролях, вы можете получить тонкий контроль над тем, что могут видеть пользователи, имея доступ к Azure Sentinel. Функции RBAC можно назначать непосредственно в рабочей области Azure Sentinel или в группу подписки или ресурсов, к которой принадлежит рабочее пространство.

Есть три конкретные встроенные роли Azure Sentinel.  
**Все встроенные роли Azure Sentinel предоставляют доступ к данным в рабочем пространстве Azure Sentinel.**
- [Читатель Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Ответчик Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Вкладчик Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Помимо ролей ВАК, посвященных RBAC, есть роли Azure и Log Analytics RBAC, которые могут предоставить более широкий набор разрешений, включающий доступ к рабочему пространству Azure Sentinel и другим ресурсам:

- **Роли Azure:** [Владелец,](../role-based-access-control/built-in-roles.md#owner) [Автор](../role-based-access-control/built-in-roles.md#contributor)и [Читатель](../role-based-access-control/built-in-roles.md#reader). Роли Azure предоставляют доступ ко всем ресурсам Azure, включая рабочие пространства журналов Analytics и ресурсы Azure Sentinel.

-   **Роли аналитики журналов:** [Вклад в аналитику журналов,](../role-based-access-control/built-in-roles.md#log-analytics-contributor)читатель журнала [Analytics](../role-based-access-control/built-in-roles.md#log-analytics-reader). Роли Log Analytics предоставляют доступ во все рабочие области log Analytics. 

> [!NOTE]
> Роли журнала Analytics также предоставляют доступ к считыванию во всех ресурсах Azure, но будут присваивать разрешения на запись ресурсам Log Analytics.


Например, пользователь, которому присвоены функции **считывателяAz Sentinel** и **ванимателя Azure** (не **в автором Azure Sentinel) (не в автором Асуциера),** сможет отсечь данные в Azure Sentinel, хотя у него есть только разрешения **для чтения Sentinel.** Поэтому, если вы хотите предоставить разрешения только в Azure Sentinel, следует тщательно удалить предыдущие разрешения этого пользователя, чтобы убедиться, что вы не нарушаете нужную роль разрешения для другого ресурса.

> [!NOTE]
>- Azure Sentinel использует игровые книги для автоматического реагирования на угрозы. Playbooks использует приложения Azure Logic Apps и является отдельным ресурсом Azure. Возможно, необходимо назначить конкретных членов вашей группы операций безопасности с возможностью использования logic Apps для согласования, автоматизации и реагирования (SOAR). Вы можете использовать роль [вкладчика Logic App](../role-based-access-control/built-in-roles.md#logic-app-contributor) или роль [оператора Logic App](../role-based-access-control/built-in-roles.md#logic-app-operator) для присвоения явного разрешения на использование игровых книг.
>- Для добавления разъемов данных необходимые роли для каждого разъема указаны на тип разъема и перечислены на соответствующей странице разъема. Кроме того, для подключения любого источника данных необходимо иметь разрешение на запись в рабочем пространстве Azure Sentinel.



## <a name="roles-and-allowed-actions"></a>Роли и разрешенные действия

В следующей таблице отображаются роли и разрешенные действия в Azure Sentinel. Значок "X" указывает, что данное действие разрешено для роли.

| Роль | Создание и запуск игровых книг| Создание и отодевание панелей мониторинга, аналитических правил и других ресурсов Azure Sentinel | Управление инцидентами (увольнение, присвоение и т.д.) | Просмотр данных, инцидентов, панелей мониторинга и других ресурсов Azure Sentinel |
|--- |---|---|---|---|
| Читатель Azure Sentinel | -- | -- | -- | X |
| Ответчик Azure Sentinel|--|--| X | X |
| Вкладчик Azure Sentinel | -- | X | X | X |
| Вкладчик Azure Sentinel - Logic App | X | X | X | X |


> [!NOTE]
> - Рекомендуется назначить пользователям роли с минимальными разрешениями, необходимыми для выполнения их задач. Например, присваивайте роль вкладчика Azure Sentinel только пользователям, которым необходимо создавать правила или панели мониторинга.
> - Мы рекомендуем установить разрешения для Azure Sentinel в области группы ресурсов, чтобы пользователь мог иметь доступ ко всем рабочим местам Azure Sentinel в одной и той же группе ресурсов.
>
## <a name="building-custom-rbac-roles"></a>Создание пользовательских ролей RBAC

В дополнение к встроенным ролям RBAC можно создавать пользовательские роли RBAC для Azure Sentinel или вместо него. Пользовательские роли RBAC для Azure Sentinel создаются так же, как и другие пользовательские роли [Azure RBAC,](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) основанные на [конкретных разрешениях на Ресурсы Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) и [Ресурсы Analytics журналов Azure.](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>Расширенный доступ RBAC для данных, хранящихся в Azure Sentinel
  
Вы можете использовать расширенный элемент управления доступом на основе ролей Log Analytics в рабочей области Azure Sentinel. Это включает в себя как ролевый контроль доступа на тип данных, так и ресурсо-ориентированный ролевой контроль доступа. Для получения дополнительной информации о ролях log Analytics [см. Управление данными журнала и рабочими областями в Azure Monitor.](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions)

## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как работать с ролями для пользователей Azure Sentinel и что каждая роль позволяет пользователям делать.

* [Лазурный Sentinel блог](https://aka.ms/azuresentinelblog). Записи блога, посвященные безопасности и соответствию требованиям в Azure.
