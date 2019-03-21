---
title: База данных Azure для MariaDB драйверы и совместимость средств управления
description: В этой статье описываются драйверы MariaDB и средства управления, которые совместимы с базой данных Azure для MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: 7a3d9a5f87a565625052fc54e3ecccc99fd928a7
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259505"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>MariaDB драйверы и средства управления, совместимые с базой данных Azure для MariaDB

В этой статье описываются драйверы и средства управления, которые совместимы с базой данных Azure для MariaDB.

## <a name="mariadb-drivers"></a>Драйверы MariaDB

База данных Azure для MariaDB использует community edition сервера MariaDB. Таким образом, она совместима с самыми разнообразными языками программирования и драйверами. API и протокола mariadb, но совместимы с используемыми приложением MySQL. Это означает, что соединителей, которые взаимодействуют с MySQL, также должны работать с MariaDB.

Целью является поддержка трех последних версий драйверов MariaDB, и по-прежнему усилий с авторами по с открытым кодом, чтобы постоянно улучшать функциональность и удобство использования драйверов MariaDB. Список драйверов, которые были протестированы и признаны совместимыми с базой данных Azure для MariaDB 10.2 приведен в следующей таблице:

**Драйвер** | **Ссылки** | **Совместимые версии** | **Несовместимые версии** | **Примечания**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5,3 | Для подключения PHP 7.0 с SSL MySQLi добавьте MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT в строке подключения. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Набор PDO: параметр ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` имеет значение False.
.NET | [MySqlConnector на GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Пакет установки из Nuget](https://www.nuget.org/packages/MySqlConnector/) | Версия 0.27 и более поздние версии | Версия 0.26.5 и предыдущие версии |
MySQL Connector/NET | [MySQL Connector/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | Ошибка кодирования может привести к сбою подключения в некоторых системах Windows, не поддерживающих UTF-8.
Node.js |  [MySQLjs на GitHub](https://github.com/mysqljs/mysql/) <br> Пакет установки из NPM:<br> Запустите команду `npm install mysql` из NPM | 2.15 | Версия 2.14.1 и предыдущие версии
GO | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | Версия 1.2 и предыдущие версии | Используйте `allowNativePasswords=true` в строке подключения для версии 1.3. Версия 1.4 содержит исправления и `allowNativePasswords=true` больше не требуется.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | Версия 1.2.2 и предыдущие версии |
Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | Версия 1.5.5 и предыдущие версии |

## <a name="management-tools"></a>Инструменты управления

Преимущества совместимости также распространяются на инструменты управления базой данных. Имеющиеся у вас инструменты должны продолжать работать с базой данных Azure для MariaDB, до тех пор, пока Обработка базы данных выполняется в пределах разрешений пользователя. В следующей таблице перечислены три распространенных инструментов управления базами данных, которые были протестированы и признаны совместимыми с базой данных Azure для MariaDB 10.2:

| | **MySQL Workbench 6.x и более поздней версии** | **Navicat 12** | **PHPMyAdmin 4.x и более поздней версии**
---|---|---|---
Создание, обновление, чтение, запись и удаление | X | X | X
SSL-соединение | X | X | X
Автозавершение запроса SQL | X | X |
Импорт и экспорт данных | X | X | X
Экспорт в несколько форматов | X | X | X
Архивация и восстановление |  | X |
Отображение параметров сервера | X | X | X
Отображение клиентских подключений | X | X | X

## <a name="next-steps"></a>Дальнейшие действия

- [Устранение проблем с подключением к Базе данных Azure для MariaDB](howto-troubleshoot-common-connection-issues.md)