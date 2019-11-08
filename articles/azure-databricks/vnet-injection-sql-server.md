---
title: Запрос SQL Server контейнера DOCKER для Linux с Azure Databricks
description: В этой статье описывается, как развертывать Azure Databricks в виртуальной сети, также называемой внедрением VNet.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 460079248e6cbd939c36b84f94cac41dce4dda2b
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747666"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Руководство. запрос SQL Server контейнера DOCKER для Linux в виртуальной сети из записной книжки Azure Databricks

В этом учебнике объясняется, как интегрировать Azure Databricks с контейнером DOCKER SQL Server Linux в виртуальной сети. 

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Развертывание рабочей области Azure Databricks в виртуальной сети
> * Установка виртуальной машины Linux в общедоступной сети
> * Установка Docker
> * Установка Microsoft SQL Server на Linux контейнера DOCKER
> * Запрос SQL Server с помощью JDBC из записной книжки "кирпичы"

## <a name="prerequisites"></a>Предварительные требования

* Создайте [рабочую область "кирпичи" в виртуальной сети](quickstart-create-databricks-workspace-vnet-injection.md).

* Установите [Ubuntu для Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Скачайте [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="create-a-linux-virtual-machine"></a>Создание виртуальной машины Linux

1. В портал Azure выберите значок для **виртуальных машин**. Затем выберите **+ Добавить**.

    ![Добавить новую виртуальную машину Azure](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. На вкладке **Основные сведения** выберите Ubuntu Server 18,04 LTS и измените размер виртуальной машины на B2s. Выберите имя пользователя и пароль администратора.

    ![Вкладка "Основные сведения" новой конфигурации виртуальной машины](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Перейдите на вкладку " **сеть** ". Выберите виртуальную сеть и общедоступную подсеть, которая включает кластер Azure Databricks. Выберите **проверить и создать**, а затем **создать** , чтобы развернуть виртуальную машину.

    ![Вкладка "Сетевые подключения" для новой конфигурации виртуальной машины](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. После завершения развертывания перейдите к виртуальной машине. Обратите внимание на общедоступный IP-адрес и виртуальную сеть или подсеть в **обзоре**. Выберите **общедоступный IP-адрес**

    ![Обзор виртуальной машины](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Измените **назначение** на **статическое** и введите **метку DNS-имени**. Выберите **сохранить**и перезапустите виртуальную машину.

    ![Конфигурация общедоступного IP-адреса](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Выберите вкладку **сети** в разделе **Параметры**. Обратите внимание, что группа безопасности сети, созданная во время развертывания Azure Databricks, связана с виртуальной машиной. Выберите **Добавить правило для входящего порта**.

7. Добавьте правило, чтобы открыть порт 22 для SSH. Используйте следующие параметры:
    
    |Настройка|Рекомендуемое значение|Description (Описание)|
    |-------|---------------|-----------|
    |Источник|IP-адреса|IP-адреса указывает, что это правило будет разрешать или запрещать входящий трафик с определенного исходного IP-адреса.|
    |Исходные IP-адреса|< общедоступный IP-\>|Введите общедоступный IP-адрес. Чтобы найти общедоступный IP-адрес, посетите [Bing.com](https://www.bing.com/) и найдите **"My IP"** .|
    |Диапазоны исходных портов|*|Разрешите трафик с любого порта.|
    |Место назначения|IP-адреса|IP-адреса указывает, что исходящий трафик для определенного исходного IP-адреса будет разрешен или отклонен этим правилом.|
    |IP-адреса назначения|< общедоступный IP-адрес виртуальной машины\>|Введите общедоступный IP-адрес виртуальной машины. Его можно найти на странице **Обзор** виртуальной машины.|
    |Диапазоны портов назначения|22|Откройте порт 22 для SSH.|
    |Приоритет|290|Присвойте правилу приоритет.|
    |Имя|SSH-кирпичи — учебник. Виртуальная машина|Присвойте правилу имя.|


    ![Добавление правила безопасности для входящего трафика для порта 22](./media/vnet-injection-sql-server/open-port.png)

8. Добавьте правило, чтобы открыть порт 1433 для SQL со следующими параметрами:

    |Настройка|Рекомендуемое значение|Description (Описание)|
    |-------|---------------|-----------|
    |Источник|Любой|Источник указывает, что это правило будет разрешать или запрещать входящий трафик с определенного исходного IP-адреса.|
    |Диапазоны исходных портов|*|Разрешите трафик с любого порта.|
    |Место назначения|IP-адреса|IP-адреса указывает, что исходящий трафик для определенного исходного IP-адреса будет разрешен или отклонен этим правилом.|
    |IP-адреса назначения|< общедоступный IP-адрес виртуальной машины\>|Введите общедоступный IP-адрес виртуальной машины. Его можно найти на странице **Обзор** виртуальной машины.|
    |Диапазоны портов назначения|1433|Откройте порт 22 для SQL Server.|
    |Приоритет|300|Присвойте правилу приоритет.|
    |Имя|SQL-кирпичи — учебник. Виртуальная машина|Присвойте правилу имя.|

    ![Добавление правила безопасности для входящего трафика для порта 1433](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Выполнение SQL Server в контейнере DOCKER

1. Откройте [Ubuntu для Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)или любое другое средство, которое позволит SSH-подключение к виртуальной машине. Перейдите к виртуальной машине в портал Azure и выберите **Подключиться** , чтобы получить команду SSH, которую необходимо подключить.

    ![Подключение к виртуальной машине](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Введите команду в окне терминала Ubuntu и введите пароль администратора, созданный при настройке виртуальной машины.

    ![Вход с SSH терминала Ubuntu](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. Используйте следующую команду, чтобы установить DOCKER на виртуальной машине.

    ```bash
    sudo apt-get install docker.io
    ```

    Проверьте установку DOCKER с помощью следующей команды:

    ```bash
    sudo docker --version
    ```

4. Установите образ.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    Проверьте образы.

    ```bash
    sudo docker images
    ```

5. Запустите контейнер из образа.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Убедитесь, что контейнер работает.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>Создание базы данных SQL

1. Откройте SQL Server Management Studio и подключитесь к серверу, используя имя сервера и проверку подлинности SQL. Имя пользователя для входа — **SA** , а пароль — пароль, заданный в команде DOCKER. Пароль в команде example имеет значение `Password1234`.

    ![Подключение к SQL Server с помощью SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. После успешного подключения выберите **создать запрос** и введите следующий фрагмент кода, чтобы создать базу данных, таблицу и вставить в нее некоторые записи.

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

    ![Запрос на создание базы данных SQL Server](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Запрос SQL Server из Azure Databricks

1. Перейдите в рабочую область Azure Databricks и убедитесь, что вы создали кластер в составе необходимых компонентов. Затем выберите **создать записную книжку**. Укажите имя записной книжки, выберите *Python* в качестве языка и выберите созданный кластер.

    ![Параметры записной книжки новых кирпичей](./media/vnet-injection-sql-server/create-notebook.png)

2. Используйте следующую команду, чтобы проверить связь с внутренним IP-адресом SQL Server виртуальной машины. Проверка связи должна быть успешной. В противном случае убедитесь, что контейнер работает, и проверьте конфигурацию группы безопасности сети (NSG).

    ```python
    %sh
    ping 10.179.64.4
    ```

    Для просмотра можно также использовать команду nslookup.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. После успешной проверки связи с SQL Server можно выполнить запрос к базе данных и таблицам. Выполните следующий код Python:

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

Ставшие ненужными группу ресурсов, рабочую область Azure Databricks и все связанные ресурсы можно удалить. Удаление задания позволяет избежать ненужных затрат. Если вы планируете использовать рабочую область Azure Databricks в будущем, можно остановить кластер и перезапустить его позже. Если вы не собираетесь использовать рабочую область Azure Databricks, удалите все ресурсы, созданные в ходе работы с этим руководством, выполнив следующие шаги.

1. В меню слева на портале Azure щелкните **Группы ресурсов**, а затем выберите имя созданной группы ресурсов.

2. На странице группы ресурсов выберите **Удалить**, в текстовом поле введите имя ресурса для удаления и еще раз щелкните **Удалить**.

## <a name="next-steps"></a>Дальнейшие действия

Перейдите к следующей статье, чтобы узнать, как извлекать, преобразовывать и загружать данные с помощью Azure Databricks.
> [!div class="nextstepaction"]
> [Учебник. Извлечение, преобразование и загрузка данных с помощью Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
