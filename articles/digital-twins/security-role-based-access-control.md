---
title: Общие сведения об управлении доступом на основе ролей в Azure Digital двойников | Документация Майкрософт
description: Узнайте о контроле доступа на основе ролей и управлении разрешениями в Azure Digital двойников.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: efa19cdd1dd0827fd0e88f533b94f69bd7b99ca9
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307211"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Управление доступом на основе ролей в Azure Digital Twins

Azure Digital двойников обеспечивает точный контроль доступа к конкретным данным, ресурсам и действиям на пространственном графе. Это делается с помощью детального управления ролями и разрешениями, называемой управлением [доступом на основе ролей](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC). RBAC состоит с _ролей_ и _назначений ролей_. Роли определяют уровень разрешений. Назначения ролей сопоставляет роль с пользователем или устройством.

Используя RBAC, разрешения может предоставляться:

- пользователю;
- устройству;
- субъект-службе;
- определяемой пользователем функции;
- всем пользователям, которые являются частью домена;
- клиенту.

Также можно настроить уровень доступа.

RBAC — уникальное, так как позволяет наследовать разрешения по пространственному графу.

## <a name="what-can-i-do-with-rbac"></a>Что можно сделать с помощью RBAC?

RBAC может использоваться разработчиком для целей, приведенных ниже.

- Предоставление пользователю возможности управления устройствами во всем здании, или только в определенной комнате, или на указанном этаже.
- Предоставление администратору глобального доступа ко всем узлам пространственного графа (всего графа или только определенного его раздела).
- Предоставление специалисту службы поддержки доступа на чтение к графу, за исключением ключей доступа.
- Предоставление всем участникам домена доступа на чтение для всех объектов графа.

## <a name="rbac-best-practices"></a>Рекомендации по использованию RBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Роли

### <a name="role-definitions"></a>Определения ролей

Определение роли — это коллекция разрешений и других атрибутов, которые составляют роль. Определение роли содержит список разрешенных операций, среди них: *СОЗДАНИЕ*, *ЧТЕНИЕ*, *ОБНОВЛЕНИЕ* и *УДАЛЕНИЕ*, которые может выполнять любой объект с этой ролью. Он также указывает, к каким типам объектов применяются разрешения.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> Чтобы получить полные определения для вышеупомянутых ролей, запросите системы или роли API.
> Дополнительные сведения см. в статье [Создание назначений ролей и управление ими в Azure Digital Twins](./security-create-manage-role-assignments.md#retrieve-all-roles).

### <a name="object-identifier-types"></a>Типы идентификатора объекта

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> Сведения о том, как предоставить разрешения для вашего субъекта-службы, см. в статье [Создание назначений ролей и управление ими в Azure Digital Twins](./security-create-manage-role-assignments.md#grant-permissions-to-your-service-principal).

В следующих статьях справочной документации описаны:

- Функции командлета [Get-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- Функции командлета [Get-AzureRmADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal).
- [Краткое руководство: настройка среды разработки](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="role-assignments"></a>Назначения ролей

Назначение ролей Digital Twins связывает объект (пользователя, клиента Azure Active Directory и т. д.) с ролью и пространством. Разрешения предоставляются всем объектам, которые принадлежат этой области. Пространство включает весь пространственный граф.

Например, пользователь получает назначение ролей с ролью `DeviceInstaller` для корневого узла пространственного графа, представляющего здание. После этого пользователь сможет считывать и обновлять устройства для этого узла и для всех других дочерних пространств в здании.

Чтобы предоставить разрешения для получателя, создайте назначения ролей. Чтобы отменить разрешения, удалите назначение ролей.

>[!IMPORTANT]
> Дополнительные сведения о назначении ролей см. в статье [Создание назначений ролей и управление ими в Azure Digital Twins](./security-create-manage-role-assignments.md).

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о создании и управлении назначениями ролей Azure Digital Twins см. в статье [Создание назначений ролей и управление ими в Azure Digital Twins](./security-create-manage-role-assignments.md).

- Подробнее о [RBAC для Azure](https://docs.microsoft.com/azure/role-based-access-control/).
