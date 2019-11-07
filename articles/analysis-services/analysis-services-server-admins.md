---
title: Управление администраторами серверов в службах Azure Analysis Services | Документы Майкрософт
description: В этой статье описывается, как управлять администраторами сервера для Azure Analysis Services сервера с помощью портал Azure, PowerShell или интерфейсов API.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f7c57a5751f2ff34abb26b7653070ce4ee5010fe
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572623"
---
# <a name="manage-server-administrators"></a>Управление администраторами серверов

Администраторами сервера должны быть допустимые пользователи или группы безопасности в Azure Active Directory (Azure AD) для клиента, в котором размещен сервер. Для управления администраторами сервера можно использовать элемент **Администраторы служб Analysis Services** для сервера на портале Azure или свойств сервера в SSMS, PowerShell или REST API. 

Для **групп безопасности** необходимо включить [поддержку почты](https://docs.microsoft.com/exchange/recipients-in-exchange-online/manage-mail-enabled-security-groups), если свойство `MailEnabled` имеет значение `True`. При указании группы по адресу электронной почты используйте `obj:groupid@tenantid`.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Добавление администраторов на портале Azure

1. На портале выберите для сервера **Администраторы служб Analysis Services**.
2. В области **\<имя_сервера> — Администраторы служб Analysis Services** щелкните **Добавить**.
3. В области **добавления администраторов сервера** выберите учетные записи пользователей из Azure AD или пригласите внешних пользователей по электронной почте.

    ![Администраторы сервера на портале Azure](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Добавление администраторов сервера с помощью SSMS

1. Щелкните правой кнопкой мыши сервер и выберите пункт **Свойства**.
2. В разделе **Свойства сервера анализа данных** выберите **Безопасность**.
3. Нажмите кнопку **Добавить**, а затем введите адрес электронной почты для пользователя или группы в Azure AD.
   
    ![Добавление администраторов сервера в SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Используйте командлет [New-азаналисиссервицессервер](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) , чтобы указать параметр администратора при создании нового сервера. <br>
Используйте командлет [Set-азаналисиссервицессервер](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) , чтобы изменить параметр администратора для существующего сервера.

## <a name="rest-api"></a>REST API

Используйте команду [Create](https://docs.microsoft.com/rest/api/analysisservices/servers/create) для указания свойства asAdministrator при создании нового сервера. <br>
Используйте команду [Update](https://docs.microsoft.com/rest/api/analysisservices/servers/update) для указания свойства asAdministrator при изменении существующего сервера. <br>



## <a name="next-steps"></a>Дальнейшие действия 

[Аутентификация и пользовательские разрешения](analysis-services-manage-users.md)  
[Управление ролями и пользователями базы данных](analysis-services-database-users.md)  
[Контроль доступа на основе ролей](../role-based-access-control/overview.md)  

