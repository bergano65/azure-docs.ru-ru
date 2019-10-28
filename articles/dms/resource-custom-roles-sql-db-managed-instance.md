---
title: Пользовательские роли для SQL Server для оперативной миграции управляемого экземпляра базы данных SQL | Документация Майкрософт
description: Узнайте, как использовать пользовательские роли для SQL Server для оперативной миграции управляемого экземпляра базы данных SQL.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/25/2019
ms.openlocfilehash: 8148e029bf343613a230b20d0397fa7851c96712
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952359"
---
# <a name="custom-roles-for-sql-server-to-sql-database-managed-instance-online-migrations"></a>Пользовательские роли для SQL Server для оперативной миграции управляемого экземпляра базы данных SQL

Azure Database Migration Service использует идентификатор приложения для взаимодействия со службами Azure. Для идентификатора приложения требуется роль участника на уровне подписки (которая не разрешает множество корпоративных отделов безопасности) или создание пользовательских ролей, предоставляющих определенные разрешения, которые требуются для службы миграции баз данных Azure. Так как в Azure Active Directory существует ограничение в 2 000 пользовательских ролей, может потребоваться объединить все разрешения, необходимые для идентификатора приложения, в одну или две настраиваемые роли, а затем предоставить ИДЕНТИФИКАТОРу приложения настраиваемую роль для конкретных объектов или групп ресурсов (VS). уровень сание). Если число пользовательских ролей не имеет значения, можно разделить пользовательские роли по типу ресурса, чтобы создать три пользовательские роли в целом, как описано ниже.

Раздел AssignableScopes строки определения роли JSON позволяет управлять отображением разрешений в пользовательском интерфейсе **назначения ролей** на портале. Скорее всего, вы захотите определить роль в группе ресурсов или даже на уровне ресурсов, чтобы избежать загромождения пользовательского интерфейса дополнительными ролями. Обратите внимание, что это не выполняет фактическое назначение ролей.

## <a name="minimum-number-of-roles"></a>Минимальное число ролей

В настоящее время рекомендуется создать как минимум две пользовательские роли для идентификатора приложения: по одному на уровне ресурсов, а другое — на уровне подписки.

> [!NOTE]
> Последнее требование пользовательской роли может быть удалено по мере развертывания нового кода управляемого экземпляра базы данных SQL в Azure.

**Настраиваемая роль для идентификатора приложения**. Эта роль необходима для Azure Database Migration Service миграции на уровне *ресурсов* или *групп ресурсов* (Дополнительные сведения об идентификаторе приложения см. в статье [Создание приложения Azure AD и субъекта-службы с помощью портала). может получить доступ к ресурсам](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)).

```json
{
  "Name": "DMS Role - App ID",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read",
        "Microsoft.DataMigration/locations/*",
        "Microsoft.DataMigration/services/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/ResourceGroups/<StorageAccount_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<ManagedInstance_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<DMS_rg_name>",
  ]
}
```

**Настраиваемая роль для идентификатора приложения — подписка**. Эта роль необходима для Azure Database Migration Service миграции на уровне *подписки* .

```json
{
  "Name": "DMS Role - App ID - Sub",
  "IsCustom": true,
  "Description": "DMS App ID access at subscription level to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>"
  ]
}
```

Приведенный выше код JSON должен храниться в трех текстовых файлах. для создания ролей можно использовать командлеты AzureRM, AZ PowerShell или Azure CLI, чтобы создать роли с помощью **New-AzureRmRoleDefinition (AzureRM)** или **New-азроледефинитион (AZ)** .

Дополнительные сведения см. в статье [пользовательские роли для ресурсов Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

После создания этих пользовательских ролей необходимо добавить назначения ролей для пользователей и ИДЕНТИФИКАТОРов приложений в соответствующие ресурсы или группы ресурсов:

* Роль "DMS Role-App ID" должна быть предоставлена ИДЕНТИФИКАТОРу приложения, который будет использоваться для миграции, а также в учетной записи хранения, Azure Database Migration Service экземпляре и уровнях ресурсов управляемого экземпляра базы данных SQL.
* ИДЕНТИФИКАТОРу приложения на уровне подписки должна быть предоставлена роль "роль DMS — идентификатор приложения-подсистема" (при предоставлении ресурса или группы ресурсов произойдет сбой). Это требование является временным до развертывания обновления кода.

## <a name="expanded-number-of-roles"></a>Развернутое число ролей

Если число пользовательских ролей в Azure Active Directory не имеет значения, рекомендуется создать всего три роли. Вам по-прежнему потребуется роль "роль DMS — идентификатор приложения — подсистема", но приведенная выше роль "DMS Role-App ID" разделяется по типу ресурса на две различные роли.

**Настраиваемая роль для идентификатора приложения для управляемого экземпляра базы данных SQL**

```json
{
  "Name": "DMS Role - App ID - SQL MI",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<ManagedInstance_rg_name>"
  ]
}
```

**Настраиваемая роль для идентификатора приложения для хранилища**

```json
{
  "Name": "DMS Role - App ID - Storage",
  "IsCustom": true,
  "Description": "DMS App ID storage access to complete MI migrations",
  "Actions": [
"Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<StorageAccount_rg_name>"
  ]
}
```

## <a name="role-assignment"></a>Назначение роли

Чтобы назначить роль для пользователей или ИДЕНТИФИКАТОРов приложений, откройте портал Azure выполните следующие действия.

1. Перейдите к группе ресурсов или ресурсу (за исключением роли, которая должна быть предоставлена в подписке), перейдите к **элементу управление доступом**, а затем найдите только что созданные пользовательские роли.

2. Выберите соответствующую роль, выберите идентификатор приложения, а затем сохраните изменения.

  ИДЕНТИФИКАТОРы приложений теперь отображаются на вкладке **назначения ролей** .

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь с руководством по миграции для вашего сценария в [руководстве по миграции базы данных](https://datamigration.microsoft.com/)Майкрософт.
