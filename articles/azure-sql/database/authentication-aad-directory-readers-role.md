---
title: Роль "читатели каталога" в Azure Active Directory для Azure SQL
description: Приложения Azure AD (субъекты-службы) поддерживают создание пользователей Azure AD в базе данных SQL Azure, Управляемый экземпляр Azure SQL и Azure синапсе Analytics.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: a517a4b14a64be2b9bf12270eef3d08e14d8f3c8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556328"
---
# <a name="directory-readers-role-in-azure-active-directory-for-azure-sql"></a>Роль "читатели каталога" в Azure Active Directory для Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> Эта функция в этой статье доступна в **общедоступной предварительной версии**.

Azure Active Directory (Azure AD) предоставила [Использование облачных групп для управления назначениями ролей в Azure Active Directory (Предварительная версия)](../../active-directory/users-groups-roles/roles-groups-concept.md). Это позволяет назначать роли Azure AD группам.

При включении [управляемого удостоверения](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) для базы данных SQL Azure, azure SQL управляемый экземпляр или Azure синапсе Analytics роль " [**читатели каталога**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) Azure AD" должна быть назначена удостоверению, чтобы разрешить доступ на чтение к [API Graph Azure AD](../../active-directory/develop/active-directory-graph-api.md). Управляемое удостоверение базы данных SQL и Azure синапсе называется удостоверением сервера. Управляемое удостоверение SQL Управляемый экземпляр называется удостоверением управляемого экземпляра и автоматически назначается при создании экземпляра. Дополнительные сведения о назначении удостоверения сервера для базы данных SQL или Azure синапсе см. в статье [Включение субъектов-служб для создания пользователей Azure AD](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users).

Роль " **читатели каталога** " необходима для:

- Создание имен входа Azure AD для SQL Управляемый экземпляр
- Олицетворение пользователей Azure AD в Azure SQL
- Миграция SQL Server пользователей, использующих проверку подлинности Windows, в SQL Управляемый экземпляр с проверкой подлинности Azure AD (с помощью команды [ALTER USER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-mi-current#d-map-the-user-in-the-database-to-an-azure-ad-login-after-migration) )
- Изменение администратора Azure AD для Управляемый экземпляр SQL
- Разрешение [субъектов-служб (приложений)](authentication-aad-service-principal.md) создавать пользователей Azure AD в Azure SQL

## <a name="assigning-the-directory-readers-role"></a>Назначение роли читателей каталога

Чтобы назначить роль " [**читатели каталога**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) " удостоверению, требуется пользователь с разрешениями администратора [глобального администратора](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) или [привилегированной роли](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) . Пользователи, которые часто управляют или развертывают базу данных SQL, SQL Управляемый экземпляр или Azure синапсе, могут не иметь доступа к этим ролям с высоким уровнем привилегий. Это часто может привести к осложнениям для пользователей, которые создают незапланированные ресурсы SQL Azure или нуждаются в помощи членов ролей с высоким уровнем привилегий, которые часто недоступны в крупных организациях.

Для Управляемый экземпляр SQL роль " **читатели каталога** " должна быть назначена удостоверению управляемого экземпляра, прежде чем вы сможете [настроить администратора Azure AD для управляемого экземпляра](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance). 

Назначение роли " **чтение каталога** " идентификатору сервера не требуется для базы данных SQL или Azure синапсе при настройке администратора Azure AD для логического сервера. Однако, чтобы включить создание объектов Azure AD в базе данных SQL или Azure синапсе от имени приложения Azure AD, требуется роль " **читатели каталога** ". Если роль не назначена удостоверению логического сервера SQL, создание пользователей Azure AD в Azure SQL завершится ошибкой. Дополнительные сведения см. в статье [Субъект-служба Azure Active Directory с SQL Azure](authentication-aad-service-principal.md).

## <a name="granting-the-directory-readers-role-to-an-azure-ad-group"></a>Предоставление роли "читатели каталога" группе Azure AD

Сейчас в **общедоступной предварительной версии**вы можете создать группу Azure AD с правами [глобального администратора](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) или [администратора привилегированных ролей](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) и назначить ей разрешение [**чтение каталога**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) . Это позволит получить доступ к API Graph Azure AD для членов этой группы. Кроме того, пользователи Azure AD, являющиеся владельцами этой группы, могут назначать новые члены для этой группы, включая удостоверения логических серверов Azure SQL.

Этому решению по-прежнему требуется пользователь с высоким уровнем прав (глобальный администратор или администратор привилегированных ролей) для создания группы и назначения пользователей в качестве однократного действия, но владельцы групп Azure AD смогут назначать дополнительные участники. Это избавляет от необходимости использования пользователя с высоким уровнем привилегий в будущем для настройки всех баз данных SQL, управляемых экземпляров SQL или серверов Azure синапсе в своем клиенте Azure AD.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство. Назначение роли "читатели каталога" группе Azure AD и управление назначениями ролей](authentication-aad-directory-readers-role-tutorial.md)
