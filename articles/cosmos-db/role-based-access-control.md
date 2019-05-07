---
title: Элемент управления доступом на основе ролей в Azure Cosmos DB с помощью интеграции Azure Active Directory
description: Узнайте, как Azure Cosmos DB обеспечивает защиту базы данных с интеграцией Active directory (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: e43341e59e807fdc20033d909beadb9d582149b5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078945"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Управление доступом на основе ролей в Azure Cosmos DB

Azure Cosmos DB предоставляет управления встроенные доступа на основе ролей (RBAC) для распространенных сценариев управления в Azure Cosmos DB. Лицо, которое содержит профиль в Azure Active Directory можно назначить эти роли RBAC для пользователей, групп, субъекты-службы или управляемые удостоверения, чтобы предоставить или запретить доступ к ресурсам и операциям с ресурсами Azure Cosmos DB. Назначения ролей относятся к плоскости управления только доступ, который включает доступ к учетным записям Azure Cosmos, баз данных, контейнеры и предлагает (пропускная способность).

## <a name="built-in-roles"></a>Встроенные роли

Ниже приведены встроенные роли, включенные в Azure Cosmos DB.

|**Встроенная роль**  |**Описание**  |
|---------|---------|
|[Участник учетных записей DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)   | Может управлять учетными записями Azure Cosmos DB  |
|[Модуль чтения для учетной записи Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)  | Позволяет считывать данные учетных записей Azure Cosmos DB.        |
|[Оператор архивации Cosmos](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)     |  Можно отправить запрос на восстановление для базы данных Azure Cosmos или контейнера.       |
|[Оператор Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-operator)  | Можно подготовить учетные записи Azure Cosmos, баз данных и контейнеров, но не доступ к ключам, которые требуются для доступа к данным.         |

> [!IMPORTANT]
> Поддержка RBAC в Azure Cosmos DB действует для только операции уровня управления. Выполнение операций плоскости данных обеспечивается с помощью главных ключей или маркеры ресурсов. Дополнительные сведения см. в разделе [безопасный доступ к данным в Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Система управления идентификацией и доступом (IAM)

**Управление доступом (IAM)** панели на портале Azure можно настроить управление доступом на основе ролей к ресурсам Azure Cosmos. Роли применяются к пользователям, групп, субъектов-служб и управляемые удостоверения в Active Directory. Можно использовать встроенные роли или пользовательские роли для отдельных пользователей и групп. На следующем снимке экрана показан интеграции Active Directory (RBAC) с помощью управления доступом (IAM) на портале Azure:

![Управление доступом (IAM) на портале Azure: демонстрация безопасности базы данных](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Пользовательские роли

Помимо встроенных ролей, пользователи также могут создавать [пользовательские роли](../role-based-access-control/custom-roles.md) в Azure и применить эти роли для субъекта-службы для всех подписок в клиенте Active Directory. Пользовательские роли позволяют пользователям создавать определения ролей RBAC с помощью пользовательского набора ресурсов операции с поставщиками. Чтобы узнать, какие операции доступны для создания пользовательских ролей в Azure Cosmos DB см. в разделе, [операции поставщиков ресурсов Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="next-steps"></a>Дальнейшие действия

- [Что такое управление доступом на основе ролей (RBAC) для ресурсов Azure](../role-based-access-control/overview.md)
- [Пользовательские роли для ресурсов Azure](../role-based-access-control/custom-roles.md)
- [Операции поставщиков ресурсов Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
