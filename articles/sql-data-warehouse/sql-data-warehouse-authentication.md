---
title: Аутентификация
description: Узнайте, как пройти аутентификацию в хранилище данных SQL Azure с помощью аутентификации Azure Active Directory (AAD) или SQL Server.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: fda29e432fbd952261893f3c32a4df7b9990ae66
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692931"
---
# <a name="authenticate-to-azure-sql-data-warehouse"></a>Аутентификация в хранилище данных SQL Azure
Узнайте, как пройти аутентификацию в хранилище данных SQL Azure с помощью аутентификации Azure Active Directory (AAD) или SQL Server.

Для подключения к хранилищу данных SQL необходимо передавать учетные данные безопасности для аутентификации. После установки подключения некоторые его параметры настраиваются при установке сеанса запроса.  

Дополнительные сведения о безопасности и разрешении подключений к хранилищу данных см. в статье [Защита базы данных в хранилище данных SQL][Secure a database in SQL Data Warehouse].

## <a name="sql-authentication"></a>Аутентификация SQL
Для подключения к хранилищу данных SQL необходимо предоставить следующие сведения.

* Полное имя сервера
* Тип проверки подлинности SQL
* Имя пользователя
* Пароль
* База данных по умолчанию (необязательно)

По умолчанию устанавливается подключение к базе данных *master* , а не к пользовательской базе данных. Для подключения к пользовательской базе данных можно выполнить одно из следующих действий:

* Укажите базу данных по умолчанию при регистрации сервера в обозревателе объектов SQL Server в SSDT, SSMS или в строке подключения приложения. Например, добавьте параметр InitialCatalog для подключения ODBC.
* Выделите пользовательскую базу данных перед созданием сеанса в SSDT.

> [!NOTE]
> Использование инструкции Transact-SQL **USE MyDatabase;** для изменения базы данных, к которой осуществляется подключение, не поддерживается. Инструкции по подключению к хранилищу данных SQL с помощью SSDT см. в статье [Запросы к хранилищу данных SQL Azure (Visual Studio)][Query with Visual Studio].
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Аутентификация Azure Active Directory (AAD)
[Azure Active Directoryная][What is Azure Active Directory] проверка подлинности — это механизм подключения к хранилище данных SQL Microsoft Azure с помощью удостоверений в Azure Active Directory (Azure AD). С помощью аутентификации Azure Active Directory можно централизованно управлять удостоверениями пользователей базы данных и другими службами Майкрософт. Централизованное управление удостоверениями позволяет использовать единое расположение для управления пользователями хранилища данных SQL и упрощает управление разрешениями. 

### <a name="benefits"></a>Преимущества
Преимущества Azure Active Directory:

* наличие альтернативы аутентификации SQL Server;
* возможность остановить увеличение количества пользователей на серверах баз данных;
* изменение паролей в одном расположении;
* управление разрешениями базы данных с помощью внешних групп (AAD);
* возможность исключить хранение паролей с помощью встроенной проверки подлинности Windows и других видов аутентификации, поддерживаемых Azure Active Directory;
* для проверки подлинности удостоверений на уровне базы данных используются данные пользователей автономной базы данных;
* поддержка аутентификации на основе маркеров для приложений, подключающихся к хранилищу данных SQL;
* Поддерживает многофакторную проверку подлинности с помощью Active Directory универсальной аутентификации для различных средств, включая [SQL Server Management Studio](../sql-database/sql-database-ssms-mfa-authentication.md) и [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/azure-active-directory?toc=/azure/sql-data-warehouse/toc.json).

> [!NOTE]
> Azure Active Directory является сравнительно новым компонентом и имеет некоторые ограничения. Чтобы убедиться, что Azure Active Directory подходит для вашей среды, ознакомьтесь с разделом [Функции и ограничения Azure AD][Azure AD features and limitations], в частности с его подразделом "Дополнительные замечания".
> 
> 

### <a name="configuration-steps"></a>Этапы настройки
Следуйте приведенным ниже инструкциям, чтобы настроить аутентификацию Azure Active Directory.

1. Создание и заполнение каталога Azure Active Directory
2. Связывание каталога Active Directory с подпиской Azure или изменение каталога, который сейчас связан с этой подпиской (этот шаг можно пропустить)
3. Создание администратора Azure Active Directory для хранилища данных SQL Azure
4. Настройка клиентских компьютеров
5. Создание пользователей автономной базы данных в базе данных, сопоставленной с удостоверениями Azure AD
6. Подключение к хранилищу данных с помощью удостоверений Azure AD

Сейчас пользователи Azure Active Directory не отображаются в обозревателе объектов SSDT. Сведения о пользователях можно просмотреть в файле [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

### <a name="find-the-details"></a>Поиск подробных сведений
* Процедуры настройки и использования аутентификации Azure Active Directory практически идентичны для базы данных SQL Azure и хранилища данных SQL Azure. Выполните действия, описанные в разделе [Подключение к Базе данных SQL или хранилищу данных SQL c использованием проверки подлинности Azure Active Directory](../sql-database/sql-database-aad-authentication.md).
* Создайте пользовательские роли базы данных и назначьте их пользователям. Затем предоставьте ролям управляемые разрешения. Дополнительные сведения см. в разделе [Приступая к работе с разрешениями Database Engine](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Дальнейшие действия
Чтобы приступить к отправке запросов к хранилищу данных с помощью Visual Studio и других приложений, ознакомьтесь с разделом [Запросы к хранилищу данных SQL Azure (Visual Studio)][Query with Visual Studio].

<!-- Article references -->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]:../active-directory/fundamentals/active-directory-whatis.md
[Azure AD features and limitations]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
