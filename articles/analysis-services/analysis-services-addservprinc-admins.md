---
title: Добавление субъекта-службы к роли администратора сервера Azure Analysis Services | Документация Майкрософт
description: Узнайте, как добавить субъект-службу для автоматизации к роли администратора сервера
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5e4650d3be5ce21a49b419577eaf83225b1c0d4d
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298713"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Добавление субъекта-службы к роли администратора сервера 

 Чтобы автоматизировать задачи PowerShell, выполняемые без участия пользователя, субъекту-службе должны быть предоставлены разрешения **администратора сервера** на управляемом сервере Analysis Services. В этой статье описывается, как добавить субъект-службу к роли администратора сервера на сервере Azure AS.

## <a name="before-you-begin"></a>Перед началом работы
Прежде чем приступить к этой задаче, нужно зарегистрировать субъект-службу в Azure Active Directory.

[Создание субъекта-службы с помощью портала Azure](../active-directory/develop/howto-create-service-principal-portal.md)   
[Создание субъекта-службы с помощью PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="required-permissions"></a>Необходимые разрешения
Для выполнения этой задачи вам потребуются разрешения [администратора сервера](analysis-services-server-admins.md) на сервере Azure Analysis Services. 

## <a name="add-service-principal-to-server-administrators-role"></a>Добавление субъекта-службы к роли администратора сервера

1. В среде SSMS подключитесь к серверу Azure Analysis Services.
2. Выберите **Свойства сервера** > **Безопасность** и нажмите кнопку **Добавить**.
3. В окне **Выберите пользователя или группу** выполните поиск своего зарегистрированного приложения по имени, выберите его и нажмите кнопку **Добавить**.

    ![Поиск учетной записи субъекта-службы](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Проверьте идентификатор учетной записи субъекта-службы и нажмите кнопку **ОК**.
    
    ![Поиск учетной записи субъекта-службы](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> Для операций сервера, использующих командлеты Azure PowerShell, планировщик запуска субъекта-службы также должен принадлежать роли **владельца** для ресурса в [контроле доступа на основе РОЛЕЙ Azure (RBAC)](../role-based-access-control/overview.md). 

## <a name="related-information"></a>Связанные сведения

* [Скачивание модуля PowerShell SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Скачивание SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


