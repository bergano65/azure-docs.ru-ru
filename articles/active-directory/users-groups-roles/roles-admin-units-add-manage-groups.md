---
title: Добавление, удаление и перечисление групп в административной единице (Предварительная версия) — Azure Active Directory | Документация Майкрософт
description: Управление группами и разрешениями их ролей в административной единице в Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f0b0c0a7b666aed56ad24625c80c0a57683b998
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81683272"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Добавление и управление группами в административных единицах в Azure Active Directory

В Azure Active Directory (Azure AD) можно добавлять группы в административную единицу (AU) для более детального управления областью администрирования.

Инструкции по подготовке к использованию PowerShell и Microsoft Graph для управления административными единицами см. в статье [Приступая к работе](roles-admin-units-manage.md#get-started).

## <a name="add-groups-to-an-au"></a>Добавление групп в AU

### <a name="azure-portal"></a>Портал Azure

В предварительной версии можно назначать группы только по отдельности административной единице. Невозможно выполнить групповое назначение групп административной единице. Вы можете назначить группу административной единице одним из двух способов на портале:

1. На странице **групп > Azure AD**

    Откройте страницу обзора групп в Azure AD и выберите группу, которая должна быть назначена административной единице. В левой части выберите **административные единицы** , чтобы вывести список административных единиц, которым назначена группа. В верхней части экрана вы найдете параметр назначить административной единице и щелкнув его, вы увидите панель справа, чтобы выбрать административную единицу.

    ![Назначение группы по отдельности административной единице](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. На странице **Azure AD > административные единицы > все группы**

    Откройте колонку все группы в административных единицах Azure AD >. Если с административной единицей уже назначены группы, они будут отображаться на правой стороне. Выберите **Добавить** в верхней части, и на правой панели появится список групп, доступных в вашей организации Azure AD. Выберите одну или несколько групп, которые будут назначены административным единицам.

    ![Выберите административную единицу, а затем щелкните Добавить участника.](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId

В этом примере командлет Add-Азуреададминистративеунитмембер используется для добавления группы в административную единицу. Идентификатор объекта административной единицы и идентификатор объекта добавляемой группы берутся в качестве аргумента. Выделенный раздел может быть изменен в соответствии с требованиями конкретной среды.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref

    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/groups/{id}"
    }

Пример:

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
    }

## <a name="list-groups-in-an-au"></a>Вывод списка групп в AU

### <a name="azure-portal"></a>Портал Azure

Перейдите к **административным единицам Azure AD >** на портале. Выберите административную единицу, для которой требуется получить список пользователей. По умолчанию **все пользователи** уже выбраны на панели слева. Выберите **все группы** . в правой части находится список групп, входящих в выбранную административную единицу.

![Выберите административную единицу для удаления](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId

Это поможет вам получить все члены административной единицы. Если требуется отобразить все группы, входящие в административную единицу, можно использовать приведенный ниже фрагмент кода.

    foreach ($member in (Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
    {
    if($member.ObjectType -eq "Group")
    {
    Get-AzureADGroup -ObjectId $member.ObjectId
    }
    }

### <a name="microsoft-graph"></a>Microsoft Graph

    HTTP request
    GET /administrativeUnits/{Admin id}/members/$/microsoft.graph.group
    Request body
    {}

## <a name="list-aus-for-a-group"></a>Вывод списка Au для группы

### <a name="azure-portal"></a>Портал Azure

На портале Azure AD можно открыть сведения о группе, открыв **группы**. Выберите группу, чтобы открыть профиль группы. Выберите **административные единицы** , чтобы получить список всех административных единиц, членом которых является группа.

![Список административных единиц для группы](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-group-from-an-au"></a>Удаление группы из AU

### <a name="azure-portal"></a>Портал Azure

Существует два способа удаления группы из административной единицы в портал Azure.

Откройте группу **Azure AD** > **и откройте** профиль для группы, которую необходимо удалить из административной единицы. Выберите **административные единицы** на панели слева, чтобы получить список всех административных единиц, членом которых является группа. Выберите административную единицу, из которой необходимо удалить группу, а затем выберите **удалить из административной единицы**.

![Удаление группы из административной единицы](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

Кроме того, можно переходить к**административным единицам** **Azure AD** > и выбрать административную единицу, членом которой является группа. Выберите **группы** на панели слева, чтобы вывести список групп элементов. Выберите группу для удаления из административной единицы, а затем выберите **удалить группы**.

![Список групп в административной единице](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref

## <a name="next-steps"></a>Дальнейшие шаги

- [Назначение роли административной единице](roles-admin-units-assign-roles.md)
- [Управление пользователями в административной единице](roles-admin-units-add-manage-users.md)
