---
title: Краткие руководства по подключению и созданию запросов к базе данных SQL Azure | Документация Майкрософт
description: В этих кратких руководствах по Базе данных SQL Azure объясняется, как подключаться и создавать запросы к базе данных SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 69944bc9d6edbd6a369f7ea977dffa94de64d1c5
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675431"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>Краткие руководства: Подключение и создание запросов к Базе данных SQL Azure

В этом документе представлены ссылки на примеры для Azure, в которых показано, как подключаться и создавать запросы к базе данных SQL Azure. В нем также приводятся рекомендации по использованию протокола TLS.

## <a name="quickstarts"></a>Краткие руководства

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|В этом кратком руководстве показано, как использовать SSMS для подключения к базе данных Azure SQL, а затем с помощью инструкций Transact-SQL выполнить запрос, вставку, обновление и удаление данных в базе данных.|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|В этом кратком руководстве показано, как использовать Azure Data Studio для подключения к базе данных SQL Azure, а затем с помощью инструкций Transact-SQL (T-SQL) создать базу данных TutorialDB, которая используется в руководствах по Azure Data Studio.|
|[портал Azure](sql-database-connect-query-portal.md)|В этом кратком руководстве показано, как использовать редактор запросов для подключения к базе данных SQL, а затем с помощью инструкций Transact-SQL выполнить запрос, вставку, обновление и удаление данных в базе данных.|
|[Visual Studio Code](sql-database-connect-query-vscode.md)|В этом кратком руководстве показано, как, используя Visual Studio Code, подключиться к базе данных SQL Azure, а затем с помощью инструкций Transact-SQL выполнить запрос, вставку, обновление и удаление данных в базе данных.|
|[.NET с Visual Studio](sql-database-connect-query-dotnet-visual-studio.md)|В этом кратком руководстве показано, как использовать платформу .NET Framework для создания программы C# с помощью Visual Studio, которая подключается к базе данных SQL Azure, а затем с помощью инструкций Transact-SQL выполнить запрос данных.|
|[.NET Core](sql-database-connect-query-dotnet-core.md)|В этом кратком руководстве показано, как использовать .NET Core в Windows, Linux и Mac OS для создания программы C#, которая подключается к базе данных SQL Azure, а затем с помощью инструкций Transact-SQL выполнить запрос данных.|
|[GO](sql-database-connect-query-go.md)|В этом кратком руководстве показано, как использовать Go для подключения к базе данных SQL Azure. Также демонстрируется применение инструкций Transact-SQL для запроса и изменения данных.|
|[Java](sql-database-connect-query-java.md)|В этом кратком руководстве показано, как использовать Java для подключения к базе данных SQL Azure, а затем с помощью инструкций Transact-SQL выполнить запрос данных.|
|[Node.js](sql-database-connect-query-nodejs.md)|В этом кратком руководстве показано, как использовать Node.js для создания программы, которая подключается к базе данных SQL Azure, а затем с помощью инструкций Transact-SQL выполнить запрос данных.|
|[PHP](sql-database-connect-query-php.md)|В этом кратком руководстве показано, как использовать PHP для создания программы, которая подключается к базе данных SQL Azure, а затем с помощью инструкций Transact-SQL выполнить запрос данных.|
|[Python](sql-database-connect-query-python.md)|В этом кратком руководстве показано, как использовать Python для подключения к базе данных SQL Azure, а затем с помощью инструкций Transact-SQL выполнить запрос данных. |
|[Ruby](sql-database-connect-query-ruby.md)|В этом кратком руководстве показано, как использовать Ruby для создания программы, которая подключается к базе данных SQL Azure, а затем с помощью инструкций Transact-SQL выполнить запрос данных.|
|[R](sql-database-connect-query-r.md)|В этом кратком руководстве показано, как использовать R и Службы машинного обучения в Базе данных SQL Azure, чтобы создать программу для подключения к базе данных SQL Azure, а затем с помощью инструкций Transact-SQL выполнять запросы данных.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>Рекомендации по использованию протокола TLS для подключения к базе данных SQL
Протокол TLS (Transport Layer Security) использует все драйверы, предоставляемые или поддерживаемые корпорацией Майкрософт, для подключения к базе данных SQL Azure. Специальная настройка не требуется. Чтобы обеспечить подключение к SQL Server или Базе данных SQL Azure, мы рекомендуем задать во всех приложениях следующие или подобные конфигурации:

 - **Encrypt = On**;
 - **TrustServerCertificate = Off**.

В некоторых системах используются другие, но подобные ключевые слова для конфигурации. Эти конфигурации гарантируют, что драйвер клиента проверяет подлинность сертификата TLS, полученного от сервера.

Также рекомендуется отключить протоколы TLS 1.1 и 1.0 в клиенте, если нужно обеспечить соответствие Стандарту безопасности данных в сфере платежных карт (PCI-DSS).

Сторонние драйверы могут не поддерживать TLS по умолчанию. Это может вызвать трудности при подключении к Базе данных SQL Azure. Иногда в приложениях со встроенными драйверами не предусмотрено управление этими параметрами подключения. Мы рекомендуем проверять безопасность таких драйверов и приложений перед их использованием в системах, которые взаимодействуют с конфиденциальными данными.

## <a name="libraries"></a>Библиотеки

Для подключения к Базе данных SQL Azure можно использовать различные библиотеки и платформы. Ознакомьтесь с нашими [руководствами по началу работы](https://aka.ms/sqldev), чтобы быстро приступить к программированию на таких языках, как C#, Java, Node.js, PHP и Python. Затем создайте приложение с помощью SQL Server в Linux или Windows либо Docker в macOS.

В следующей таблице перечислены библиотеки подключений или *драйверы*, которые клиентские приложения могут применять с помощью различных языков для подключения и использования сервера SQL Server, работающего локально или в облаке. Эти библиотеки и драйверы можно использовать в среде Linux, Windows или Docker, а также при подключении к базе данных SQL Azure и хранилищу данных SQL Azure. 

| Язык | платформа | Дополнительные ресурсы | Скачивание | Начало работы |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET для SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-sql-server) | [Загрузить](https://www.microsoft.com/net/download/) | [Начало работы](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC Driver для SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) | [Загрузить](https://go.microsoft.com/fwlink/?linkid=852460) |  [Начало работы](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Драйвер PHP SQL для SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Загрузить](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Начало работы](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Драйвер Node.js для SQL Server](https://msdn.microsoft.com/library/mt652093.aspx) | [Установка](https://msdn.microsoft.com/library/mt652094.aspx) |  [Начало работы](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Драйвер Python SQL](https://msdn.microsoft.com/library/mt652092.aspx) | Варианты установки: <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [Начало работы](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Драйвер Ruby для SQL Server](https://msdn.microsoft.com/library/mt691981.aspx) | [Установка](https://msdn.microsoft.com/library/mt711041.aspx) | [Начало работы](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Microsoft ODBC Driver for SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Загрузить](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

В следующей таблице перечислены примеры платформ объектно-реляционного сопоставления и веб-платформ, которые клиентские приложения могут использовать с сервером SQL Server, работающим локально или в облаке. Эти платформы можно использовать в Linux, Windows или Docker, а также при подключении к базе данных SQL и хранилищу данных SQL. 

| Язык | платформа | Платформы ORM |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernate ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquent)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Дополнительная информация

- Сведения об архитектуре подключения см. в статье [Архитектура подключений к базе данных SQL Azure](sql-database-connectivity-architecture.md).
- Получите [драйверы SQL Server](https://msdn.microsoft.com/library/mt654049.aspx), которые используются для подключения из клиентских приложений.
- Подключение к базе данных SQL:
  - [Подключение к базе данных SQL с помощью .NET (C#)](sql-database-connect-query-dotnet.md) 
  - [Подключение к Базе данных SQL с помощью PHP](sql-database-connect-query-php.md) 
  - [Подключение к базе данных SQL с помощью Node.js](sql-database-connect-query-nodejs.md) 
  - [Подключение к Базе данных SQL с помощью Java](sql-database-connect-query-java.md) 
  - [Подключение к базе данных SQL с помощью Python](sql-database-connect-query-python.md)
  - [Подключение к базе данных SQL с помощью Ruby](sql-database-connect-query-ruby.md)
- Примеры кода логики повторов:
  - [Отказоустойчивое подключение к SQL с помощью ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Отказоустойчивое подключение к SQL с помощью PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
