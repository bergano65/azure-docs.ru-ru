---
title: Условный доступ
description: Узнайте, как настроить условный доступ для базы данных SQL Azure и Azure синапсе.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sql-data-warehouse
ms.date: 02/06/2020
tag: azure-synpase
ms.openlocfilehash: cd56ccf2e6a4ceb0d81c25b5b9e795176be66b77
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80124907"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>Условный доступ (MFA) с базой данных SQL Azure и Azure синапсе Analytics

[База данных SQL](sql-database-technical-overview.md)azure, [управляемый экземпляр](sql-database-managed-instance.md)и [Azure синапсе](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) поддерживают условный доступ Майкрософт. 

> [!NOTE]
> Эта статья относится к Azure SQL Server и к базе данных SQL и Azure синапсе, созданным на сервере Azure SQL. Для простоты база данных SQL используется при обращении к базе данных SQL и Azure синапсе.

Ниже показано, как настроить базу данных SQL для принудительного применения политики условного доступа.  

## <a name="prerequisites"></a>Предварительные условия  
- Необходимо настроить базу данных SQL или пул SQL в Azure синапсе для поддержки проверки подлинности Azure Active Directory. Дополнительные сведения см. в статье [Настройка проверки подлинности Azure Active Directory с помощью базы данных SQL или Azure синапсе и управление ею](sql-database-aad-authentication-configure.md).  
- При включении многофакторной идентификации необходимо выполнить подключение с помощью поддерживаемого средства, например последней версии SSMS. Дополнительные сведения см. в разделе [Настройка многофакторной проверки подлинности в Базе данных SQL Azure для SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Настройка центра сертификации для базы данных или хранилища данных SQL Azure  
1. Войдите на портал, выберите **Azure Active Directory**, а затем выберите **Условный доступ**. Дополнительные сведения см. в статье [Техническая информация об условном доступе в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![Колонка условного доступа](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. В колонке **политик условного доступа** щелкните **Создать политику**, укажите имя, а затем щелкните **Configure rules** (Настройка правил).  
3. В разделе **назначения**выберите **Пользователи и группы**, установите флажок **выбрать пользователей и группы**, а затем выберите пользователя или группу для условного доступа. Щелкните **Выбрать** , а затем **Готово** ,чтобы подтвердить выбор.  
   ![выбор пользователей и групп](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Выберите **Облачные приложения** и щелкните **Выбрать приложения**. Вы увидите все приложения, доступные для условного доступа. Выберите **База данных SQL Azure**, в нижней области щелкните **Выбрать**, а затем щелкните **Готово**.  
   ![выбор базы данных SQL](./media/sql-database-conditional-access/select-sql-database.png)  
   Если вы не можете найти **базу данных SQL Azure** , указанную на следующем третьем снимке экрана, выполните следующие действия.   
   - Войдите на свой экземпляр базы данных или хранилища данных SQL Azure с помощью SSMS, используя учетную запись администратора AAD.  
   - Выполните процедуру `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Войдите в AAD и убедитесь, что база данных SQL Azure и Azure синапсе указаны в приложениях в AAD.  

5. Выберите **Элементы управления доступом**, выберите **Предоставить**, а затем просмотрите политику, которую вы хотите применить. Для этого примера мы выберем **Требовать многофакторную проверку подлинности**.  
   ![выбор предоставления доступа](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Сводка  
Выбранное приложение (база данных SQL Azure), позволяющее подключаться к базе данных или хранилищу данных SQL Azure, с помощью Azure AD Premium, теперь принудительно применяет выбранную политику условного доступа **Требовать многофакторную проверку подлинности.**  
Вопросы о базе данных SQL Azure и Azure синапсе в отношении многофакторной идентификации см MFAforSQLDB@microsoft.com. по адресу.  

## <a name="next-steps"></a>Дальнейшие шаги  

Ознакомьтесь с руководством [Защита базы данных SQL Azure](sql-database-security-tutorial.md).
