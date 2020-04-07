---
title: Аутентификация
description: Узнайте, как проверить подлинность в Azure Synapse Analytics, используя active Directory (Azure AD) или аутентификацию сервера S'L.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: fcd9607cd630f41c67c655c2e8016254600b52e2
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745393"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>Проверка подлинности аналитики Azure Synapse

Узнайте, как проверить подлинность для s'L Analytics в Azure Synapse с помощью Azure Active Directory (AAD) или проверки подлинности сервера S'L.

Для подключения к пулу S'L необходимо передать учетные данные безопасности для целей проверки подлинности. После установки подключения некоторые его параметры настраиваются при установке сеанса запроса.  

Для получения дополнительной информации о безопасности и о том, как включить подключение к вашему складу данных, [см.](sql-data-warehouse-overview-manage-security.md)

## <a name="sql-authentication"></a>Проверка подлинности SQL

Для подключения к пулу S'L необходимо предоставить следующую информацию:

* Полное имя сервера
* Тип проверки подлинности SQL
* Имя пользователя
* Пароль
* База данных по умолчанию (необязательно)

По умолчанию ваше соединение подключается к *основной* базе данных, а не к базе данных пользователей. Для подключения к пользовательской базе данных можно выполнить одно из следующих действий:

* Укажите базу данных по умолчанию при регистрации сервера в обозревателе объектов SQL Server в SSDT, SSMS или в строке подключения приложения. Например, добавьте параметр InitialCatalog для подключения ODBC.
* Выделите пользовательскую базу данных перед созданием сеанса в SSDT.

> [!NOTE]
> Использование инструкции Transact-SQL **USE MyDatabase;** для изменения базы данных, к которой осуществляется подключение, не поддерживается. Для руководства, подключающегося к пулу S'L с SSDT, обратитесь к запросу со статьей [Visual Studio.](sql-data-warehouse-query-visual-studio.md)

## <a name="azure-active-directory-aad-authentication"></a>Аутентификация Azure Active Directory (AAD)

Активная аутентификация [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) — это механизм подключения к пулу S'L с помощью идентификаторов в Active Directory Azure (Azure AD). С помощью аутентификации Azure Active Directory можно централизованно управлять удостоверениями пользователей базы данных и другими службами Майкрософт. Управление центральным идентификатором предоставляет единое место для управления пользователями Azure Synapse и упрощает управление разрешениями.

### <a name="benefits"></a>Преимущества

Преимущества Azure Active Directory:

* наличие альтернативы аутентификации SQL Server;
* возможность остановить увеличение количества пользователей на серверах баз данных;
* изменение паролей в одном расположении;
* Управление разрешениями базы данных с помощью внешних (Azure AD) групп.
* возможность исключить хранение паролей с помощью встроенной проверки подлинности Windows и других видов аутентификации, поддерживаемых Azure Active Directory;
* для проверки подлинности удостоверений на уровне базы данных используются данные пользователей автономной базы данных;
* Поддерживает проверку подлинности токенов для приложений, подключающихся к пулу S'L.
* Поддерживает многофакторную аутентификацию с помощью универсальная аутентификация Active Directory для различных инструментов, включая [студию управления серверами,](../../sql-database/sql-database-ssms-mfa-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) а также [инструменты серверных данных S'L.](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

> [!NOTE]
> Azure Active Directory является сравнительно новым компонентом и имеет некоторые ограничения. Чтобы убедиться, что Azure Active Directory подходит для вашей среды, ознакомьтесь с разделом [Функции и ограничения Azure AD](../../sql-database/sql-database-aad-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#azure-ad-features-and-limitations), в частности с его подразделом "Дополнительные замечания".

### <a name="configuration-steps"></a>Этапы настройки

Следуйте приведенным ниже инструкциям, чтобы настроить аутентификацию Azure Active Directory.

1. Создание и заполнение каталога Azure Active Directory
2. Связывание каталога Active Directory с подпиской Azure или изменение каталога, который сейчас связан с этой подпиской (этот шаг можно пропустить)
3. Создание администратора активного каталога Azure для Azure Synapse
4. Настройка клиентских компьютеров
5. Создание пользователей автономной базы данных в базе данных, сопоставленной с удостоверениями Azure AD
6. Подключитесь к пулу S'L, используя идентификаторы Azure AD

Сейчас пользователи Azure Active Directory не отображаются в обозревателе объектов SSDT. Сведения о пользователях можно просмотреть в файле [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#azure-ad-features-and-limitations).

### <a name="find-the-details"></a>Поиск подробных сведений

* Шаги по настройке и использованию аутентификации active Directory Azure практически идентичны для базы данных Azure S'L и аналитики S'L в Azure Synapse. Следуйте подробным шагам в теме [Подключение к базе данных S'L или пулу S'L, используя активную проверку подлинности каталогов Azure.](../../sql-database/sql-database-aad-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* Создайте пользовательские роли базы данных и назначьте их пользователям. Затем предоставьте ролям управляемые разрешения. Дополнительные сведения см. в разделе [Приступая к работе с разрешениями Database Engine](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Дальнейшие действия

Чтобы начать запрос с Visual Studio и другими приложениями, [см.](sql-data-warehouse-query-visual-studio.md)
