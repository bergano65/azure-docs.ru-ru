---
title: Разрешения в Azure Sentinel | Документация Майкрософт
description: В этой статье объясняется, как Azure Sentinel использует управление доступом на основе ролей для назначения разрешений пользователям и определяет разрешенные действия для каждой роли.
services: sentinel
cloud: na
documentationcenter: na
author: rkarlin
manager: angrobe
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: rkarlin
ms.openlocfilehash: 0479eba13fd747ea1bcab2672fce1df2156802e8
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74947592"
---
# <a name="permissions-in-azure-sentinel"></a>Разрешения в Azure Sentinel

В Azure Sentinel используется [Управление доступом на основе ролей (RBAC)](../role-based-access-control/role-assignments-portal.md)для предоставления [встроенных ролей](../role-based-access-control/built-in-roles.md) , которые могут быть назначены пользователям, группам и службам в Azure.

С помощью RBAC вы можете использовать и создавать роли в группе операций безопасности, чтобы предоставить соответствующий доступ к Azure Sentinel. С учетом ролей вы можете точно контролировать, какие пользователи могут видеть доступ к Azure Sentinel. Роли RBAC можно назначать в рабочей области Sentinel Azure напрямую или в подписке или группе ресурсов, к которой принадлежит Рабочая область.

Существует три определенные встроенные роли-Sentinel Azure.  
**Все встроенные роли Azure Sentinel предоставляют доступ на чтение данных в рабочей области Sentinel Azure.**
- [Читатель маркеров Azure](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Ответчик Sentinel Azure](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Участник Sentinel Azure](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Помимо ролей RBAC, выделенных на основе маркеров Azure, существуют роли Azure и Log Analytics RBAC, которые могут предоставлять более широкий набор разрешений, включая доступ к рабочей области Azure Sentinel и другим ресурсам:

- **Роли Azure:** [владелец](../role-based-access-control/built-in-roles.md#owner), [участник](../role-based-access-control/built-in-roles.md#contributor)и [читатель](../role-based-access-control/built-in-roles.md#reader). Роли Azure предоставляют доступ ко всем ресурсам Azure, включая рабочие области Log Analytics и Azure Sentinel Resources.

-   **Роли log Analytics:** [участник log Analytics](../role-based-access-control/built-in-roles.md#log-analytics-contributor), [модуль чтения log Analytics](../role-based-access-control/built-in-roles.md#log-analytics-reader). Роли Log Analytics предоставляют доступ для всех рабочих областей Log Analytics. 

> [!NOTE]
> Log Analytics роли также предоставляют доступ на чтение для всех ресурсов Azure, но назначает разрешения на запись только для Log Analytics ресурсов.


Например, пользователь, которому назначена роль Azure **Sentinel Reader** и **участник Azure** (не **корреспондентский участник Azure**), сможет изменять данные в Azure Sentinel, хотя у них есть только разрешение на **Чтение** меток. Поэтому, если вы хотите предоставить разрешения только в Azure Sentinel, следует тщательно удалить предыдущие разрешения этого пользователя, убедившись, что вы не нарушаете все необходимые роли разрешений для другого ресурса.

> [!NOTE]
>- Для автоматического реагирования на угрозу Azure Sentinel использует модули PlayBook. Модули PlayBook используют Azure Logic Apps и являются отдельным ресурсом Azure. Вам может потребоваться назначить определенные члены группы операций безопасности с возможностью использования Logic Apps для операций оркестрации, автоматизации и реагирования (ВЗЛЕТЕЛ). Вы можете использовать роль [участника приложения логики](../role-based-access-control/built-in-roles.md#logic-app-contributor) или роль [оператора приложения логики](../role-based-access-control/built-in-roles.md#logic-app-operator) , чтобы назначить явное разрешение для использования модули PlayBook.
>- Чтобы добавить соединители данных, необходимые роли для каждого соединителя относятся к типу каждого соединителя и перечислены на соответствующей странице соединителя. Кроме того, чтобы подключить любой источник данных, необходимо иметь разрешение на запись в рабочей области "Sentinel" Azure.



## <a name="roles-and-allowed-actions"></a>Роли и разрешенные действия

В следующей таблице показаны роли и разрешенные действия в Azure Sentinel. Значок "X" указывает, что данное действие разрешено для роли.

| Роль | Создание и запуск модули PlayBook| Создание и изменение панелей мониторинга, аналитических правил и других ресурсов Sentinel Azure | Управление инцидентами (отклонение, назначение и т. д.) | Просмотр данных, инцидентов, панелей мониторинга и других ресурсов Sentinel Azure |
|--- |---|---|---|---|
| Читатель маркеров Azure | -- | -- | -- | X |
| Ответчик Sentinel Azure|--|--| X | X |
| Участник Sentinel Azure | -- | X | X | X |
| Участник Sentinel Azure и участник приложения логики | X | X | X | X |


> [!NOTE]
> - Рекомендуется назначить пользователям роли с минимальными разрешениями, необходимыми для выполнения их задач. Например, назначьте роль участника Azure Sentinel только пользователям, которые должны создавать правила или панели мониторинга.
> - Рекомендуется задать разрешения для Sentinel в области группы ресурсов, чтобы пользователь мог иметь доступ ко всем рабочим областям Azure Sentinel в той же группе ресурсов.
>
## <a name="building-custom-rbac-roles"></a>Создание пользовательских ролей RBAC

В дополнение к или вместо, используя встроенные роли RBAC, можно создать пользовательские роли RBAC для Sentinel Azure. Пользовательские роли RBAC для Sentinel Azure создаются так же, как и другие [пользовательские роли RBAC Azure](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) , на основе [конкретных разрешений для Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) и [ресурсов Azure log Analytics](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights).

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>Расширенный RBAC для данных, хранящихся в Azure Sentinel
  
Вы можете использовать Log Analytics расширенный контроль доступа на основе ролей для данных в рабочей области Sentinel Azure. Сюда входят Управление доступом на основе ролей для каждого типа данных и управление доступом на основе ролей. Дополнительные сведения о ролях Log Analytics см. [в статье Управление данными и рабочими областями в Azure Monitor](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions).

## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как работать с ролями в Azure Sentinel Users и какую роль разрешает выполнять пользователи.

* [Блог Sentinel Azure](https://aka.ms/azuresentinelblog). Записи блога, посвященные безопасности и соответствию требованиям в Azure.
