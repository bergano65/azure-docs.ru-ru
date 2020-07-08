---
title: Устранение неполадок с подключением
description: Устранение неполадок с подключением в пуле Synapse SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 33cc0c33f90d68ac3396f1366d4ff8437563eda1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213896"
---
# <a name="troubleshooting-connectivity-issues-in-synapse-sql-pool"></a>Устранение неполадок с подключением в пуле Synapse SQL

В этой статье перечислены распространенные методы устранения неполадок, связанных с подключением к базе данных в пуле SQL.

## <a name="check-service-availability"></a>Проверка доступности службы

Проверьте, доступна ли служба. На портале Azure перейдите к пулу SQL, к которому вы пытаетесь подключиться. На панели оглавления слева щелкните **Диагностика и решение проблем**.

![Выберите "Работоспособность ресурсов".](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Вы увидите состояние пула SQL. Если служба не отображается как **доступная**, выполните следующие действия.

![Служба доступна](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Если в сведениях о работоспособности ресурса указано, что экземпляр пула SQL приостановлен или масштабируется, следуйте указаниям по возобновлению работы экземпляра.

![Служба приостановлена](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png). Дополнительные сведения о работоспособности ресурса можно найти здесь.

## <a name="check-for-paused-or-scaling-operation"></a>Проверка на наличие приостановленных или масштабируемых операций

На портале проверьте наличие приостановленного или масштабируемого экземпляра пула SQL.

![Служба приостановлена](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Если вы видите, что служба приостановлена или масштабируется, проверьте, не происходит ли это во время запланированного обслуживания. На портале в *обзоре* пула SQL приводится выбранное расписание обслуживания.

![Просмотр расписания обслуживания](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

В противном случае обратитесь к ИТ-администратору, чтобы убедиться в том, что обслуживание является запланированным. Чтобы возобновить работу экземпляра пула SQL, выполните [эти действия](pause-and-resume-compute-portal.md).

## <a name="check-your-firewall-settings"></a>Проверка параметров брандмауэра

База данных пула SQL обменивается данными через порт 1433.  Если вы пытаетесь подключиться из корпоративной сети, то сетевой брандмауэр может запретить исходящий трафик через порт 1433. В этом случае вы не сможете подключиться к [логическому серверу](../../azure-sql/database/logical-servers.md) , пока ваш ИТ-отдел не откроет порт 1433. Дополнительные сведения о конфигурациях брандмауэра можно найти [здесь](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>Проверка параметров конечной точки виртуальной сети или службы

Если вы получаете ошибки с кодами 40914 и 40615, просмотрите [описание ошибки и варианты ее устранения](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Проверка наличия новых драйверов

### <a name="software"></a>Программное обеспечение

Убедитесь в том, что вы используете последние средства для подключения к пулу SQL:

- SSMS
- Azure Data Studio
- SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Драйверы

Убедитесь в том, что используются последние версии драйверов.  Использование более старых версий драйверов может привести к непредвиденному поведению, так как они могут не поддерживать новые функции.

- [ODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [OLE DB](/sql/connect/oledb/download-oledb-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [PHP](/sql/connect/php/download-drivers-php-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="check-your-connection-string"></a>Проверка строки подключения

Убедитесь, что строки подключения заданы правильно.  Ниже приведено несколько примеров.  Дополнительные сведения о строках подключений можно найти [здесь](sql-data-warehouse-connection-strings.md).

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

Проверьте, не находится ли сервер под большой нагрузкой из-за высокого числа запросов в очереди. В таком случае вам может понадобиться вертикально масштабировать пул SQL и добавить ресурсы.

## <a name="common-error-messages"></a>Распространенные сообщения об ошибках

Если вы получаете ошибки с кодами 40914 и 40615, просмотрите [описание ошибки и варианты ее устранения](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>По-прежнему возникают проблемы с подключением?

Создайте [запрос в службу поддержки](sql-data-warehouse-get-started-create-support-ticket.md), чтобы команда разработчиков могла помочь вам.
