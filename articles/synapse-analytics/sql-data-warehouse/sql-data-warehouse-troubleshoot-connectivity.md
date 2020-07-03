---
title: Устранение неполадок с подключением
description: Устранение неполадок подключения в пуле SQL синапсе.
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
ms.openlocfilehash: d69c8dd28b946df3fff500c31c7cdefa4767c0c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81408214"
---
# <a name="troubleshooting-connectivity-issues"></a>Устранение неполадок с подключением

В этой статье перечислены распространенные методы устранения неполадок, связанных с подключением к базе данных аналитики SQL.

## <a name="check-service-availability"></a>Проверка доступности службы

Проверьте, доступна ли служба. В портал Azure перейдите в пул SQL синапсе, который вы пытаетесь подключить. На левой панели ОГЛАВЛЕНИя щелкните **Диагностика и решение проблем**.

![Выбор работоспособности ресурсов](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Состояние пула SQL синапсе будет показано здесь. Если служба не отображается как **доступная**, проверьте дальнейшие действия.

![Служба доступна](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Если работоспособность ресурса показывает, что экземпляр пула SQL синапсе приостановлен или масштабируется, следуйте указаниям по возобновлению работы вашего экземпляра.

![Приостановка работы](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) службы дополнительные сведения о работоспособность ресурсов можно найти здесь.

## <a name="check-for-paused-or-scaling-operation"></a>Проверка на наличие приостановленных или масштабируемых операций

Проверьте, не был ли экземпляр пула SQL синапсе приостановлен или масштабируется на портале.

![Служба приостановлена](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Если вы видите, что служба приостановлена или масштабируется, убедитесь, что она не находится в расписании обслуживания. На портале с *обзором*пула синапсе SQL вы увидите выбранное расписание обслуживания.

![Обзор расписания обслуживания](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

В противном случае обратитесь к ИТ-администратору, чтобы убедиться, что это обслуживание не запланировано. Чтобы возобновить работу экземпляра SQL Analytics, выполните следующие [действия](pause-and-resume-compute-portal.md).

## <a name="check-your-firewall-settings"></a>Проверка параметров брандмауэра

База данных SQL Analytics взаимодействует через порт 1433.Если вы пытаетесь подключиться из корпоративной сети, то сетевой брандмауэр может запретить исходящий трафик через порт 1433. В таком случае вы не сможете подключиться к серверу Базы данных SQL Azure, пока ваш ИТ-отдел не откроет порт 1433. Дополнительные сведения о конфигурациях брандмауэра можно найти [здесь](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>Проверка параметров конечной точки виртуальной сети или службы

Если вы получаете ошибки 40914 и 40615, ознакомьтесь [с описанием ошибки и разрешением здесь](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Проверка наличия новых драйверов

### <a name="software"></a>Программное обеспечение

Убедитесь, что вы используете новейшие средства для подключения к пулу синапсе SQL:

- SSMS
- Azure Data Studio
- SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>драйверы,

Убедитесь, что вы используете последние версии драйверов.Использование более старой версии драйверов может привести к непредвиденному поведению, так как старые драйверы могут не поддерживать новые функции.

- [ODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [OLE DB](/sql/connect/oledb/download-oledb-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [PHP](/sql/connect/php/download-drivers-php-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="check-your-connection-string"></a>Проверка строки подключения

Убедитесь, что строки подключения заданы правильно.  Ниже приведены некоторые примеры.  Дополнительные сведения о строках подключений можно найти [здесь](sql-data-warehouse-connection-strings.md).

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

Проверьте, не находится ли сервер под большой нагрузкой из-за высокого числа запросов в очереди. Возможно, потребуется увеличить масштаб пула SQL синапсе для получения дополнительных ресурсов.

## <a name="common-error-messages"></a>Распространенные сообщения об ошибках

Ошибках 40914 и 40615, см. [Описание ошибки и разрешение здесь](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>У вас остались проблемы с подключением?

Создайте запрос в [службу поддержки](sql-data-warehouse-get-started-create-support-ticket.md) , чтобы команда разработчиков могла поддерживать вас.
