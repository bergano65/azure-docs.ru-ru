---
title: Аутентификация в хранилище данных SQL Azure | Документация Майкрософт
description: Узнайте, как пройти аутентификацию в хранилище данных SQL Azure с помощью аутентификации Azure Active Directory (AAD) или SQL Server.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/12/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: ab439566da38a537534fc10e70058ab57005ce22
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183804"
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
> Использование инструкции Transact-SQL **USE MyDatabase;** для изменения базы данных, к которой осуществляется подключение, не поддерживается. Инструкции по подключению к хранилищу данных SQL с помощью Visual Studio и SSDT см. в [этой статье][Query with Visual Studio].
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Аутентификация Azure Active Directory (AAD)
Проверка подлинности [Azure Active Directory][What is Azure Active Directory] — это механизм подключения к хранилищу данных SQL Microsoft Azure с помощью удостоверений в Azure Active Directory (Azure AD). С помощью аутентификации Azure Active Directory можно централизованно управлять удостоверениями пользователей базы данных и другими службами Майкрософт. Централизованное управление удостоверениями позволяет использовать единое расположение для управления пользователями хранилища данных SQL и упрощает управление разрешениями. 

### <a name="benefits"></a>Преимущества
Преимущества Azure Active Directory:

* наличие альтернативы аутентификации SQL Server;
* возможность остановить увеличение количества пользователей на серверах баз данных;
* изменение паролей в одном расположении;
* управление разрешениями базы данных с помощью внешних групп (AAD);
* возможность исключить хранение паролей с помощью встроенной проверки подлинности Windows и других видов аутентификации, поддерживаемых Azure Active Directory;
* для проверки подлинности удостоверений на уровне базы данных используются данные пользователей автономной базы данных;
* поддержка аутентификации на основе маркеров для приложений, подключающихся к хранилищу данных SQL;
* поддержка Многофакторной Идентификации с помощью универсальной аутентификации Active Directory для SQL Server Management Studio. Описание службы Многофакторной идентификации см. в статье [Поддержка SSMS в Azure AD MFA для базы данных SQL и хранилища данных SQL](../sql-database/sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Azure Active Directory является сравнительно новым компонентом и имеет некоторые ограничения. Чтобы убедиться, что Azure Active Directory подходит для вашей среды, ознакомьтесь с разделом [Функции и ограничения Azure AD][Azure AD features and limitations], в частности с его подразделом "Дополнительные замечания".
> 
> 

### <a name="configuration-steps"></a>Этапы настройки
Следуйте приведенным ниже инструкциям, чтобы настроить аутентификацию Azure Active Directory.

1. Создание и заполнение каталога Azure Active Directory
2. Необязательно: Свяжите или измените каталог Active Directory, который сейчас связан с вашей подпиской Azure.
3. Создание администратора Azure Active Directory для хранилища данных SQL Azure
4. Настройка клиентских компьютеров
5. Создание пользователей автономной базы данных в базе данных, сопоставленной с удостоверениями Azure AD
6. Подключение к хранилищу данных с помощью удостоверений Azure AD

Сейчас пользователи Azure Active Directory не отображаются в обозревателе объектов SSDT. Сведения о пользователях можно просмотреть в файле [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

### <a name="find-the-details"></a>Поиск подробных сведений
* Процедуры настройки и использования аутентификации Azure Active Directory практически идентичны для базы данных SQL Azure и хранилища данных SQL Azure. Выполните действия, описанные в разделе [Подключение к Базе данных SQL или хранилищу данных SQL c использованием проверки подлинности Azure Active Directory](../sql-database/sql-database-aad-authentication.md).
* Создайте пользовательские роли базы данных и назначьте их пользователям. Затем предоставьте ролям управляемые разрешения. Дополнительные сведения см. в разделе [Приступая к работе с разрешениями Database Engine](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Дальнейшие действия
Чтобы приступить к отправке запросов к хранилищу данных с помощью Visual Studio и других приложений, см. статью [Подключение к хранилищу данных SQL с помощью Visual Studio и SSDT][Query with Visual Studio].

<!-- Article references -->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]:../active-directory/fundamentals/active-directory-whatis.md
[Azure AD features and limitations]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
