---
title: Устранение неполадок хранилища данных SQL Azure | Документация Майкрософт
description: Диагностика и устранение неполадок хранилища данных SQL Azure.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: supportability
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 3f25d2ab5b2b988725d8f4fdf942854746f404d1
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754844"
---
# <a name="troubleshooting-connectivity-issues"></a>Устранение неполадок с подключением

В этой статье перечислены общие действия по устранению неполадок вокруг подключения к хранилищу данных SQL.
- [Проверка доступности службы](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Проверка операции масштабирования или приостановлено](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Проверьте настройки брандмауэра](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Проверьте параметры конечной точки службы или виртуальной сети](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Проверьте последние версии драйверов](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Проверьте строку подключения](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Временным проблемам с подключением](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Распространенные сообщения об ошибках](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Проверка доступности службы

Установите этот флажок, чтобы узнать, если служба доступна. На портале Azure перейдите в хранилище данных SQL, которую вы пытаетесь подключиться. На левой панели Оглавление, щелкните **Диагностика и решение проблем**.

![Выберите службу работоспособности ресурсов](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Состояние хранилища данных SQL будут показаны здесь. Если служба не отображается как **доступно**, дальнейшей проверки действия.

![Служба доступна](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Если вашей службе работоспособности ресурсов показывают, что масштабирование, следуйте инструкциям, чтобы возобновить работу хранилища данных или хранилище данных приостановлено.

![Приостановка службы](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) Дополнительные сведения о службе работоспособности ресурсов можно найти здесь.

## <a name="check-for-paused-or-scaling-operation"></a>Проверка операции масштабирования или приостановлено

Проверьте на портале, чтобы увидеть, если хранилище данных SQL будет приостановлена или масштабирование.

![Служба приостановлена](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Если видно, что служба приостановлена или масштабирование, установите флажок, чтобы увидеть, что это не так во время вашего расписания обслуживания. На портале для хранилища данных SQL *Обзор*, вы увидите расписание выделенные обслуживания.

![Общие сведения о расписание обслуживания](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

В противном случае обратитесь к администратору ИТ, чтобы убедиться, что это обслуживание не запланированное событие. Чтобы возобновить работу хранилища данных SQL, выполните действия, описанные [здесь](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute).

## <a name="check-your-firewall-settings"></a>Проверьте настройки брандмауэра

Хранилище данных SQL обменивается данными через порт 1433.   Если вы пытаетесь подключиться из корпоративной сети, то сетевой брандмауэр может запретить исходящий трафик через порт 1433. В этом случае вам не удается подключиться к серверу базы данных SQL Azure, пока ваш ИТ-отдел не откроет порт 1433. Дополнительные сведения о конфигурации брандмауэра можно найти [здесь](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#manage-server-level-ip-firewall-rules-using-the-azure-portal).

## <a name="check-your-vnetservice-endpoint-settings"></a>Проверьте параметры конечной точки службы или виртуальной сети

Если вы получили ошибки 40914 и 40615, см. в разделе [описание ошибки и разрешение здесь](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Проверьте последние версии драйверов

### <a name="software"></a>Программное обеспечение

Установите этот флажок, чтобы убедиться, что вы используете последнюю версию инструментов для подключения к хранилищу данных SQL:

* SSMS
* Azure Data Studio
* SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Драйверы

Установите этот флажок, чтобы убедиться, что вы используете последние версии драйверов.  С помощью более старой версии драйверов может привести к непредвиденному поведению, старые драйверы могут не поддерживать новые функции.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Проверьте строку подключения

Установите этот флажок, чтобы убедиться, что правильно заданы строки подключения.  Ниже приведены некоторые примеры.  Можно найти дополнительную информацию о [здесь строки подключения](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings).

Строка подключения по протоколу ADO.NET

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Строка подключения ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

Строки подключения PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

Строка подключения JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Временным проблемам с подключением

Установите этот флажок, чтобы увидеть, если у вас возникли снизить нагрузку на сервер с большое количество запросов в очереди. Может потребоваться увеличить масштаб хранилища данных для получения дополнительной информации.

## <a name="common-error-messages"></a>Распространенные сообщения об ошибках

Ошибки 40914 и 40615, см. в разделе [описание ошибки и разрешение здесь](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>По-прежнему возникают проблемы с подключением?
Создание [запрос в службу поддержки](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) , команды разработчиков могут помочь вам.
