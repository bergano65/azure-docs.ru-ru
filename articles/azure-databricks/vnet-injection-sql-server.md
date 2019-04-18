---
title: Запрос контейнера Docker в Linux SQL Server в виртуальной сети с Azure Databricks notebook
description: В этой статье описывается развертывание Azure Databricks к виртуальной сети, также называется путем внедрения кода виртуальной сети.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 345e07fac30f4ad0c8e9918cb8a1ff0fb8aeb811
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59288956"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Руководство по Запрос контейнера Docker в Linux SQL Server в виртуальной сети с Azure Databricks notebook

Этом учебнике показано, как интегрировать Azure Databricks с помощью контейнера Docker SQL Server на Linux в виртуальной сети. 

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Развертывание в рабочей области Azure Databricks к виртуальной сети
> * Установка виртуальной машины Linux в общедоступной сети
> * Установка Docker
> * Установите Microsoft SQL Server в контейнере docker в Linux
> * Запрос SQL Server, с помощью JDBC из записной книжки Databricks

## <a name="prerequisites"></a>Технические условия

* Создание [рабочей области Databricks в виртуальной сети](quickstart-create-databricks-workspace-vnet-injection.md).

* Установка [Ubuntu для Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Скачайте [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="create-a-linux-virtual-machine"></a>Создание виртуальной машины Linux

1. На портале Azure, выберите значок **виртуальные машины**. Выберите **+ добавить**.

    ![Добавить новую виртуальную машину Azure](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. На **основы** вкладке, выберите Ubuntu Server 16.04 LTS. Измените размер виртуальной Машины на B1ms, имеющая один виртуальных ЦП и 2 ГБ ОЗУ. Минимальным требованием для контейнера Docker SQL Server в Linux — 2 ГБ. Выберите администратора, имя пользователя и пароль.

    ![Основные сведения о вкладке новую конфигурацию виртуальной машины](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Перейдите к **сети** вкладки. Выберите виртуальную сеть и общедоступный подсети, которая содержит кластер Azure Databricks. Выберите **проверки + создать**, затем **создать** для развертывания виртуальной машины.

    ![Вкладка "Сеть" из новой конфигурации виртуальной машины](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. После завершения развертывания перейдите к виртуальной машине. Обратите внимание на общедоступный IP-адрес и виртуальную сеть и подсеть в **Обзор**. Выберите **общедоступный IP-адрес**

    ![Обзор виртуальной машины](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Изменение **назначения** для **статический** и введите **метка DNS-имени**. Выберите **Сохранить**и перезапустите виртуальную машину.

    ![Открытая конфигурация IP-адрес](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Выберите **сети** открыть, выбрав вкладку **параметры**. Обратите внимание на то, что группы безопасности сети, которая была создана во время развертывания Azure Databricks связан с виртуальной машиной. Выберите **добавить правило входящего порта**.

7. Добавьте правило для открытия порта 22 для SSH. Используйте следующие параметры:
    
    |Параметр|Рекомендуемое значение|ОПИСАНИЕ|
    |-------|---------------|-----------|
    |Источник|IP-адреса|IP-адреса указывает, что входящий трафик из определенного источника, IP-адреса будут разрешены или запрещены этим правилом.|
    |Исходные IP-адреса|< ваш общедоступный IP-адрес\>|Введите свой общедоступный IP-адрес. Общедоступный IP-адрес можно найти в статье, посвященной [bing.com](https://www.bing.com/) и выполнив поиск фразы **«Мой IP-адрес»**.|
    |Диапазоны исходных портов|*|Разрешите трафик с любого порта.|
    |Место назначения|IP-адреса|IP-адреса указывает, что исходящий трафик для определенного источника, IP-адреса будут разрешены или запрещены этим правилом.|
    |IP-адресов назначения|< вашей виртуальной машины общедоступный IP-адрес\>|Введите общедоступный IP-адрес виртуальной машины. Его можно найти на **Обзор** страницы виртуальной машины.|
    |Диапазоны портов назначения|22|Откройте порт 22 для SSH.|
    |Приоритет|290|Введите приоритет правила.|
    |ИМЯ|ssh-databricks-tutorial-vm|Введите имя правила.|


    ![Добавление правила безопасности для входящего трафика для порта 22](./media/vnet-injection-sql-server/open-port.png)

8. Добавьте правило, чтобы открыть порт 1433 для SQL со следующими параметрами:

    |Параметр|Рекомендуемое значение|ОПИСАНИЕ|
    |-------|---------------|-----------|
    |Источник|IP-адреса|IP-адреса указывает, что входящий трафик из определенного источника, IP-адреса будут разрешены или запрещены этим правилом.|
    |Исходные IP-адреса|10.179.0.0/16|Введите диапазон адресов для виртуальной сети.|
    |Диапазоны исходных портов|*|Разрешите трафик с любого порта.|
    |Место назначения|IP-адреса|IP-адреса указывает, что исходящий трафик для определенного источника, IP-адреса будут разрешены или запрещены этим правилом.|
    |IP-адресов назначения|< вашей виртуальной машины общедоступный IP-адрес\>|Введите общедоступный IP-адрес виртуальной машины. Его можно найти на **Обзор** страницы виртуальной машины.|
    |Диапазоны портов назначения|1433|Откройте порт 22 для SQL Server.|
    |Приоритет|300|Введите приоритет правила.|
    |ИМЯ|sql-databricks-tutorial-vm|Введите имя правила.|

    ![Добавление правила безопасности для входящего трафика для порта 1433](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Запуск SQL Server в контейнере Docker

1. Откройте [Ubuntu для Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab), или любой другой инструмент, который позволит вам подключиться по протоколу SSH к виртуальной машине. Перейдите к виртуальной машине в Azure, портала и выберите **Connect** для получения команды SSH, необходимые для подключения.

    ![Подключение к виртуальной машине](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Введите команду в окне терминала Ubuntu и введите пароль администратора, созданную при настройке виртуальной машины.

    ![Ubuntu терминала SSH входа](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. Используйте следующую команду, чтобы установить Docker на виртуальной машине.

    ```bash
    sudo apt-get install docker.io
    ```

    Проверьте установку Docker с помощью следующей команды:

    ```bash
    sudo docker --version
    ```

4. Установите образ.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    Проверьте изображения.

    ```bash
    sudo docker images
    ```

5. Запуск контейнера из образа.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Убедитесь, что контейнер запущен.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>Создание базы данных SQL

1. Откройте SQL Server Management Studio и подключитесь к серверу, используя имя сервера и проверки подлинности SQL. Знак в имени пользователя не **SA** пароль — пароль, заданный в команде Docker. Пароль в примере команды `Password1234`.

    ![Подключение к SQL Server с помощью SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. После успешного подключения, выберите **новый запрос** введите следующий фрагмент кода для создания базы данных, таблицы и вставить некоторые записи в таблице.

    ```SQL
    CREATE DATABASE MYDB;
    GO
    USE MYDB;
    CREATE TABLE states(Name VARCHAR(20), Capitol VARCHAR(20));
    INSERT INTO states VALUES ('Delaware','Dover');
    INSERT INTO states VALUES ('South Carolina','Columbia');
    INSERT INTO states VALUES ('Texas','Austin');
    SELECT * FROM states
    GO
    ```

    ![Запрос, чтобы создать базу данных SQL Server](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Запрос SQL Server из Azure Databricks

1. Перейдите в рабочую область Azure Databricks и убедитесь, что вы создали кластер как часть необходимых компонентов. Выберите **Создание записной книжки**. Присвойте имя, выберите записную книжку *Python* в качестве языка и выберите созданный кластер.

    ![Новые параметры записной книжки Databricks](./media/vnet-injection-sql-server/create-notebook.png)

2. Используйте следующую команду для проверки связи внутренний IP-адрес виртуальной машины SQL Server. Проверка связи будет считаться успешной. В противном случае убедитесь, что контейнер работает и проверьте конфигурацию группы безопасности сети.

    ```python
    %sh
    ping 10.179.64.4
    ```

    Можно также использовать команды nslookup для просмотра.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. После успешного проверка связи выполняется SQL Server, их можно запросить базу данных и таблицы. Запустите python следующий код:

    ```python
    jdbcHostname = "10.179.64.4"
    jdbcDatabase = "MYDB"
    userName = 'SA'
    password = 'Password1234'
    jdbcPort = 1433
    jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, userName, password)

    df = spark.read.jdbc(url=jdbcUrl, table='states')
    display(df)
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

Ставшие ненужными группу ресурсов, рабочей области Azure Databricks и удалите все связанные ресурсы. Удаление задания позволяет избежать ненужных выставления счетов. Если вы планируете использовать рабочей области Azure Databricks в будущем, можно остановить кластера и перезапустить его позже. Если вы не собираетесь продолжить использование этой рабочей области Azure Databricks, удалите все ресурсы, созданные в этом руководстве, выполнив следующие действия:

1. В меню слева на портале Azure, щелкните **групп ресурсов** и затем щелкните имя созданной вами группе ресурсов.

2. На странице группы ресурсов, выберите **удалить**, введите имя ресурса для удаления в текстовом поле, а затем выберите **удалить** еще раз.

## <a name="next-steps"></a>Дальнейшие действия

Перейдите к следующей статье, чтобы узнать, как для извлечения, преобразования и загрузки данных с помощью Azure Databricks.
> [!div class="nextstepaction"]
> [Руководство Извлечение, преобразование и загрузка данных с помощью Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
