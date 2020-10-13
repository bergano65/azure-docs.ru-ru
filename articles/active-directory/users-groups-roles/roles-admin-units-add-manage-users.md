---
title: Добавление, удаление и вывод списка пользователей в административной единице Azure Active Directory | Документация Майкрософт
description: Управление пользователями и их ролями в административной единице в Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d29f4ef5806eb8ed9385696dea78f4ae0992b93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91818236"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Добавление пользователей и управление ими в административной единице в Azure Active Directory

В Azure Active Directory (Azure AD) можно добавить пользователей в административную единицу (AU) для более детального управления областью администрирования.

Инструкции по подготовке к использованию PowerShell и Microsoft Graph для управления административными единицами см. в разделе [Начало работы](roles-admin-units-manage.md#get-started).

## <a name="add-users-to-an-au"></a>Добавление пользователей в AU

### <a name="azure-portal"></a>Портал Azure

Пользователи могут назначаться административным единицам отдельно или в ходе выполнения групповых операций.

- Отдельное назначение из профиля пользователя

   1. Войдите в [центр администрирования Azure AD](https://portal.azure.com) с правами администратора привилегированных ролей.
   1. Выберите **Пользователи** и выберите пользователя, которого необходимо назначить административной единице, чтобы открыть профиль пользователя.
   1. Выберите **административные единицы**. Пользователь может быть назначен одной или нескольким административным единицам, выбрав **назначить административной единице** и выбрав административные единицы, в которых будет назначен пользователь.

       ![Выберите Добавить, а затем введите имя административной единицы.](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

- Индивидуальное назначение из административной единицы

   1. Войдите в [центр администрирования Azure AD](https://portal.azure.com) с правами администратора привилегированных ролей.
   1. Выберите **административные единицы** , а затем выберите административную единицу, в которой будут назначаться пользователи.
   1. Выберите **все пользователи** , а затем щелкните **Добавить участника** , чтобы выбрать одного или нескольких пользователей, которые будут назначены административной единице на панели **Добавление участника** .

        ![Выберите административную единицу, затем щелкните "Добавить участника".](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

- Групповое назначение

   1. Войдите в [центр администрирования Azure AD](https://portal.azure.com) с правами администратора привилегированных ролей.
   1. Выберите **административные единицы**.
   1. Выберите административную единицу, в которую будут добавлены пользователи.
   1. Откройте **все пользователи**  >  **Добавить участников из CSV-файла**. Затем можно загрузить шаблон значений с разделителями-запятыми (CSV) и изменить файл. Формат прост, и в каждой строке необходимо добавить одно имя участника-пользователя. Когда файл будет готов, сохраните его в нужном месте и передайте его в рамках этого шага.

    ![Групповое назначение пользователей административной единице](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrativeunitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId
```

В приведенном выше примере командлет Add-AzureADAdministrativeUnitMember используется для добавления пользователя в административную единицу. Идентификатор объекта административной единицы, в которую добавляется пользователь, и идентификатор объекта пользователя, который будет добавлен в качестве аргумента. Выделенную часть можно изменить в соответствии с требованиями конкретной среды.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref
Request body
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{id}"
}
```

Пример

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/johndoe@fabidentity.com"
}
```

## <a name="list-administrative-units-for-a-user"></a>Список административных единиц для пользователя

### <a name="azure-portal"></a>Портал Azure

В портал Azure можно открыть профиль пользователя, выполнив следующие действия.

1. Открытие **пользователей Azure AD**  >  **Users**.

1. Выберите пользователя, чтобы открыть профиль пользователя.

1. Выберите **административные единицы** , чтобы просмотреть список административных единиц, которым назначен пользователь.

   ![Перечисление административных единиц для пользователя](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```
Примечание. по умолчанию Get-AzureADAdministrativeUnitMember возвращает только 100 членов. для получения дополнительных элементов можно добавить "-ALL $true".

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/v1.0/users/{id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-au"></a>Удаление одного пользователя из AU

### <a name="azure-portal"></a>Портал Azure

Существует два способа удаления пользователя из административной единицы. В портал Azure можно открыть профиль пользователя, перейдя к пользователям **Azure AD**  >  **Users**. Выберите пользователя, чтобы открыть профиль пользователя. Выберите административную единицу, из которой нужно удалить пользователя, и щелкните **удалить из административной единицы**.

![Удаление пользователя из административной единицы из профиля пользователя](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

Вы также можете удалить пользователя в **Azure AD**  >  **административных единицах** Azure AD, выбрав административную единицу, из которой вы хотите удалить пользователей. Выберите пользователя и щелкните **удалить участника**.
  
![Удаление пользователя на уровне административной единицы](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $auId -MemberId $memberUserObjId
```

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/v1.0/directory/administrativeUnits/{adminunit-id}/members/{user-id}/$ref

## <a name="bulk-remove-more-than-one-user"></a>Групповое удаление более одного пользователя

Вы можете войти в административные единицы > Azure AD и выбрать административную единицу, из которой вы хотите удалить пользователей. Щелкните элемент "групповое удаление". Скачайте шаблон CSV-файла, чтобы предоставить список пользователей для удаления.

Измените скачанный шаблон CSV, указав соответствующие записи пользователя. Не удаляйте первые две строки шаблона. Добавьте одно имя участника-пользователя в каждую строку.

![Изменение CSV-файла для группового удаления пользователей из административных единиц](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

После сохранения записей в файле отправьте его и нажмите кнопку **Отправить**.

![Отправка файла с массовыми передачами](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Назначение роли административной единице](roles-admin-units-assign-roles.md)
- [Добавление групп в административную единицу](roles-admin-units-add-manage-groups.md)
