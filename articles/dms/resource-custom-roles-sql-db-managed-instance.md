---
title: "Пользовательские роли: Онлайн-сервер S'L для управляемых экземпляров"
titleSuffix: Azure Database Migration Service
description: Научитесь использовать пользовательские роли для сервера S'L Server для управляемых экземпляров онлайн-данных Azure S'L.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 10/25/2019
ms.openlocfilehash: e9a1024ca3ab68841474ab051c029042df4915b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254944"
---
# <a name="custom-roles-for-sql-server-to-sql-database-managed-instance-online-migrations"></a>Пользовательские роли для сервера S'L в базу данных S'L управляемые экземпляры онлайн

Миграционная служба базы данных Azure использует идентификатор APP для взаимодействия с службами Azure. Идентификатор APP требует либо роли автора на уровне подписки (что многие отделы корпоративной безопасности не разрешат) или создания пользовательских ролей, которые предоставляют конкретные разрешения, которые требует служба миграции базы данных Azure. Поскольку в каталоге Azure Active Directory существует ограничение в 2000 пользовательских ролей, может потребоваться объединить все разрешения, требуемые специально Идентификатором APP, в одну или две пользовательские роли, а затем предоставить APP ID пользовательскую роль на определенных объектах или группах ресурсов (против в группах ресурсов (против в группе ресурсов уровня подписки). Если количество пользовательских ролей не является проблемой, можно разделить пользовательские роли по типу ресурсов, чтобы создать три пользовательские роли в общей сложности, как описано ниже.

Раздел AssignableScopes строки определения роли json позволяет контролировать, где разрешения отображаются в uI **добавленного назначения роли** на портале. Скорее всего, вы захотите определить роль на уровне ресурсов или даже на уровне ресурсов, чтобы избежать захламления uI дополнительными ролями. Обратите внимание, что это не выполняет фактическое назначение ролей.

## <a name="minimum-number-of-roles"></a>Минимальное количество ролей

В настоящее время мы рекомендуем создать как минимум две пользовательские роли для Идентификатора APP, одна на уровне ресурсов, а другая на уровне подписки.

> [!NOTE]
> Последнее требование пользовательской роли в конечном итоге может быть удалено по мере развертывания в Azure нового кода управляемых экземпляров s-L.

**Пользовательская роль для идентификатора APP**. Эта роль необходима для миграции миграционных служб ресурсов Azure на уровне *ресурса* или *группы ресурсов* (для получения дополнительной информации об идентификаторе APP см. статью [Используйте портал для создания приложения Azure AD и принципа службы, которое может получить доступ к ресурсам).](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

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

**Пользовательская роль для APP ID - подписка**. Эта роль необходима для миграции миграционной службы базы данных Azure на уровне *подписки.*

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

Вышеупомянутый Json должен храниться в трех текстовых файлах, и вы можете использовать либо AzureRM, смдлеты A'S PowerShell, либо Azure CLI для создания ролей, используя либо **New-AzureRmRoleDefinition (AzureRM)** или **New-AzRoleDefinition (АЗРолеОпределение)**.

Для получения дополнительной информации смотрите статью [Пользовательские роли для ресурсов Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

После создания этих пользовательских ролей необходимо добавить назначения ролей пользователям и APP ID (ы) в соответствующие ресурсы или группы ресурсов:

* Роль "DMS Role - App ID" должна быть предоставлена идентификатору APP, который будет использоваться для миграций, а также в учетной записи хранилища, экземпляре службы миграции базы данных Azure и управляемых уровнях ресурсов экземпляра базы данных.
* Роль "DMS Role - App ID - Sub" должна быть предоставлена Идентификатору APP на уровне подписки (предоставление на ресурсе или ресурсной группе не удастся). Это требование является временным до развертывания обновления кода.

## <a name="expanded-number-of-roles"></a>Расширенное количество ролей

Если количество пользовательских ролей в каталоге Azure Active Не вызывает беспокойства, мы рекомендуем создать в общей сложности три роли. Вам все еще понадобится роль "DMS Role - App ID - Sub", но вышеупомянутая роль "DMS Role - App ID" разделена по типу ресурсов на две разные роли.

**Пользовательская роль для идентификатора APP для управляемого экземпляра базы данных S'L**

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

**Пользовательская роль для идентификатора APP для хранения**

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

Чтобы назначить роль пользователям/APP ID, откройте портал Azure, выполните следующие действия:

1. Перейдите к группе ресурсов или ресурсу (за исключением роли, которая должна быть предоставлена в подписке), перейдите на **контроль доступа,** а затем прокрутите, чтобы найти только что созданные пользовательские роли.

2. Выберите подходящую роль, выберите идентификатор APP, а затем сохраните изменения.

  Ваш идентификатор APP (ы) теперь отображается на вкладке **«Назначения роли».**

## <a name="next-steps"></a>Дальнейшие действия

* Просмотрите руководство по миграции для вашего сценария в [Руководстве по миграции баз данных](https://datamigration.microsoft.com/)Майкрософт.
