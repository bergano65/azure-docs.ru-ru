---
title: Azure Active Directory субъекта-службы с помощью Azure SQL
description: Приложения Azure AD (субъекты-службы) поддерживают создание пользователей Azure AD в базе данных SQL Azure, Управляемый экземпляр Azure SQL и Azure синапсе Analytics.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 07/27/2020
ms.openlocfilehash: bfe609dc480dcdb71f162ca1bfd383a27257fedc
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321706"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>Azure Active Directory субъекта-службы с помощью Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Поддержка создания пользователей Azure Active Directory (Azure AD) в базе данных SQL Azure (SQL DB) и [Azure синапсе Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) от имени приложений Azure AD (субъектов-служб) в настоящее время **доступна в общедоступной предварительной версии**.

> [!NOTE]
> Эта функция уже поддерживается для SQL Управляемый экземпляр.

## <a name="service-principal-azure-ad-applications-support"></a>Поддержка субъекта-службы (приложения Azure AD)

Эта статья относится к приложениям, интегрированным с Azure AD, и является частью регистрации Azure AD. Этим приложениям часто требуется проверка подлинности и авторизация доступа к Azure SQL для выполнения различных задач. Эта функция в **общедоступной предварительной версии** теперь позволяет субъектам-службам создавать пользователей Azure AD в базе данных SQL и Azure синапсе. Произошло ограничение, препятствующее созданию объектов Azure AD от имени удаленных приложений Azure AD.

При регистрации приложения Azure AD с помощью портал Azure или команды PowerShell в клиенте Azure AD создаются два объекта:

- объект приложения;
- объект субъекта-службы.

Дополнительные сведения о приложениях Azure AD см. [в разделе объекты приложения и субъекта-службы в Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md) и [Создайте субъект-службу Azure с Azure PowerShell](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.2.0).

База данных SQL, Azure синапсе и SQL Управляемый экземпляр поддерживают следующие объекты Azure AD:

- Пользователи Azure AD (управляемые, Федеративные и гостевые)
- Группы Azure AD (управляемые и Федеративные)
-  Приложения Azure AD 

`CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER`Теперь для базы данных SQL и Azure синапсе поддерживается команда T-SQL от имени приложения Azure AD.

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>Функциональные возможности создания пользователей Azure AD с помощью субъектов-служб

Поддержка этой функции полезна в процессах автоматизации приложений Azure AD, где объекты Azure AD создаются и поддерживаются в базе данных SQL и Azure синапсе без вмешательства человека. Субъекты-службы могут быть администратором Azure AD для логического сервера SQL в составе группы или отдельного пользователя. Приложение может автоматизировать создание объектов Azure AD в базе данных SQL и Azure синапсе при выполнении от имени системного администратора и не требует каких-либо дополнительных прав SQL. Это позволяет полностью автоматизировать создание пользователей базы данных. Эта функция также поддерживается для управляемого системой удостоверения и управляемого удостоверения, назначенного пользователем. Дополнительные сведения см. в статье [что такое управляемые удостоверения для ресурсов Azure?](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="enable-service-principals-to-create-azure-ad-users"></a>Включение субъектов-служб для создания пользователей Azure AD

Чтобы включить создание объектов Azure AD в базе данных SQL и Azure синапсе от имени приложения Azure AD, необходимы следующие параметры:

1. Назначение удостоверения сервера
    - Для нового логического сервера SQL Azure выполните следующую команду PowerShell:
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    Дополнительные сведения см. в описании команды [New-азсклсервер](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlserver) .

    - Для существующих логических серверов Azure SQL выполните следующую команду:
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    Дополнительные сведения см. в описании команды [Set-азсклсервер](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver) .

    - Чтобы проверить, назначено ли удостоверение сервера серверу, выполните команду Get-Азсклсервер.

    > [!NOTE]
    > Удостоверение сервера можно также назначить с помощью команд интерфейса командной строки. Дополнительные сведения см. в разделе [AZ SQL Server Create](https://docs.microsoft.com/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) и [AZ SQL Server Update](https://docs.microsoft.com/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-update).

2. Предоставьте службе " [**читатели каталога**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) Azure AD" разрешение на доступ к удостоверению сервера, созданному или назначенному серверу.
    - Чтобы предоставить это разрешение, следуйте описанию, используемому для SQL Управляемый экземпляр, которое доступно в следующей статье: [предоставление администратора Azure AD (SQL управляемый экземпляр)](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance)
    - Пользователь Azure AD, предоставляющий это разрешение, должен входить в роль администратора **глобального администратора** Azure AD или **привилегированных ролей** .

> [!IMPORTANT]
> Шаги 1 и 2 должны выполняться в указанном порядке. Сначала создайте или назначьте удостоверение сервера, а затем предоставьте разрешение на [**чтение каталога**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) . Пропуск одного из этих шагов или и того и другого приведет к ошибке выполнения во время создания объекта Azure AD в Azure SQL от имени приложения Azure AD. Пошаговые инструкции по созданию пользователя Azure AD от имени приложения Azure AD см. в статье [учебник. Создание пользователей Azure AD с помощью приложений Azure AD](authentication-aad-service-principal-tutorial.md).

## <a name="troubleshooting-and-limitations-for-public-preview"></a>Устранение неполадок и ограничения для общедоступной предварительной версии

- При создании объектов Azure AD в Azure SQL от имени приложения Azure AD без включения удостоверения сервера и предоставления разрешений на **чтение каталога** операция завершится со следующими возможными ошибками. Ниже приведен пример ошибки для выполнения команды PowerShell для создания пользователя базы данных SQL `myapp` в статье [учебник. Создание пользователей Azure AD с помощью приложений Azure AD](authentication-aad-service-principal-tutorial.md).
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved.`
    - `User or server identity does not have permission to read from Azure Active Directory.`
      - В описанной выше ошибке выполните действия, чтобы [назначить удостоверение логическому серверу SQL Azure](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server) и [назначить ему разрешение на чтение каталога для удостоверения логического сервера SQL](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity).
    > [!NOTE]
    > Приведенные выше сообщения об ошибках будут изменены до общедоступной версии функции, чтобы ясно определить требования недостающей настройки для поддержки приложений Azure AD.
- Настройка приложения Azure AD в качестве администратора Azure AD для Управляемый экземпляр SQL поддерживается только с помощью команды CLI, а команда PowerShell — с помощью команды [AZ. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) или более поздней версии. Дополнительные сведения см. в разделе команды [AZ SQL MI AD-Admin Create](https://docs.microsoft.com/cli/azure/sql/mi/ad-admin?view=azure-cli-latest#az-sql-mi-ad-admin-create) и [Set-азсклинстанцеактиведиректорядминистратор](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) . 
    - Если вы хотите использовать портал Azure для SQL Управляемый экземпляр, чтобы задать администратора Azure AD, можно решить эту проблему, создав группу Azure AD. Затем добавьте субъект-службу (приложение Azure AD) в эту группу и задайте эту группу в качестве администратора Azure AD для Управляемый экземпляр SQL.
    - Настройка субъекта-службы (приложения Azure AD) в качестве администратора Azure AD для базы данных SQL и Azure синапсе поддерживается с помощью команд портал Azure, [PowerShell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse)и [CLI](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse) .
- Использование приложения Azure AD с субъектом-службой из другого клиента Azure AD завершится ошибкой при доступе к базе данных SQL или Управляемый экземпляр SQL, созданной в другом клиенте. Субъект-служба, назначенный этому приложению, должен принадлежать тому же клиенту, что и логический сервер SQL или Управляемый экземпляр.
- [AZ. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) Module или более поздней версии требуется при использовании PowerShell для настройки отдельного приложения Azure AD в качестве администратора Azure AD для SQL Azure. Убедитесь, что вы выполнили обновление до последней версии модуля.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство. Создание пользователей Azure AD с помощью приложений Azure AD](authentication-aad-service-principal-tutorial.md)


