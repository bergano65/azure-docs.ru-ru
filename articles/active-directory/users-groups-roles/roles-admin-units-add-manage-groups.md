---
title: Добавление, удаление и перечисление групп в административной единице Azure Active Directory | Документация Майкрософт
description: Управление группами и их разрешениями ролей в административной единице в Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 10/07/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 133ea21bf7a7c1df0fccaeacce7d7a29199c033d
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91827695"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Добавление и управление группами в административных единицах в Azure Active Directory

В Azure Active Directory (Azure AD) можно добавлять группы в административную единицу (AU) для более детального управления областью администрирования.

Инструкции по подготовке к использованию PowerShell и Microsoft Graph для управления административными единицами см. в разделе [Начало работы](roles-admin-units-manage.md#get-started).

## <a name="add-groups-to-an-au"></a>Добавление групп в AU

### <a name="azure-portal"></a>Портал Azure

Группы можно назначать только по отдельности административной единице. В административную единицу нельзя назначить группы. Вы можете назначить группу административной единице на портале одним из двух способов.

1. На странице **Azure AD > Группы**

    Откройте страницу обзора групп в Azure AD и выберите группу, которую нужно назначить административной единице. В левой части выберите **Административные единицы**, чтобы получить список административных единиц, которым назначена группа. В верхней части экрана расположен параметр "Назначить административной единице", после нажатия на который отобразится панель справа для выбора административной единицы.

    ![Назначение группы административной единице по отдельности](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. На странице **Azure AD > Административные единицы > Все группы**

    Откройте колонку "Все группы" в разделе "Azure AD > Административные единицы". Если административной единице уже были назначены группы, они отобразятся справа. Нажмите кнопку **Добавить** в верхней части — на панели справа появится список групп, доступных в вашей организации Azure AD. Выберите одну или несколько групп, которые будут назначены административным единицам.

    ![Выберите административную единицу, затем щелкните "Добавить участника".](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrative unitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId
```

В этом примере командлет Add-Азуреадмсадминистративеунитмембер используется для добавления группы в административную единицу. В качестве аргументов передаются идентификатор объекта административной единицы и идентификатор объекта добавляемой группы. Выделенную часть можно изменить в соответствии с требованиями конкретной среды.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref

Request body
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/{id}"
}
```

Пример

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="list-groups-in-an-au"></a>Вывод списка групп в AU

### <a name="azure-portal"></a>Портал Azure

Перейдите на портале в раздел **Azure AD > Административные единицы**. Выберите административную единицу, для которой требуется получить список пользователей. По умолчанию на левой панели уже выбраны **Все пользователи**. Выберите **Все группы**, и в правой части отобразится список групп, входящих в выбранную административную единицу.

![Список групп в административной единице](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrative unitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId
```

Это позволит вам получить всех членов административной единицы. Если требуется отобразить все группы, входящие в административную единицу, можно использовать приведенный ниже фрагмент кода.

```http
foreach ($member in (Get-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```

### <a name="microsoft-graph"></a>Microsoft Graph

```http
HTTP request
GET /directory/administrativeUnits/{Admin id}/members/$/microsoft.graph.group
Request body
{}
```

## <a name="list-aus-for-a-group"></a>Вывод списка AU для группы

### <a name="azure-portal"></a>Портал Azure

На портале Azure AD можно показать сведения о группе, открыв раздел **Группы**. Выберите группу, чтобы открыть ее профиль. Нажмите **Административные единицы**, чтобы получить список всех административных единиц, членом которых является группа.

![Список административных единиц для группы](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/v1.0/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-au"></a>Удаление группы из AU

### <a name="azure-portal"></a>Портал Azure

Существует два способа удаления группы из административной единицы на портале Azure.

- Удалить из обзора группы

  1. Откройте группу **Azure AD**  >  **Groups** и откройте профиль для группы, которую необходимо удалить из административной единицы.
  1. На панели слева нажмите **Административные единицы**, чтобы получить список всех административных единиц, членом которых является группа. Выберите административную единицу, из которой необходимо удалить группу, а затем нажмите **Удалить из административной единицы**.

    ![Удаление группы из административной единицы](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

- Удалить из административной единицы

  1. Откройте **Azure AD**  >  **административные единицы** Azure AD и выберите административную единицу, членом которой является группа.
  1. На панели слева нажмите **Группы**, чтобы вывести список групп административной единицы.
  1. Выберите группу, которую необходимо удалить из административной единицы, и нажмите **Удалить группы**.

    ![Список групп в административной единице](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureADMSAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId
```

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/v1.0/directory/AdministrativeUnits/<adminunit-id>/members/<group-id>/$ref
```

## <a name="next-steps"></a>Дальнейшие действия

- [Назначение роли административной единице](roles-admin-units-assign-roles.md)
- [Управление пользователями в административной единице](roles-admin-units-add-manage-users.md)
