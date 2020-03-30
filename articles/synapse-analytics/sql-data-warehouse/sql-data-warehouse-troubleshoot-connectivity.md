---
title: Устранение неполадок с подключением
description: Подключение к устранению неполадок в аналитике S'L.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 689a2e549c2627c607b6549f164e55a73318f63e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350046"
---
# <a name="troubleshooting-connectivity-issues"></a>Устранение неполадок с подключением

В этой статье перечислены общие методы устранения неполадок при подключении к базе данных S'L Analytics.
- [Проверка доступности услуг](sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Проверка на наличие приостановленных или масштабируемых операций](sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Проверка параметров брандмауэра](sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Проверка параметров конечной точки виртуальной сети или службы](sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Проверка наличия новых драйверов](sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Проверка строки подключения](sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Нерегулярные ошибки подключения](sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Распространенные сообщения об ошибках](sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Проверка доступности услуг

Проверьте, доступна ли услуга. На портале Azure перейдите к базе данных S'L Analytics, к ней вы пытаетесь подключиться. В левой панели TOC, нажмите на **диагностику и решить проблемы**.

![Выберите здоровье ресурсов](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Статус вашей аналитики S'L будет показан здесь. Если служба не отображается как **доступная,** проверьте дальнейшие шаги.

![Доступно ею](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Если ваше здоровье ресурсов показывает, что ваш экземпляр S'L Analytics приостановлен или масштабируется, следуйте инструкциям, чтобы возобновить экземпляр.

![Сервис Приостановил](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) дополнительную информацию о здоровье ресурсов можно найти здесь.

## <a name="check-for-paused-or-scaling-operation"></a>Проверка на наличие приостановленных или масштабируемых операций

Проверьте портал, чтобы узнать, приостанавливается или масштабируется ваш экземпляр S'L Analytics.

![Сервис Пауза](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Если вы видите, что ваша служба приостановлена или масштабируется, проверьте, что это не во время вашего графика обслуживания. На портале для вашего *обзора*аналитики S'L вы увидите выбранный график обслуживания.

![Расписание обслуживания обзоров](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

В противном случае обратитесь к ИТ-администратору, чтобы убедиться, что это обслуживание не является запланированным событием. Чтобы возобновить экземпляр S'L Analytics, выполните последующие шаги, изложенные [здесь](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute).

## <a name="check-your-firewall-settings"></a>Проверка параметров брандмауэра

База данных S'L Analytics общается по порту 1433.Если вы пытаетесь подключиться из корпоративной сети, то сетевой брандмауэр может запретить исходящий трафик через порт 1433. В таком случае вы не сможете подключиться к серверу Базы данных SQL Azure, пока ваш ИТ-отдел не откроет порт 1433. Дополнительную информацию о конфигурациях брандмауэра можно найти [здесь](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>Проверка параметров конечной точки виртуальной сети или службы

Если вы получаете Ошибки 40914 и 40615, смотрите [описание ошибки и разрешение здесь](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Проверка наличия новых драйверов

### <a name="software"></a>Программное обеспечение

Проверьте, чтобы убедиться, что вы используете новейшие инструменты для подключения к вашей базе данных S'L Analytics:

* SSMS
* Azure Data Studio
* SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Драйверы

Убедитесь, что вы используете последние версии драйверов.Использование старой версии драйверов может привести к неожиданному поведению, так как старые драйверы могут не поддерживать новые функции.

* [Odbc](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [Php](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Проверка строки подключения

Убедитесь, что строки подключения заданы правильно.  Ниже приведены некоторые образцы.  Дополнительные сведения о строках подключений можно найти [здесь](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings).

Строка подключения по протоколу ADO.NET

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Строка подключения ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

Строка подключения PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

Строка подключения JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Нерегулярные ошибки подключения

Проверьте, не находится ли сервер под большой нагрузкой из-за высокого числа запросов в очереди. Возможно, потребуется расширить свой экземпляр S'L Analytics для получения дополнительных ресурсов.

## <a name="common-error-messages"></a>Распространенные сообщения об ошибках

Ошибки 40914 и 40615, см [описание ошибки и разрешение здесь](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Все еще возникали проблемы с подключением?
Создайте [билет поддержки,](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) чтобы инженерная команда смогли поддержать вас.
