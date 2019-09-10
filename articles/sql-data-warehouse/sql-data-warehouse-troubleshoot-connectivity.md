---
title: Устранение неполадок хранилища данных SQL Azure | Документация Майкрософт
description: Диагностика и устранение неполадок хранилища данных SQL Azure.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: supportability
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: ebdeaf21253e89a9a14e3a56ca7be0f6e8adceb0
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859232"
---
# <a name="troubleshooting-connectivity-issues"></a>Устранение неполадок с подключением

В этой статье перечислены распространенные методы устранения неполадок, связанных с подключением к хранилищу данных SQL.
- [Проверка доступности службы](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Проверка на паузу или операцию масштабирования](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Проверьте параметры брандмауэра.](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Проверка параметров конечной точки виртуальной сети или службы](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Проверка наличия последних версий драйверов](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Проверка строки подключения](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Проблемы с периодическим подключением](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Распространенные сообщения об ошибках](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Проверка доступности службы

Проверьте, доступна ли служба. В портал Azure перейдите к хранилищу данных SQL, к которому вы пытаетесь подключиться. На левой панели ОГЛАВЛЕНИя щелкните **Диагностика и решение проблем**.

![Выбор работоспособности ресурсов](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Состояние хранилища данных SQL будет показано здесь. Если служба не отображается как **доступная**, проверьте дальнейшие действия.

![Служба доступна](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Если работоспособность ресурса показывает, что хранилище данных приостановлено или масштабируется, следуйте указаниям по возобновлению хранилища данных.

![Приостановка работы службы](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) дополнительные сведения о работоспособность ресурсов можно найти здесь.

## <a name="check-for-paused-or-scaling-operation"></a>Проверка на паузу или операцию масштабирования

Проверьте наличие приостановленного или масштабируемого хранилища данных SQL на портале.

![Служба приостановлена](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Если вы видите, что служба приостановлена или масштабируется, убедитесь, что она не находится в расписании обслуживания. На портале с *общими сведениями о*хранилище данных SQL вы увидите выбранное расписание обслуживания.

![Обзор расписания обслуживания](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

В противном случае обратитесь к ИТ-администратору, чтобы убедиться, что это обслуживание не запланировано. Чтобы возобновить работу хранилища данных SQL, выполните действия, описанные [здесь](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute).

## <a name="check-your-firewall-settings"></a>Проверьте параметры брандмауэра.

Хранилище данных SQL обменивается данными через порт 1433.   Если вы пытаетесь подключиться из корпоративной сети, то сетевой брандмауэр может запретить исходящий трафик через порт 1433. В этом случае вы не сможете подключиться к серверу базы данных SQL Azure, пока ваш ИТ-отдел не откроет порт 1433. Дополнительные сведения о конфигурациях брандмауэра можно найти [здесь](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>Проверка параметров конечной точки виртуальной сети или службы

Если вы получаете ошибки 40914 и 40615, ознакомьтесь [с описанием ошибки и разрешением здесь](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Проверка наличия последних версий драйверов

### <a name="software"></a>Программное обеспечение

Убедитесь, что вы используете новейшие средства для подключения к хранилищу данных SQL:

* SSMS
* Azure Data Studio
* SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Драйверы

Убедитесь, что вы используете последние версии драйверов.  Использование более старой версии драйверов может привести к непредвиденному поведению, так как старые драйверы могут не поддерживать новые функции.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Проверка строки подключения

Убедитесь, что строки подключения заданы правильно.  Ниже приведены некоторые примеры.  Дополнительные сведения о [строках подключения](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings)можно найти здесь.

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

## <a name="intermittent-connection-issues"></a>Проблемы с периодическим подключением

Проверьте, испытывает ли высокая нагрузка на сервер с большим количеством запросов в очереди. Вам может потребоваться увеличить масштаб хранилища данных для получения дополнительных ресурсов.

## <a name="common-error-messages"></a>Распространенные сообщения об ошибках

Ошибках 40914 и 40615, см. [Описание ошибки и разрешение здесь](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>У вас остались проблемы с подключением?
Создайте запрос в [службу поддержки](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) , чтобы команда разработчиков могла поддерживать вас.
