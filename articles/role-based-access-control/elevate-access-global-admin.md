---
title: Повышение прав доступа глобального администратора в Azure Active Directory | Документация Майкрософт
description: Сведения об использовании портала Azure или REST API для повышения прав доступа глобального администратора в Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: bagovind
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/15/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: a2f66078a817f5e6ad7296df11634a1a6130a055
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49321671"
---
# <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Повышение прав доступа глобального администратора в Azure Active Directory

Если вы являетесь [глобальным администратором](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator) в Azure Active Directory (Azure AD), иногда могут возникать ситуации, требующие выполнения следующих задач:

- восстановление доступа к подписке Azure, если пользователь потерял доступ;
- предоставление другому пользователю или себе доступа к подписке Azure;
- просмотр всех подписок Azure в организации;
- предоставление приложению автоматизации (например, приложению для выставления счетов или аудита) доступа ко всем подпискам Azure.

В этой статье описываются различные способы повышения прав доступа в Azure AD.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="overview"></a>Обзор

Azure AD и ресурсы Azure защищены независимо друг от друга. То есть назначения ролей Azure AD не предоставляют доступ к ресурсам Azure, а назначения ролей Azure не предоставляют доступ к Azure AD. Однако глобальный администратор в Azure AD может назначить себе права доступа для управления всеми подписками Azure и группами управления в вашем каталоге. Эту возможность следует использовать, если у вас нет доступа к ресурсам в подписке Azure, например к виртуальным машинам или учетным записям хранения, и вы хотите использовать свои привилегии глобального администратора для получения доступа к этим ресурсам.

При повышении прав доступа вам будет назначена роль [администратора доступа пользователей](built-in-roles.md#user-access-administrator) в Azure, охватывающая корень каталога (`/`). Это позволяет просматривать все ресурсы и назначать доступ в любой подписке или группе управления в каталоге. Назначения роли администратора доступа пользователей можно удалить с помощью PowerShell.

После внесения изменений, которые необходимо выполнить на уровне корня, следует удалить этот повышенный уровень доступа.

![Повышение прав доступа](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Портал Azure

Выполните следующие действия, чтобы повысить права доступа глобального администратора с помощью портала Azure.

1. Войдите на [портал Azure](https://portal.azure.com) или [центр администрирования Azure Active Directory](https://aad.portal.azure.com) как глобальный администратор.

1. В списке навигации выберите **Azure Active Directory**, а затем нажмите кнопку **Свойства**.

   ![Снимок экрана: свойства Azure AD](./media/elevate-access-global-admin/aad-properties.png)

1. В разделе **Управление доступом для ресурсов Azure** установите значение параметра **Да**.

   ![Снимок экрана: управление доступом для ресурсов Azure](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Если для параметра задано значение **Да**, вам назначается роль администратора доступа пользователей в Azure RBAC, охватывающая корень каталога (/). Это предоставляет разрешение на назначение ролей во всех подписках и группах управления Azure, связанных с данным каталогом Azure AD. Этот параметр доступен только пользователям, которым назначена роль глобального администратора в Azure AD.

   Если для параметра задать значение **Нет**, роль администратора доступа пользователей в Azure RBAC, охватывающая корень каталога (/), будет удалена из вашей учетной записи. Вы больше не сможете назначать роли во всех подписках и группах управления Azure, связанных с данным каталогом Azure AD. Вы сможете просматривать (и управлять ими) только подписки и группы управления Azure, к которым вам был предоставлен доступ.

1. Нажмите кнопку **Сохранить**, чтобы сохранить настройки.

   Этот параметр не является глобальным свойством и применяется только к текущему выполнившему вход пользователю.

1. Выполните задачи, для которых требуются повышенные права доступа. Закончив, установите переключатель обратно в положение **Нет**.

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-role-assignment-at-the-root-scope-"></a>Получение списка назначения ролей в корневой области (/)

Чтобы получить список назначения роли администратора доступа пользователей для пользователя в области scope (`/`), выполните команду [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment).

```azurepowershell
Get-AzureRmRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc
ObjectType         : User
```

### <a name="remove-a-role-assignment-at-the-root-scope-"></a>Удаление назначения ролей в корневой области (/)

Чтобы удалить назначение роли администратора доступа пользователей для пользователя в области root (`/`), выполните команду [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment).

```azurepowershell
Remove-AzureRmRoleAssignment -SignInName <username@example.com> `
  -RoleDefinitionName "User Access Administrator" -Scope "/"
```

## <a name="rest-api"></a>REST API

### <a name="elevate-access-for-a-global-administrator"></a>Повышение прав доступа глобального администратора

Чтобы повысить права доступа глобального администратора с помощью REST API, выполните приведенные ниже основные действия.

1. С помощью REST вызовите действие `elevateAccess`, которое предоставит вам роль администратора доступа пользователей в области root (`/`).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Создание [назначение роли](/rest/api/authorization/roleassignments), чтобы назначить любую роль в любой области. В следующем примере показаны свойства для назначения роли {roleDefinitionID} в области root (`/`).

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
   }
   ```

1. Администратор доступа пользователей может также удалять назначения ролей в корневой области (`/`).

1. Удалите привилегии администратора доступа пользователей, пока они не понадобятся вновь.

### <a name="list-role-assignments-at-the-root-scope-"></a>Получение списка назначений ролей в корневой области (/)

Вы можете перечислить все назначения ролей для пользователя в корневой области (`/`).

- Вызовите [GET roleAssignments](/rest/api/authorization/roleassignments/listforscope), где `{objectIdOfUser}` — идентификатор объекта пользователя, чьи назначения роли вы хотите получить.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-the-root-scope-"></a>Вывод списка запретов назначений в корневой области (/)

Вы можете перечислить все запреты назначений для пользователя в корневой области (`/`).

- Вызовите GET denyAssignments, где `{objectIdOfUser}` — идентификатор объекта пользователя, запреты назначений которого вы хотите получить.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Удаление повышенного права доступа

При вызове `elevateAccess` для вас создается назначение роли. Поэтому, чтобы отозвать эти привилегии, необходимо удалить назначение.

1. Вызовите [GET roleDefinitions](/rest/api/authorization/roledefinitions/get) (где `roleName` — это администратор доступа пользователей), чтобы определить GUID имени роли этого администратора.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
        "roleName": "User Access Administrator",
        "type": "BuiltInRole",
        "description": "Lets you manage user access to Azure resources.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Authorization/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "0001-01-01T08:00:00.0000000Z",
        "updatedOn": "2016-05-31T23:14:04.6964687Z",
        "createdBy": null,
        "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    Сохраните ИД из параметра `name`. В этом случае — `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`.

2. Также необходимо вывести назначений ролей для администратора каталога в области каталога. Выведите список всех назначений в области каталога для `principalId` администратора каталога, совершившего вызов для повышения прав доступа. При этом будут перечислены все назначения в каталоге для objectid.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >У администратора каталога не должно быть много назначений. Если предыдущий запрос возвращает слишком много назначений, можно запросить все назначения только на уровне области каталога, а затем отфильтровать результаты: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`.
        
    2. Предыдущие вызовы возвращают список назначения ролей. Найдите назначение роли, у которого задана область `"/"` и `roleDefinitionId` заканчивается значением ИД имени роли, найденным на шаге 1, а `principalId` совпадает со значением objectId администратора каталога. 
    
    Пример назначения ролей:

        ```json
        {
          "value": [
            {
              "properties": {
                "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                "principalId": "{objectID}",
                "scope": "/",
                "createdOn": "2016-08-17T19:21:16.3422480Z",
                "updatedOn": "2016-08-17T19:21:16.3422480Z",
                "createdBy": "93ce6722-3638-4222-b582-78b75c5c6d65",
                "updatedBy": "93ce6722-3638-4222-b582-78b75c5c6d65"
              },
              "id": "/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
              "type": "Microsoft.Authorization/roleAssignments",
              "name": "e7dd75bc-06f6-4e71-9014-ee96a929d099"
            }
          ],
          "nextLink": null
        }
        ```
        
    Опять же, сохраните ИД из параметра `name`. В данном случае это e7dd75bc-06f6-4e71-9014-ee96a929d099.

    3. Наконец, используйте ИД назначения роли, чтобы удалить назначение, добавленное `elevateAccess`:

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Дополнительная информация

- [Общие сведения о различных ролях](rbac-and-directory-admin-roles.md)
- [Управление доступом на основе ролей с помощью REST](role-assignments-rest.md)
