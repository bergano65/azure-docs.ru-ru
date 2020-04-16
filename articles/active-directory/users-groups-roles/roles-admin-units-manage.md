---
title: Добавление и удаление административных единиц (предварительный просмотр) - Активный каталог Azure Документы Майкрософт
description: Используйте административные единицы для ограничения объема разрешений на роль в каталоге Azure Active
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
ms.openlocfilehash: 351cbee9cbc7cc02c7f20553b6fc8c3f861541df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428164"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Управление административными подразделениями в каталоге Azure Active

Для более детального административного управления в Active Directory Azure (Azure AD) можно назначить пользователям роль Azure AD с областью, ограниченной одним или несколькими административными единицами (AUs).

## <a name="getting-started"></a>Начало работы

1. Для выполнения запросов из следующих инструкций через [Graph Explorer,](https://aka.ms/ge)пожалуйста, убедитесь:

    1. Перейдите на Azure AD на портале, а затем в приложениях выберите Graph Explorer и предоставьте согласие админа Graph Explorer.

        ![выберите Graph Explorer и предоставьте согласие админа на этой странице](./media/roles-admin-units-manage/select-graph-explorer.png)

    1. В Graph Explorer убедитесь, что вы выберете бета-версию.

        ![выбрать бета-версию перед операцией POST](./media/roles-admin-units-manage/select-beta-version.png)

1. Пожалуйста, используйте предварительную версию Azure AD PowerShell. Подробные инструкции здесь.

## <a name="add-an-administrative-unit"></a>Добавление административной единицы

### <a name="azure-portal"></a>Портал Azure

1. Перейдите в Active Directory на портале и выберите административные единицы в левой панели.

    ![перейдите к административным подразделениям в active-каталоге Azure](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Выберите **Добавить**и указать название административной единицы и опционально можно добавить описание для административной единицы.

    ![выберите Добавить, а затем ввести имя для административной единицы](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. Выберите **Добавить** для завершения административной единицы.

### <a name="powershell"></a>PowerShell

Установите Azure AD PowerShell (предварительная версия), прежде чем пытаться выполнить действия ниже:

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

Значения, указанные выше, могут быть изменены по мере необходимости.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>Удалить административную единицу

В Active Directory Azure (Azure AD) можно удалить блок админ, который вам больше не нужен как единица возможностей для административных ролей.

### <a name="azure-portal"></a>Портал Azure

Перейти к **Azure AD > административных единиц** на портале. Выберите административное подразделение для удаления, а затем выберите **Удалить**. После подтверждения **Да,** административное подразделение будет удалено.

![Выберите административное подразделение для удаления](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="powershell"></a>PowerShell

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

Выделенный раздел может быть изменен по мере необходимости для конкретной среды.

### <a name="graph-api"></a>API Graph

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>Дальнейшие действия

[Управление пользователями в административном подразделении](roles-admin-units-add-manage-users.md)
[Управление группами в административном подразделении](roles-admin-units-add-manage-groups.md)
