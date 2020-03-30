---
title: Совместимость драйверов и инструментов - База данных Azure для MyS'L
description: В этой статье описываются драйверы и инструменты управления MySQL, совместимые со службой "База данных Azure для MySQL".
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: e8917a0a5678c4c6b72352a0d4c1523bfea3c96d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537216"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Совместимость драйверов и инструментов управления MySQL с базой данных Azure для MySQL.
В этой статье описываются драйверы и инструменты управления, совместимые с Базой данных Azure для MySQL.

## <a name="mysql-drivers"></a>Драйверы MySQL
База данных Azure для MySQL использует самый популярный выпуск Community Edition базы данных MySQL. Таким образом, она совместима с самыми разнообразными языками программирования и драйверами. Цель этого руководства — обеспечить поддержку трех последних версий драйверов MySQL и взаимодействие с сообществом разработчиков открытого кода, чтобы постоянно улучшать функциональность и удобство использования драйверов MySQL. Следующая таблица содержит список драйверов, которые были протестированы и которые совместимы с базой данных Azure для MySQL 5.6 и 5.7.

| **Язык программирования** | **Драйвер** | **Ссылки** | **Совместимые версии** | **Несовместимые версии** | **Примечания** |
| :----------------------- | :--------- | :-------- | :---------------------- | :------------------------ | :-------- |
| PHP | mysqli, pdo_mysql, mysqlnd | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5,3 | Для подключения PHP 7.0 с SSL MySQLi добавьте MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT в строке подключения. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Набор PDO: параметр ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` имеет значение False.|
| .NET | Async MyS'L Коннектор для .NET | https://github.com/mysql-net/MySqlConnector <br> [Пакет установки из Nuget](https://www.nuget.org/packages/MySqlConnector/) | Версия 0.27 и более поздние версии | Версия 0.26.5 и предыдущие версии | |
| .NET | MySQL Connector/NET | https://github.com/mysql/mysql-connector-net | 6.6.3 ,7.0 ,8.0 |  | Ошибка кодирования может привести к сбою подключения в некоторых системах Windows, не поддерживающих UTF-8. |
| Node.js | mysqljs | https://github.com/mysqljs/mysql/ <br> Пакет установки из NPM:<br> Запустите команду `npm install mysql` из NPM | 2.15 | Версия 2.14.1 и предыдущие версии | |
| Node.js | узл-mysql2 | https://github.com/sidorares/node-mysql2 | 1,3,4 евро | | |
| Go | Перейти MyS'L Драйвер | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | Версия 1.2 и предыдущие версии | Используйте `allowNativePasswords=true` в строке соединения для версии 1.3. Версия 1.4 содержит `allowNativePasswords=true` исправление и больше не требуется. |
| Python | МизЗЛ Коннектор/Питон | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2, используйте 8.0.16 » с MyS'L 8.0  | Версия 1.2.2 и предыдущие версии | |
| Python | ПиМыСЗЛ | https://pypi.org/project/PyMySQL/ | 0.7.11, 0.8.0, 0.8.1, 0.9.3 | 0.9.0 - 0.9.2 (регрессия в web2py) | |
| Java | MariaDB Коннектор/ J | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | Версия 1.5.5 и предыдущие версии | | 
| Java | MyS'L Коннектор / J | https://github.com/mysql/mysql-connector-j | 5.1.21 ", использовать 8.0.17" с MyS'L 8.0 | 5.1.20 и ниже | |
| C | MyS'L Коннектор/C (libmysqlclient) | https://dev.mysql.com/doc/refman/5.7/en/c-api-implementations.html | 6,0,2 евро | | |
| C | MyS'L Коннектор / ODBC (myodbc) | https://github.com/mysql/mysql-connector-odbc | 3.51.29 евро | | |
| C++ | Соединитель MyS'L/СЗЗ | https://github.com/mysql/mysql-connector-cpp | 1,1,9 евро | 1.1.3 и ниже | | 
| C++ | МиСЗЛЗ| https://tangentsoft.net/mysql++ | 3,2,3 евро | | |
| Ruby | mysql2 | https://github.com/brianmario/mysql2 | 0,4.10 евро | | |
| R | RMySQL | https://github.com/rstats-db/RMySQL | 0.10.16 евро | | |
| Swift | mysql-swift | https://github.com/novi/mysql-swift | 0,7,2 | | |
| Swift | пара/mysql | https://github.com/vapor/mysql-kit | 2,0,1 евро | | |

## <a name="management-tools"></a>Средства управления
Преимущества совместимости также распространяются на инструменты управления базой данных. Имеющиеся инструменты должны продолжать работать с базой данных Azure для MySQL, пока обработка базы данных выполняется в пределах разрешений пользователя. Следующая таблица содержит три распространенных инструмента управления базой данных, которые были протестированы и которые совместимы с базой данных Azure для MySQL 5.6 и 5.7.

|                                     | **MySQL Workbench 6.x и более поздней версии** | **Navicat 12** | **PHPMyAdmin 4.x и более поздней версии** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Создание, обновление, чтение, запись и удаление | X | X | X |
| SSL-соединение | X | X | X |
| Автозавершение запроса SQL | X | X |  |
| Импорт и экспорт данных | X | X | X | 
| Экспорт в несколько форматов | X | X | X |
| Резервное копирование и восстановление |  | X |  |
| Отображение параметров сервера | X | X | X |
| Отображение клиентских подключений | X | X | X |

## <a name="next-steps"></a>Дальнейшие действия

- [Узнайте, как устранить проблемы с подключением к Базе данных Azure для MySQL](howto-troubleshoot-common-connection-issues.md)