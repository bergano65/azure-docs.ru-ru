---
title: Подключение и отправка запроса к SQL Azure для пограничных вычислений (предварительная версия)
description: Сведения о подключении и отправке запроса к SQL Azure для пограничных вычислений (предварительная версия)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e7034c3f664eeba802341510b109ba9cc57845a8
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235134"
---
# <a name="connect-and-query-azure-sql-edge-preview"></a>Подключение и отправка запроса к SQL Azure для пограничных вычислений (предварительная версия)

После развертывания контейнеров SQL Azure для пограничных вычислений вы можете подключиться к ядру СУБД SQL из любого из следующих расположений.

- Внутри контейнера
- Из другого контейнера docker, работающего на том же узле.
- С главного компьютера
- С любого другого клиентского компьютера в сети.

## <a name="tools-to-connect-to-azure-sql-edge"></a>Средства для подключения к SQL Azure для пограничных вычислений

Подключения к экземпляру SQL Azure для пограничных вычислений можно выполнить из любого из перечисленных ниже универсальных средств.

* [sqlcmd](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) — клиентские средства sqlcmd уже включены в образ контейнера SQL Azure для пограничных вычислений. Если подключиться к запущенному контейнеру с помощью интерактивной оболочки bash, можно запускать программы локально.
* [Среда SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode)

Чтобы подключиться к ядру СУБД SQL Azure для пограничных вычислений с сетевого компьютера, вам потребуется следующее:

- *IP-адрес или сетевое имя главного компьютера* — это ведущий компьютер, на котором работает контейнер SQL Azure для пограничных вычислений.
- *Сопоставление портов узла контейнера SQL Azure для пограничных вычислений* — это сопоставление портов для порта контейнера docker на узле. В контейнере SQL Azure для пограничных вычислений всегда сопоставляется с портом 1433. Это можно изменить в рамках развертывания SQL Azure для пограничных вычислений. Чтобы изменить номер порта, обновите параметры "Параметры создания контейнера" для модуля SQL Azure для пограничных вычислений в Azure IoT Edge. В приведенном ниже примере порт 1433 в контейнере сопоставляется с портом 1600 на узле.

    ```JSON
    {
        "PortBindings": {
          "1433/tcp": [
            {
              "HostPort": "1600"
            }
          ]
        }
    }
    ```

- *Пароль системного администратора для экземпляра SQL Azure для пограничных вычислений* — это значение, указанное для переменной среды **SA_PASSWORD** во время развертывания SQL Azure для пограничных вычислений.

## <a name="connecting-to-the-database-engine-from-within-the-container"></a>Подключение к ядру СУБД с другого контейнера

[Средства командной строки SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) включены в образ контейнера SQL Azure для пограничных вычислений. Если подключиться к контейнеру с помощью интерактивной командной строки, программы можно будет запускать локально.

1. Выполните команду `docker exec -it`, чтобы запустить интерактивную оболочку bash внутри запущенного контейнера. В следующем примере `e69e056c702d` — это идентификатор контейнера.

    ```bash
    docker exec -it <Azure SQL Edge container id or name> /bin/bash
    ```

    > [!TIP]
    > Указывать идентификатор контейнера полностью во всех случаях не требуется. Достаточно указать количество символов, необходимое для его уникальной идентификации. Соответственно, вместо полного идентификатора в этом примере может использоваться форма `e6` или `e69`.

2. После входа в контейнер подключитесь локально с помощью sqlcmd. Средство sqlcmd не включено в путь по умолчанию, поэтому необходимо указать полный путь.

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. По завершении работы с sqlcmd введите `exit`.

4. После завершения работы с интерактивной командной строкой введите `exit`. Контейнер продолжит работать после выхода из интерактивной оболочки bash.

## <a name="connect-to-sql-edge-from-another-container-on-the-same-host"></a>Подключение к SQL Azure для пограничных вычислений из другого контейнера на том же узле

Поскольку два контейнера, работающие на одном узле, находятся в одной сети docker, к ним можно легко получить доступ, используя имя контейнера и адрес порта для службы. Например, при подключении к экземпляру SQL Azure для пограничных вычислений из другого модуля Python (контейнера) на том же узле можно использовать строку подключения, аналогичную приведенной ниже. В приведенном ниже примере предполагается, что SQL Azure для пограничных вычислений настроен так, чтобы прослушивать порт по умолчанию.

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-sql-edge-from-another-network-machine"></a>Подключение к SQL Azure для пограничных вычислений с другого сетевого компьютера

Чтобы подключиться к экземпляру SQL Azure для пограничных вычислений с другого компьютера в сети, необходимо использовать IP-адрес узла docker и порт узла, с которым сопоставлен контейнер SQL Azure для пограничных вычислений. Например, если IP-адрес узла docker — *xxx.xxx.xxx.xxx", а контейнер SQL Azure для пограничных вычислений сопоставлен с портом узла *1600*, адрес сервера для экземпляра SQL для пограничных вычислений будет **xxx.xxx.xxx.xxx,1600**. Обновленный скрипт Python будет выглядеть следующим образом

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

Чтобы подключиться к экземпляру SQL Azure для пограничных вычислений с помощью службы SQL Server Management Studio, работающей на компьютере Windows, см. статью о [SQL Server Management Studio](https://docs.microsoft.com/sql/linux/sql-server-linux-manage-ssms).

Чтобы подключиться к экземпляру SQL Azure для пограничных вычислений с помощью службы Visual Studio Code, работающей на компьютере Windows, Mac или Linux, см. статью о [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode).

Чтобы подключиться к экземпляру SQL Azure для пограничных вычислений с помощью службы Azure Data Studio, работающей на компьютере Windows, Mac или Linux, см. статью об [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-server).

## <a name="see-also"></a>См. также раздел

[Подключение и выполнение запросов](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[Установка средств SQL Server на Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)
