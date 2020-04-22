---
title: Добавление и удаление административных единиц (предварительный просмотр) - Активный каталог Azure Документы Майкрософт
description: Используйте административные единицы для ограничения сферы действия разрешений на роль в Active Directory Azure.
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
ms.openlocfilehash: 7a4a0dfaeda18b3f68ddc3c7cc7333b8c994d174
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684912"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Управление административными подразделениями в каталоге Azure Active

Для более детального административного управления в Active Directory Azure (Azure AD) можно назначить пользователям роль Azure AD с областью, ограниченной одним или несколькими административными единицами (AUs).

## <a name="get-started"></a>Начало работы

1. Для выполнения запросов из следующих инструкций через [Graph Explorer](https://aka.ms/ge), сделать следующее:

    а. На портале Azure перейдите к Azure AD. В списке приложений выберите **Graph Explorer,** а затем выберите **согласие админа Гранта на Graph Explorer.**

    ![Скриншот, показывающий ссылку на "Грант админ согласия"](./media/roles-admin-units-manage/select-graph-explorer.png)

    b. В Graph Explorer выберите **бета-версию.**

    ![Скриншот, показывающий выбранную бета-версию](./media/roles-admin-units-manage/select-beta-version.png)

1. Используйте предварительную версию Azure AD PowerShell.

## <a name="add-an-administrative-unit"></a>Добавление административной единицы

### <a name="use-the-azure-portal"></a>Использование портала Azure

1. На портале Azure перейдите на Azure AD, а затем, в левом стеле, выберите **Административные единицы**.

    ![Скриншот ссылки административных единиц (Preview) в Azure AD](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Выберите **Добавить,** а затем введите название административной единицы. Дополнительно добавьте описание административной единицы.

    ![Скриншот кнопки Добавить и текстового ящика для ввода названия административного блока](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. Выберите **Добавить** для завершения административной единицы.

### <a name="use-powershell"></a>Использование PowerShell

Установите Azure AD PowerShell (предварительный просмотр), прежде чем пытаться запустить следующие команды:

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

При необходимости можно изменить значения, оговоренные кавычками.

### <a name="use-microsoft-graph"></a>Использование графика Майкрософт

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>Удалить административную единицу

В Azure AD можно удалить административную единицу, которая вам больше не нужна как единица области для административных ролей.

### <a name="use-the-azure-portal"></a>Использование портала Azure

1. На портале Azure перейдите на**административные подразделения** **Azure AD.** >  
1. Выберите административное подразделение для удаления, а затем выберите **Удалить**. 
1. Чтобы подтвердить, что вы хотите удалить административное подразделение, выберите **Да**. Административная единица удаляется.

![Скриншот кнопки "Удаление" административного блока и окна подтверждения](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>Использование PowerShell

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

Можно изменить значения, заключенные в кавычки, как это требуется для конкретной среды.

### <a name="use-the-graph-api"></a>Использование API графика

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>Следующие шаги

* [Управление пользователями в административном блоке](roles-admin-units-add-manage-users.md)
* [Управление группами в административной единице](roles-admin-units-add-manage-groups.md)
