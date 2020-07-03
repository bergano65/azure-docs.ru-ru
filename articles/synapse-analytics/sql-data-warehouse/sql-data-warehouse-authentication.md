---
title: Проверка подлинности
description: Узнайте, как выполнять аутентификацию в Azure синапсе Analytics с помощью Azure Active Directory (Azure AD) или проверки подлинности SQL Server.
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
ms.openlocfilehash: ed8dd902ac490b4e6a0f172029bf3ffa1d44acee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81251849"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>Аутентификация в Azure синапсе Analytics

Узнайте, как выполнять аутентификацию в SQL Analytics в Azure синапсе с помощью Azure Active Directory (AAD) или проверки подлинности SQL Server.

Для подключения к пулу SQL необходимо передать учетные данные безопасности в целях проверки подлинности. После установки подключения некоторые его параметры настраиваются при установке сеанса запроса.  

Дополнительные сведения о безопасности и о том, как включить подключения к хранилищу данных, см. в разделе [Защита документации по базе данных](sql-data-warehouse-overview-manage-security.md).

## <a name="sql-authentication"></a>Проверка подлинности SQL

Чтобы подключиться к пулу SQL, необходимо предоставить следующие сведения:

* Полное имя сервера
* Тип проверки подлинности SQL
* Имя пользователя
* Пароль
* База данных по умолчанию (необязательно)

По умолчанию подключение подключается к базе данных *master* , а не к пользовательской базе данных. Для подключения к пользовательской базе данных можно выполнить одно из следующих действий:

* Укажите базу данных по умолчанию при регистрации сервера в обозревателе объектов SQL Server в SSDT, SSMS или в строке подключения приложения. Например, добавьте параметр InitialCatalog для подключения ODBC.
* Выделите пользовательскую базу данных перед созданием сеанса в SSDT.

> [!NOTE]
> Использование инструкции Transact-SQL **USE MyDatabase;** для изменения базы данных, к которой осуществляется подключение, не поддерживается. Инструкции по подключению к пулу SQL с SSDT см. в статье [запрос в Visual Studio](sql-data-warehouse-query-visual-studio.md) .

## <a name="azure-active-directory-aad-authentication"></a>Аутентификация Azure Active Directory (AAD)

[Azure Active Directoryная](../../active-directory/fundamentals/active-directory-whatis.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) проверка подлинности — это механизм подключения к пулу SQL с помощью удостоверений в Azure Active Directory (Azure AD). С помощью аутентификации Azure Active Directory можно централизованно управлять удостоверениями пользователей базы данных и другими службами Майкрософт. Централизованное управление ИДЕНТИФИКАТОРами предоставляет единое место для управления пользователями Azure синапсе и упрощает управление разрешениями.

### <a name="benefits"></a>Преимущества

Преимущества Azure Active Directory:

* наличие альтернативы аутентификации SQL Server;
* возможность остановить увеличение количества пользователей на серверах баз данных;
* изменение паролей в одном расположении;
* Управление разрешениями базы данных с помощью внешних групп (Azure AD).
* возможность исключить хранение паролей с помощью встроенной проверки подлинности Windows и других видов аутентификации, поддерживаемых Azure Active Directory;
* для проверки подлинности удостоверений на уровне базы данных используются данные пользователей автономной базы данных;
* Поддерживает проверку подлинности на основе маркеров для приложений, подключающихся к пулу SQL.
* Поддерживает многофакторную проверку подлинности с помощью Active Directory универсальной аутентификации для различных средств, включая [SQL Server Management Studio](../../sql-database/sql-database-ssms-mfa-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) и [SQL Server Data Tools](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

> [!NOTE]
> Azure Active Directory является сравнительно новым компонентом и имеет некоторые ограничения. Чтобы убедиться, что Azure Active Directory подходит для вашей среды, ознакомьтесь с разделом [Функции и ограничения Azure AD](../../sql-database/sql-database-aad-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#azure-ad-features-and-limitations), в частности с его подразделом "Дополнительные замечания".

### <a name="configuration-steps"></a>Этапы настройки

Следуйте приведенным ниже инструкциям, чтобы настроить аутентификацию Azure Active Directory.

1. Создание и заполнение каталога Azure Active Directory
2. Связывание каталога Active Directory с подпиской Azure или изменение каталога, который сейчас связан с этой подпиской (этот шаг можно пропустить)
3. Создание администратора Azure Active Directory для Azure синапсе
4. Настройка клиентских компьютеров
5. Создание пользователей автономной базы данных в базе данных, сопоставленной с удостоверениями Azure AD
6. Подключение к пулу SQL с помощью удостоверений Azure AD

Сейчас пользователи Azure Active Directory не отображаются в обозревателе объектов SSDT. Сведения о пользователях можно просмотреть в файле [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="find-the-details"></a>Поиск подробных сведений

* Действия по настройке и использованию Azure Active Directory проверки подлинности практически идентичны для базы данных SQL Azure и аналитики SQL в Azure синапсе. Выполните подробные инструкции из раздела [Подключение к базе данных SQL или пулу SQL с помощью Azure Active Directory проверки подлинности](../../sql-database/sql-database-aad-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
* Создайте пользовательские роли базы данных и назначьте их пользователям. Затем предоставьте ролям управляемые разрешения. Дополнительные сведения см. в разделе [Приступая к работе с разрешениями Database Engine](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Дальнейшие шаги

Чтобы начать выполнение запросов в Visual Studio и других приложениях, см. статью [выполнение запросов с помощью Visual Studio](sql-data-warehouse-query-visual-studio.md).
