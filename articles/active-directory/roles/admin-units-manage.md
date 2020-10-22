---
title: Добавление и удаление административных единиц — Azure Active Directory | Документация Майкрософт
description: Используйте административные единицы, чтобы ограничить область разрешений роли в Azure Active Directory.
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
ms.openlocfilehash: 7b47ea3cc55be26521dfa6e2b3230b477f82f442
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92377907"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Управление административными единицами в Azure Active Directory

Для более детального административного управления в Azure Active Directory (Azure AD) можно назначить пользователей роли Azure AD с областью, ограниченной одним или несколькими административными единицами (Австралия).

## <a name="get-started"></a>Начало работы

1. Чтобы выполнить запросы из следующих инструкций с помощью [Graph Explorer](https://aka.ms/ge), выполните следующие действия.

    а. На портале Azure перейдите к Azure AD. В списке приложений выберите **Graph Explorer**, а затем выберите **предоставить согласие администратора для проводника Graph**.

    ![Снимок экрана со ссылкой на "предоставление согласия администратора"](./media/admin-units-manage/select-graph-explorer.png)


1. Используйте предварительную версию Azure AD PowerShell.

## <a name="add-an-administrative-unit"></a>Добавление административной единицы

### <a name="use-the-azure-portal"></a>Использование портала Azure

1. В портал Azure перейдите в Azure AD, а затем в левой области выберите **административные единицы**.

    ![Снимок экрана с административным унитслинк в Azure AD](./media/admin-units-manage/nav-to-admin-units.png)

1. Выберите **Добавить** , а затем введите имя административной единицы. При необходимости добавьте описание административной единицы.

    ![Снимок экрана: кнопка "Добавить" и текстовое поле для ввода имени административной единицы](./media/admin-units-manage/add-new-admin-unit.png)

1. Нажмите кнопку **Добавить** , чтобы завершить административную единицу.

### <a name="use-powershell"></a>Использование PowerShell

Прежде чем пытаться выполнить следующие команды, установите Azure AD PowerShell (Предварительная версия):

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

При необходимости можно изменить значения, заключенные в кавычки.

### <a name="use-microsoft-graph"></a>Использование Microsoft Graph

```http
Http Request
POST /administrativeUnits
Request body
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>Удаление административной единицы

В Azure AD вы можете удалить административную единицу, которая больше не нужна в качестве единицы области для административных ролей.

### <a name="use-the-azure-portal"></a>Использование портала Azure

1. В портал Azure перейдите к **Azure AD**  >  **административным единицам**Azure AD. 
1. Выберите удаляемую административную единицу и нажмите кнопку **Удалить**. 
1. Чтобы подтвердить удаление административной единицы, выберите **Да**. Административная единица удаляется.

![Снимок экрана: кнопка "административная единица удаления" и окно подтверждения](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>Использование PowerShell

```powershell
$delau = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -ObjectId $delau.ObjectId
```

Можно изменить значения, заключенные в кавычки, в соответствии с требованиями конкретной среды.

### <a name="use-the-graph-api"></a>Использование API Graph

```http
HTTP request
DELETE /administrativeUnits/{Admin id}
Request body
{}
```

## <a name="next-steps"></a>Дальнейшие шаги

* [Управление пользователями в административной единице](admin-units-add-manage-users.md)
* [Управление группами в административной единице](admin-units-add-manage-groups.md)
