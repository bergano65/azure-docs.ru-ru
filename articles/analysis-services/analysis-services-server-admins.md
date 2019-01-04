---
title: Управление администраторами серверов в службах Azure Analysis Services | Документы Майкрософт
description: Узнайте, как управлять администраторами серверов служб Analysis Services в Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5afc434ccd7a41c6fa1f4fec300941458c84889e
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53629580"
---
# <a name="manage-server-administrators"></a>Управление администраторами серверов

Администраторами сервера должны быть допустимые пользователи или группы безопасности в Azure Active Directory (Azure AD) для клиента, в котором размещен сервер. Для управления администраторами сервера можно использовать элемент **Администраторы служб Analysis Services** для сервера на портале Azure или свойств сервера в SSMS, PowerShell или REST API. 

> [!NOTE]
> У групп безопасности свойство `MailEnabled` должно иметь значение `True`.

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

Используйте командлет [New-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver), чтобы указать параметр администратора при создании нового сервера. <br>
Используйте командлет [Set-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver), чтобы изменить параметр администратора на существующем сервере.

## <a name="rest-api"></a>REST API

Используйте команду [Create](https://docs.microsoft.com/rest/api/analysisservices/servers/create) для указания свойства asAdministrator при создании нового сервера. <br>
Используйте команду [Update](https://docs.microsoft.com/rest/api/analysisservices/servers/update) для указания свойства asAdministrator при изменении существующего сервера. <br>



## <a name="next-steps"></a>Дополнительная информация 

[Аутентификация и пользовательские разрешения](analysis-services-manage-users.md)  
[Управление ролями и пользователями базы данных](analysis-services-database-users.md)  
[Управление доступом на основе ролей](../role-based-access-control/overview.md)  

