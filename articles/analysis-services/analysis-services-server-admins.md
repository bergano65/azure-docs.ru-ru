---
title: Управление администраторами серверов в службах Azure Analysis Services | Документы Майкрософт
description: В этой статье описывается, как управлять администраторами серверов для сервера аналитических служб Azure с помощью портала Azure, PowerShell или REST AIS.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 944a84405bd7e03b72b2610278f9f0e4d3cfaf38
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454243"
---
# <a name="manage-server-administrators"></a>Управление администраторами серверов

Администраторами сервера должны быть допустимые пользователи или группы безопасности в Azure Active Directory (Azure AD) для клиента, в котором размещен сервер. Для управления администраторами сервера можно использовать элемент **Администраторы служб Analysis Services** для сервера на портале Azure или свойств сервера в SSMS, PowerShell или REST API. 

При добавлении **группы безопасности**используйте `obj:groupid@tenantid`.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Добавление администраторов на портале Azure

1. На портале выберите для сервера **Администраторы служб Analysis Services**.
2. В ** \<> сервера - Аналитики услуг администраторов**, нажмите **Добавить**.
3. В области **добавления администраторов сервера** выберите учетные записи пользователей из Azure AD или пригласите внешних пользователей по электронной почте.

    ![Администраторы сервера на портале Azure](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Добавление администраторов сервера с помощью SSMS

1. Щелкните правой кнопкой мыши сервер и выберите пункт **Свойства**.
2. В разделе **Свойства сервера анализа данных** выберите **Безопасность**.
3. Нажмите кнопку **Добавить**, а затем введите адрес электронной почты для пользователя или группы в Azure AD.
   
    ![Добавление администраторов сервера в SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Для указания параметра администратора при создании нового сервера используйте cmdlet [New-AzAnalysisServicesServer.](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) <br>
Используйте [set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) cmdlet для изменения параметра администратора для существующего сервера.

## <a name="rest-api"></a>REST API

Используйте команду [Create](https://docs.microsoft.com/rest/api/analysisservices/servers/create) для указания свойства asAdministrator при создании нового сервера. <br>
Используйте команду [Update](https://docs.microsoft.com/rest/api/analysisservices/servers/update) для указания свойства asAdministrator при изменении существующего сервера. <br>



## <a name="next-steps"></a>Следующие шаги 

[Аутентификация и разрешения пользователей](analysis-services-manage-users.md)  
[Управление ролями и пользователями базы данных](analysis-services-database-users.md)  
[Контроль доступа на основе ролей](../role-based-access-control/overview.md)  

