---
title: Добавление, удаление и вывод списка пользователей в административной единице (Предварительная версия) — Azure Active Directory | Документация Майкрософт
description: Управление пользователями и их ролями в административной единице в Azure Active Directory
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
ms.openlocfilehash: 9c2c5c083115440e1e4da203f39f2b32734458c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684968"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Добавление пользователей и управление ими в административной единице в Azure Active Directory

В Azure Active Directory (Azure AD) можно добавить пользователей в административную единицу (AU) для более детального управления областью администрирования.

Инструкции по подготовке к использованию PowerShell и Microsoft Graph для управления административными единицами см. в статье [Приступая к работе](roles-admin-units-manage.md#get-started).

## <a name="add-users-to-an-au"></a>Добавление пользователей в AU

### <a name="azure-portal"></a>Портал Azure

Назначать пользователей административным единицам можно двумя способами.

1. Индивидуальное назначение

    1. Вы можете открыть Azure AD на портале и выбрать пользователей и выбрать пользователя, которого необходимо назначить административной единице. Затем можно выбрать административные единицы на панели слева. Пользователь может быть назначен одной или нескольким административным единицам, щелкнув назначить административной единице и выбрав административные единицы, в которых будет назначен пользователь.

       ![Выберите Добавить, а затем введите имя административной единицы.](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

    1. Вы можете открыть Azure AD на портале и выбрать административные единицы в левой области, а затем выбрать административную единицу, в которой будут назначаться пользователи. Выберите все пользователи в левой области, а затем щелкните Добавить участника. Затем можно выбрать одного или нескольких пользователей, которые будут назначены административной единице, с правой панели.

        ![Выберите административную единицу, а затем щелкните Добавить участника.](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

1. Групповое назначение

    Перейдите в Azure AD на портале и выберите административные единицы. Выберите административную единицу, в которую будут добавлены пользователи. Щелкните "все пользователи", > добавить членов из CSV-файла. Затем можно скачать шаблон CSV и изменить файл. Формат прост, и в каждой строке необходимо добавить одно имя участника-пользователя. Когда файл будет готов, сохраните его в нужном месте, а затем передайте его на шаге 3, выделенном в моментальном снимке.

    ![Групповое назначение пользователей административной единице](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrativeunitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId

В приведенном выше примере командлет Add-Азуреададминистративеунитмембер используется для добавления пользователя в административную единицу. Идентификатор объекта административной единицы, в которую добавляется пользователь, и идентификатор объекта пользователя, который будет добавлен в качестве аргумента. Выделенный раздел может быть изменен в соответствии с требованиями конкретной среды.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref
    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/users/{id}"
    }

Пример:

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/johndoe@fabidentity.com"
    }

## <a name="list-administrative-units-for-a-user"></a>Список административных единиц для пользователя

### <a name="azure-portal"></a>Портал Azure

В портал Azure можно открыть профиль пользователя, перейдя в Azure AD > пользователей. Щелкните пользователя, чтобы открыть профиль пользователя.

![Откройте профиль пользователя в Azure Active Directory](./media/roles-admin-units-add-manage-users/user-profile-admin-units.png)

Выберите **административные единицы** на панели слева, чтобы просмотреть список административных единиц, которым назначен пользователь.

![Перечисление административных единиц для пользователя](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $userObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/users//memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-single-user-from-an-au"></a>Удаление одного пользователя из AU

### <a name="azure-portal"></a>Портал Azure

Существует два способа удаления пользователя из административной единицы. В портал Azure можно открыть профиль пользователя, перейдя к**пользователям** **Azure AD** > . Выберите пользователя, чтобы открыть профиль пользователя. Выберите административную единицу, из которой нужно удалить пользователя, и щелкните **удалить из административной единицы**.

![Удаление пользователя из административной единицы из профиля пользователя](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

Вы также можете удалить пользователя в**административных единицах** **Azure AD** > , выбрав административную единицу, из которой вы хотите удалить пользователей. Выберите пользователя и щелкните **удалить участника**.
  
![Удаление пользователя на уровне административной единицы](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberUserObjId

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<user-id>/$ref

## <a name="bulk-remove-more-than-one-user"></a>Групповое удаление более одного пользователя

Вы можете войти в административные единицы > Azure AD и выбрать административную единицу, из которой вы хотите удалить пользователей. Щелкните элемент "групповое удаление". Скачайте шаблон CSV-файла, чтобы предоставить список пользователей для удаления.

Измените скачанный шаблон CSV, указав соответствующие записи пользователя. Не удаляйте первые две строки шаблона. Добавьте одно имя участника-пользователя в каждую строку.

![Изменение CSV-файла для группового удаления пользователей из административных единиц](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

После сохранения записей в файле отправьте его и нажмите кнопку **Отправить**.

![Отправка файла с массовыми передачами](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>Дальнейшие шаги

- [Назначение роли административной единице](roles-admin-units-assign-roles.md)
- [Добавление групп в административную единицу](roles-admin-units-add-manage-groups.md)
