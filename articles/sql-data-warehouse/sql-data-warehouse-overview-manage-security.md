---
title: Защита базы данных
description: Советы по защите базы данных в хранилище данных SQL для разработки решений.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c51a945bae7cc0b03c219bc041d64f4703baef19
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692577"
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>Защита базы данных в хранилище данных SQL
> [!div class="op_single_selector"]
> * [Обзор безопасности](sql-data-warehouse-overview-manage-security.md)
> * [Проверка подлинности](sql-data-warehouse-authentication.md)
> * [Шифрование (портал)](sql-data-warehouse-encryption-tde.md)
> * [Шифрование (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

В этой статье рассматриваются основы защиты базы данных в хранилище данных SQL. В частности, эта статья поможет приступить к работе с ресурсами для ограничения доступа, защиты данных и мониторинга действий с базой данных.

## <a name="connection-security"></a>Безопасность подключения
Под безопасностью подключения подразумеваются способы ограничения и защиты подключений к базе данных с помощью правил брандмауэра и шифрования подключения.

Правила брандмауэра используются как сервером, так и базой данных для отклонения попыток подключиться с IP-адресов, которые не были явно включены в белый список. Чтобы разрешить подключение из своего приложения или с клиентского компьютера с общедоступным IP-адресом, сначала необходимо создать правило брандмауэра уровня сервера. Для этого воспользуйтесь порталом Azure, REST API или PowerShell. В брандмауэре сервера рекомендуется максимально ограничить диапазоны разрешенных IP-адресов.  Для доступа к хранилищу данных SQL Azure с локального компьютера убедитесь, что брандмауэр сети и локального компьютера разрешает исходящие подключения по TCP-порту 1433.  

Хранилище данных SQL использует правила брандмауэра уровня сервера. Оно не поддерживает правила брандмауэра уровня базы данных. Дополнительные сведения см. в разделе [брандмауэр базы данных SQL Azure][Azure SQL Database firewall], [sp_set_firewall_rule][sp_set_firewall_rule].

Подключения к хранилищу данных SQL шифруются по умолчанию.  Изменение параметров подключения для отключения шифрования игнорируется.

## <a name="authentication"></a>Аутентификация
В процессе аутентификации предлагается подтвердить личность пользователя при подключении к базе данных. Сейчас хранилище данных SQL поддерживает аутентификацию SQL Server с использованием имени пользователя и пароля, а также Azure Active Directory. 

При создании логического сервера для базы данных вы указали имя для входа "Администратор сервера" и пароль. Используя эти учетные данные, можно выполнить проверку подлинности SQL Server для любой базы данных на этом сервере в качестве владельца базы данных (или dbo).

Но рекомендуется, чтобы пользователи вашей организации использовали для проверки подлинности другую учетную запись. Это позволит ограничить разрешения, предоставляемые приложению, и снизить риски вредоносных действий в случае, если приложение уязвимо для атак путем внедрения кода SQL. 

Чтобы создать пользователя, прошедшего аутентификацию SQL Server, подключитесь к базе данных **master** на сервере с именем для входа администратора сервера и создайте новое имя для входа на сервер.  Кроме того, рекомендуется создать в базе данных master учетную запись для пользователей хранилища данных SQL Azure. Это позволит пользователям входить в систему с помощью таких инструментов, как среда SSMS, без указания имени базы данных.  Кроме того, это позволяет использовать обозреватель объектов для просмотра всех баз данных в SQL Server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Затем подключитесь к **базе данных хранилища данных SQL** с именем для входа администратора сервера и создайте пользователя базы данных для только что созданного имени для входа на сервер.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Чтобы предоставить пользователю разрешение для выполнения дополнительных операций, таких как создание имен для входа или баз данных, назначьте ему роли `Loginmanager` и `dbmanager` в базе данных master. Дополнительные сведения об этих дополнительных ролях и проверке подлинности в базе данных SQL см. [в статье Управление базами данных и именами входа в базе данных SQL Azure][Managing databases and logins in Azure SQL Database].  Дополнительные сведения см. в статье [Подключение к хранилищу данных SQL с помощью Azure Active Directory проверки подлинности][Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication].

## <a name="authorization"></a>Авторизация
Авторизация относится к действиям, выполняемым в базе данных хранилища данных SQL Azure. Привилегии авторизации определяются членством в ролях и разрешениями. Обычно пользователям рекомендуется предоставлять наименьшие необходимые привилегии. Для управления ролями можно использовать следующие хранимые процедуры.

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Учетной записи администратора сервера, которая используется для подключения, присвоена роль db_owner, обладатель которой может выполнять любые действия в базе данных. Сохраните эту учетную запись для развертывания обновлений схемы и выполнения других действий по управлению. Для подключения к базе данных из приложения с использованием наименьших привилегий, необходимых приложению, используйте учетную запись "ApplicationUser" с более ограниченными разрешениями.

Существуют способы еще больше ограничить действия пользователя с хранилищем данных SQL Azure.

* Детализированные [разрешения][Permissions] позволяют контролировать, какие операции можно выполнять с отдельными столбцами, таблицами, представлениями, схемами, процедурами и другими объектами в базе данных. Используйте детализированные разрешения для обеспечения наивысшего уровня контроля и предоставления минимальных необходимых разрешений. 
* Для создания учетных записей пользователей приложения с более широкими возможностями или учетных записей для управления с меньшим набором возможностей можно использовать [роли базы данных][Database roles], отличные от db_datareader и db_datawriter. С помощью встроенных фиксированных ролей базы данных можно легко присваивать разрешения, однако это может привести к предоставлению лишних разрешений.
* [хранимые процедуры][Stored procedures] .

В следующем примере предоставляется доступ для чтения к определяемой пользователем схеме.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Управление базами данных и логическими серверами на портале Azure или с помощью API Azure Resource Manager контролируется путем назначения ролей для учетной записи пользователя портала. Дополнительные сведения см. [в разделе Управление доступом на основе ролей в портал Azure][Role-based access control in Azure portal].

## <a name="encryption"></a>Шифрование
Прозрачное шифрование данных хранилища данных SQL Azure помогает защититься от вредоносных атак благодаря шифрованию и расшифровке неактивных данных.  При шифровании базы связанные резервные копии и файлы журналов транзакций шифруются без необходимости вносить изменения в приложения. При использовании TDE хранилище всей базы данных шифруется с помощью симметричного ключа, который называется ключом шифрования базы данных. 

В базе данных SQL ключ шифрования базы данных защищается встроенным сертификатом сервера. Каждый сервер Базы данных SQL обладает уникальным встроенным сертификатом. Корпорация Майкрософт автоматически изменяет эти сертификаты не реже, чем раз в 90 дней. Алгоритм шифрования, используемый в хранилище данных SQL, это AES-256. Общее описание функции TDE см. в статье [Прозрачное шифрование данных (TDE)][Transparent Data Encryption].

Вы можете зашифровать базу данных с помощью [портал Azure][Encryption with Portal] или [T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения и примеры подключения к хранилищу данных SQL с различными протоколами см. в статье [Подключение к хранилищу данных SQL][Connect to SQL Data Warehouse].

<!--Image references-->

<!--Article references-->
[Connect to SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Encryption with Portal]: ./sql-data-warehouse-encryption-tde.md
[Encryption with TSQL]: ./sql-data-warehouse-encryption-tde-tsql.md
[Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Azure SQL Database firewall]: https://msdn.microsoft.com/library/ee621782.aspx
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[Database roles]: https://msdn.microsoft.com/library/ms189121.aspx
[Managing databases and logins in Azure SQL Database]: https://msdn.microsoft.com/library/ee336235.aspx
[Permissions]: https://msdn.microsoft.com/library/ms191291.aspx
[Stored procedures]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: https://msdn.microsoft.com/library/bb934049.aspx
[Azure portal]: https://portal.azure.com/

<!--Other Web references-->
[Role-based access control in Azure portal]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure
