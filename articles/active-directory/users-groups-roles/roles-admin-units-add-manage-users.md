---
title: Добавление, удаление и список пользователей в административном блоке (предварительный просмотр) - Активный каталог Azure Документы Майкрософт
description: Управление пользователями и разрешениями на их роль в административном подразделении в Active Directory Azure
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
ms.openlocfilehash: 92d96abd343266372984918fd0c942ec1cae865f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428307"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Добавление и управление пользователями в административном блоке в Active Directory Azure

В Active Directory Azure (Azure AD) можно добавить пользователей в административную единицу (AU) для более детальной административной сферы управления.

Для шагов, готовых к использованию PowerShell и Microsoft Graph для управления административными единицами, [см.](roles-admin-units-manage.md#getting-started)

## <a name="add-users-to-an-au"></a>Добавление пользователей в AU

### <a name="azure-portal"></a>Портал Azure

Вы можете назначить пользователей в административные единицы двумя способами.

1. Индивидуальное назначение

    1. Вы можете перейти в Azure AD на портале и выбрать пользователей и выбрать пользователя, который будет назначен в административное подразделение. Затем можно выбрать административные единицы в левой панели. Пользователь может быть назначен в один или несколько административных единиц, нажав на Присвоение административной единицы и выбрав административные единицы, где пользователь должен быть назначен.

       ![выберите Добавить, а затем ввести имя для административной единицы](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

    1. Вы можете перейти на Azure AD на портале и выбрать административные единицы в левом стеле, а затем выбрать административную единицу, в которой должны быть назначены пользователи. Выберите всех пользователей на левой панели, а затем выберите Добавить участника. Затем можно выбрать одного или нескольких пользователей, которые будут назначены административному блоку из правого стекла.

        ![выбрать административную единицу, а затем выбрать элемент Добавления](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

1. Массовое назначение

    Перейдите в Azure AD на портале и выберите административные единицы. Выберите административное подразделение, в котором должны быть добавлены пользователи. Продолжить, нажав на всех пользователей -> Добавить членов из файла .csv. Затем вы можете загрузить шаблон CSV и отсваивать файл. Формат прост и нуждается в одном UPN, который будет добавлен в каждой строке. Как только файл будет готов, сохраните его в соответствующем месте, а затем загрузите в шаг 3, как выделено в моментальном снимке.

    ![объем новейшему присвоению пользователям административной единицы](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $UserObj.ObjectId

В приведенном выше примере для добавления пользователя в административный блок используется cmdlet Add-AzureADAdministrativeUnitMember. Идентификатор объекта Административной единицы, в котором должен быть добавлен пользователь, и идентификатор объекта пользователя, который должен быть добавлен, рассматриваются в качестве аргумента. Выделенный раздел может быть изменен по мере необходимости для конкретной среды.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref
    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/users/{id}"
    }

Пример

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/johndoe@fabidentity.com"
    }

## <a name="list-administrative-units-for-a-user"></a>Список административных единиц для пользователя

### <a name="azure-portal"></a>Портал Azure

На портале Azure вы можете открыть профиль пользователя, перейдя на Azure AD > пользователей. Нажмите на пользователя, чтобы открыть профиль пользователя.

![Откройте профиль пользователя в active-каталоге Azure](./media/roles-admin-units-add-manage-users/user-profile-admin-units.png)

Выберите **административные единицы** на левой панели, чтобы увидеть список административных единиц, в которых был назначен пользователь.

![Перечислите административные единицы для пользователя](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $userObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/users//memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-single-user-from-an-au"></a>Удалить одного пользователя из Au

### <a name="azure-portal"></a>Портал Azure

Существует два способа удалить пользователя из административной единицы. На портале Azure вы можете открыть профиль пользователя, перейдя к**пользователям** **Azure AD.** >  Выберите пользователя, чтобы открыть профиль пользователя. Выберите единицу административной выхваемой единицы, из которых пользователь будет удален, и выберите **Удалить из административного блока.**

![Удалить пользователя из административного блока из профиля пользователя](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

Вы также можете удалить пользователя в**подразделениях** **Azure AD,** > выбрав административное подразделение, из которых необходимо удалить пользователей. Выберите пользователя и выберите **Удалить участника**.
  
![Удалить пользователя на уровне административного блока](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberUserObjId

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<user-id>/$ref

## <a name="bulk-remove-more-than-one-user"></a>Массовые удалить более одного пользователя

Вы можете перейти на Azure AD > административных единиц и выбрать административное подразделение, из которых вы хотите удалить пользователей. Нажмите на bulk удалить член. Загрузите шаблон CSV для предоставления списка пользователей, которые будут удалены.

Отображйте загруженный шаблон CSV с соответствующими записями пользователей. Не удаляйте первые два ряда шаблона. Добавьте по одному пользователю UPN в каждую строку.

![Отсечение файла CSV для удаления пользователей из административных единиц](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

После того как вы сохранили записи в файле, загрузить файл, выберите **Отправить**.

![Отправить объемный файл загрузки](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Назначить роль административной единице](roles-admin-units-assign-roles.md)
- [Добавление групп в административную единицу](roles-admin-units-add-manage-groups.md)
