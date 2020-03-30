---
title: Условный доступ
description: Узнайте, как настроить условный доступ для базы данных Azure S'L и Azure Synapse.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124907"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>Условный доступ (MFA) с базой данных Azure S'L и аналитикой Azure Synapse

База данных Azure [S'L,](sql-database-technical-overview.md) [Управляемые экземпляры](sql-database-managed-instance.md)и [Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) поддерживают Microsoft Conditional Access. 

> [!NOTE]
> Эта тема применима как к серверу Azure S'L, так и к базе данных S'L и Azure Synapse, которые создаются на сервере Azure S'L. Для простоты используется база данных S'L, когда речь идет как о базе данных S'L, так и о Azure Synapse.

Ниже показано, как настроить базу данных SQL для принудительного применения политики условного доступа.  

## <a name="prerequisites"></a>Предварительные требования  
- Необходимо настроить базу данных или пул с помощью S'L в Azure Synapse для поддержки проверки подлинности Active Directory Azure. Для конкретных шагов см. [Налажёте и управляйте аутентификацией активных каталогов Azure с помощью базы данных S'L или Azure Synapse.](sql-database-aad-authentication-configure.md)  
- При включении многофакторной идентификации необходимо выполнить подключение с помощью поддерживаемого средства, например последней версии SSMS. Дополнительные сведения см. в разделе [Настройка многофакторной проверки подлинности в Базе данных SQL Azure для SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Настройка центра сертификации для базы данных или хранилища данных SQL Azure  
1. Вопийте на портале, выберите **Активный каталог Azure,** а затем выберите **Условный доступ.** Дополнительные сведения см. в статье [Техническая информация об условном доступе в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![Лезвие условного доступа](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. В колонке **политик условного доступа** щелкните **Создать политику**, укажите имя, а затем щелкните **Configure rules** (Настройка правил).  
3. В соответствии с **назначениями,** выберите **пользователей и группы,** проверьте **Выберите пользователей и группы,** а затем выберите пользователя или группу для условного доступа. Щелкните **Выбрать** , а затем **Готово** ,чтобы подтвердить выбор.  
   ![выбор пользователей и групп](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Выберите **Облачные приложения** и щелкните **Выбрать приложения**. Вы видите все приложения, доступные для условного доступа. Выберите **База данных SQL Azure**, в нижней области щелкните **Выбрать**, а затем щелкните **Готово**.  
   ![выбор базы данных SQL](./media/sql-database-conditional-access/select-sql-database.png)  
   Если вы не можете найти **базу данных Azure S'L,** перечисленные в следующем третьем скриншоте, выполните следующие шаги:   
   - Войдите на свой экземпляр базы данных или хранилища данных SQL Azure с помощью SSMS, используя учетную запись администратора AAD.  
   - Выполните процедуру `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Вопийте в AAD и убедитесь, что база данных Azure S'L и Azure Synapse указаны в приложениях в вашем AAD.  

5. Выберите **Элементы управления доступом**, выберите **Предоставить**, а затем просмотрите политику, которую вы хотите применить. Для этого примера мы выберем **Требовать многофакторную проверку подлинности**.  
   ![выбор предоставления доступа](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Сводка  
Выбранное приложение (база данных SQL Azure), позволяющее подключаться к базе данных или хранилищу данных SQL Azure, с помощью Azure AD Premium, теперь принудительно применяет выбранную политику условного доступа **Требовать многофакторную проверку подлинности.**  
Для вопросов о базе данных Azure S'L и Azure MFAforSQLDB@microsoft.comSynapse относительно многофакторной аутентификации, свяжитесь с нами.  

## <a name="next-steps"></a>Дальнейшие действия  

Ознакомьтесь с руководством [Защита базы данных SQL Azure](sql-database-security-tutorial.md).
