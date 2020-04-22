---
title: Добавление, удаление и список групп в административном блоке (предварительный просмотр) - Активный каталог Azure Документы Майкрософт
description: Управление группами и разрешениями на их роль в административном подразделении в Active Directory Azure
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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683272"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Добавление и управление группами в административных подразделениях в Active Directory Azure

В Active Directory Azure (Azure AD) можно добавить группы в административную единицу (AU) для более детальной административной сферы управления.

Для шагов по подготовке к использованию PowerShell и Microsoft Graph для управления административными подразделениями [см.](roles-admin-units-manage.md#get-started)

## <a name="add-groups-to-an-au"></a>Добавление групп в АС

### <a name="azure-portal"></a>Портал Azure

В предварительном просмотре можно назначить группы только по отдельности административной единице. Нет возможности массового назначения групп в административную единицу. Вы можете назначить группу административной единицы одним из двух способов на портале:

1. Со страницы **Azure AD > groups**

    Откройте страницу обзора групп в Azure AD и выберите группу, которая должна быть назначена административному блоку. С левой стороны выберите **Административные единицы** для списка административных единиц, которым назначена группа. На вершине вы найдете опцию Присвоить административной единице и нажав на него даст панели на правой стороне, чтобы выбрать административную единицу.

    ![назначить группу по отдельности административной единице](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. С страницы **> административных подразделений Azure AD >**

    Откройте лезвие всех групп в Azure AD > административных единиц. Если в административном подразделении уже выделены группы, они будут отображаться на правой стороне. Выберите **Добавить** сверху и правая панель будет скользить в списке групп, доступных в вашей организации Azure AD. Выберите одну или несколько групп, которые будут назначены административным подразделениям.

    ![выбрать административную единицу, а затем выбрать элемент Добавления](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId

В этом примере для добавления группы в административную единицу используется cmdlet Add-AzureADAdministrativeUnitMember. Идентификатор объекта административного блока и идентификатор объекта группы, который должен быть добавлен, принимаются в качестве аргумента. Выделенный раздел может быть изменен по мере необходимости для конкретной среды.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref

    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/groups/{id}"
    }

Пример.

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
    }

## <a name="list-groups-in-an-au"></a>Группы список овсяных асов

### <a name="azure-portal"></a>Портал Azure

Перейти к **Azure AD > административных единиц** на портале. Выберите административную единицу, для которой вы хотите перечислить пользователей. По умолчанию **все пользователи** выбираются уже на левой панели. Выберите **все группы** и справа вы найдете список групп, которые являются членами выбранной административной единицы.

![Выберите административное подразделение для удаления](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId

Это поможет вам получить всех членов административного подразделения. Если вы хотите отобразить все группы, являющиеся членами административной единицы, можно использовать фрагмент кода ниже:

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

## <a name="list-aus-for-a-group"></a>Список AUs для группы

### <a name="azure-portal"></a>Портал Azure

На портале Azure AD вы можете открыть сведения группы, открыв **группы.** Выберите группу, чтобы открыть профиль группы. Выберите **Административные единицы,** чтобы перечислить все административные единицы, в которых группа является членом.

![Список административных единиц для группы](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-group-from-an-au"></a>Удалить группу из АС

### <a name="azure-portal"></a>Портал Azure

На портале Azure можно удалить группу из административной единицы.

Откройте > **группы** **AD Azure**и откройте профиль для группы, которая требуется удалить из административного блока. Выберите **Административные единицы** в левой панели, чтобы перечислить все административные единицы, в которых группа является членом. Выберите единицу административной единицы, из нее необходимо удалить группу, а затем выберите **Удалить из административного блока.**

![Удаление группы из административной единицы](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

Кроме того, можно перейти в**административные подразделения** **Azure AD** > и выбрать административную единицу, в которой группа является членом. Выберите **группы** в левой панели для списка групп участников. Выберите группу, которая будет удалена из административной единицы, а затем выберите **Удалить группы.**

![Группы списоков в административной единице](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref

## <a name="next-steps"></a>Следующие шаги

- [Назначить роль административной единице](roles-admin-units-assign-roles.md)
- [Управление пользователями в административном блоке](roles-admin-units-add-manage-users.md)
