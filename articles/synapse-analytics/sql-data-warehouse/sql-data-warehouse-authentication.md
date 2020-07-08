---
title: Аутентификация
description: Узнайте, как пройти проверку подлинности в Azure Synapse Analytics, используя проверку подлинности Azure Active Directory или SQL Server.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: 29709dc03ee3a06bdf2aec2587909a08ee13504e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85206736"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>Проверка подлинности в Azure Synapse Analytics

Узнайте, как пройти аутентификацию в хранилище данных SQL Azure с помощью проверки подлинности Azure Active Directory (AAD) или SQL Server.

Для подключения к пулу SQL необходимо передать учетные данные безопасности для проверки подлинности. После установки подключения некоторые его параметры настраиваются при установке сеанса запроса.  

Дополнительные сведения о безопасности и о том, как разрешить подключения к хранилищу данных, см. в статье [Защита базы данных в базе данных](sql-data-warehouse-overview-manage-security.md).

## <a name="sql-authentication"></a>Проверка подлинности SQL

Для подключения к пулу SQL необходимо предоставить следующие сведения.

* Полное имя сервера
* Тип проверки подлинности SQL
* Имя пользователя
* Пароль
* База данных по умолчанию (необязательно)

По умолчанию устанавливается подключение к базе данных *master*, а не к пользовательской базе данных. Для подключения к пользовательской базе данных можно выполнить одно из следующих действий:

* Укажите базу данных по умолчанию при регистрации сервера в обозревателе объектов SQL Server в SSDT, SSMS или в строке подключения приложения. Например, добавьте параметр InitialCatalog для подключения ODBC.
* Выделите пользовательскую базу данных перед созданием сеанса в SSDT.

> [!NOTE]
> Использование инструкции Transact-SQL **USE MyDatabase;** для изменения базы данных, к которой осуществляется подключение, не поддерживается. Инструкции по подключению к пулу SQL с помощью Visual Studio и SSDT см. в [этой статье](sql-data-warehouse-query-visual-studio.md).

## <a name="azure-active-directory-aad-authentication"></a>Аутентификация Azure Active Directory (AAD)

Проверка подлинности [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) — это механизм подключения к пулу SQL с помощью удостоверений в Azure Active Directory (Azure AD). С помощью аутентификации Azure Active Directory можно централизованно управлять удостоверениями пользователей базы данных и другими службами Майкрософт. Централизованное управление удостоверениями позволяет использовать единое расположение для управления пользователями Azure Synapse и упрощает управление разрешениями.

### <a name="benefits"></a>Преимущества

Преимущества Azure Active Directory:

* наличие альтернативы аутентификации SQL Server;
* возможность остановить увеличение количества пользователей на серверах;
* изменение паролей в одном расположении;
* управление разрешениями базы данных с помощью внешних групп Azure AD;
* Исключает хранение паролей путем включения встроенной проверки подлинности Windows и данные об видов проверки подлинности, поддерживаемых Azure Active Directory.
* для проверки подлинности удостоверений на уровне базы данных используются данные пользователей автономной базы данных;
* поддержка аутентификации на основе маркеров для приложений, подключающихся к пулу SQL;
* поддержка Многофакторной идентификации с помощью универсальной аутентификации Active Directory для различных инструментов, включая [SQL Server Management Studio](../../azure-sql/database/authentication-mfa-ssms-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) и [SQL Server Data Tools](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

> [!NOTE]
> Azure Active Directory является сравнительно новым компонентом и имеет некоторые ограничения. Чтобы убедиться, что Azure Active Directory подходит для вашей среды, ознакомьтесь с разделом [Функции и ограничения Azure AD](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#azure-ad-features-and-limitations), в частности с его подразделом "Дополнительные замечания".

### <a name="configuration-steps"></a>Этапы настройки

Следуйте приведенным ниже инструкциям, чтобы настроить аутентификацию Azure Active Directory.

1. Создание и заполнение каталога Azure Active Directory
2. Необязательное действие: Свяжите или измените каталог Active Directory, который сейчас связан с вашей подпиской Azure.
3. Создание администратора Azure Active Directory для хранилища данных Azure Synapse
4. Настройка клиентских компьютеров
5. Создание пользователей автономной базы данных в базе данных, сопоставленной с удостоверениями Azure AD
6. Подключение к пулу SQL с помощью удостоверений Azure AD

Сейчас пользователи Azure Active Directory не отображаются в обозревателе объектов SSDT. Сведения о пользователях можно просмотреть в файле [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="find-the-details"></a>Поиск подробных сведений

* Процедуры настройки и использования аутентификации Azure Active Directory практически идентичны для базы данных SQL Azure и Synapse SQL в Azure Synapse. Выполните действия, описанные в разделе [Подключение к Базе данных SQL или пулу SQL c использованием проверки подлинности Azure Active Directory](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
* Создайте пользовательские роли базы данных и назначьте их пользователям. Затем предоставьте ролям управляемые разрешения. Дополнительные сведения см. в разделе [Приступая к работе с разрешениями Database Engine](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Дальнейшие действия

Чтобы приступить к отправке запросов с помощью Visual Studio и других приложений, см. статью [Connect to Azure Synapse Analytics with Visual Studio and SSDT](sql-data-warehouse-query-visual-studio.md) (Подключение к Azure Synapse Analytics с помощью Visual Studio и SSDT).
