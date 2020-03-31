---
title: Запрос контейнера для докера Сервера Linux с azure Databricks
description: В этой статье описывается, как развернуть Azure Databricks в виртуальной сети, также известной как инъекция VNet.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 460079248e6cbd939c36b84f94cac41dce4dda2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73747666"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Учебник: Запрос контейнера для Linux Docker в виртуальной сети из ноутбука Azure Databricks

Этот учебник научит вас, как интегрировать Azure Databricks с контейнером S'L Server Linux Docker в виртуальную сеть. 

В этом руководстве описано следующее:

> [!div class="checklist"]
> * Развертывание рабочего пространства Azure Databricks в виртуальную сеть
> * Установка виртуальной машины Linux в общедоступную сеть
> * Установка Docker
> * Установка сервера Microsoft S'L на контейнер докера Linux
> * Запрос сервера S'L с помощью JDBC из блокнота Databricks

## <a name="prerequisites"></a>Предварительные требования

* Создание [рабочего пространства Databricks в виртуальной сети.](quickstart-create-databricks-workspace-vnet-injection.md)

* Установка [Ubuntu для Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Скачайте [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="create-a-linux-virtual-machine"></a>Создание виртуальной машины Linux

1. На портале Azure выберите значок для **виртуальных машин.** Затем щелкните **+ Добавить**.

    ![Добавьте новую виртуальную машину Azure](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. На **вкладке «Основы»** выберите Ubuntu Server 18.04 LTS и измените размер VM на B2. Выберите имя пользователя администратора и пароль.

    ![Основы вкладки новой виртуальной конфигурации машины](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Перейдите на вкладку **Сеть.** Выберите виртуальную сеть и общедоступную подсеть, которая включает кластер Azure Databricks. Выберите **Обзор и создайте,** затем **создайте** для развертывания виртуальной машины.

    ![Сетевое вкладке новой виртуальной конфигурации машины](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. Когда развертывание будет завершено, перейдите к виртуальной машине. Обратите внимание на общественный IP-адрес и виртуальную сеть/субнет в **Обзоре**. Выберите **общественный IP-адрес**

    ![Обзор виртуальной машины](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Измените **назначение** на **Static** и введите **метку имени DNS.** Выберите **Сохранить**, и перезапустить виртуальную машину.

    ![Конфигурация публичного IP-адреса](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Выберите вкладку **Сети** в **настройках.** Обратите внимание, что группа сетевой безопасности, созданная во время развертывания Azure Databricks, связана с виртуальной машиной. Выберите **Правило входящего порта.**

7. Добавьте правило, чтобы открыть порт 22 для SSH. Используйте следующие параметры:
    
    |Параметр|Рекомендуемое значение|Описание|
    |-------|---------------|-----------|
    |Источник|IP-адреса|IP-адреса указывает, что входящий трафик с определенного IP-адреса источника будет разрешен или отклонен этим правилом.|
    |Исходные IP-адреса|<вашей публичной IP\>|Введите свой общедоступный IP-адрес. Вы можете найти свой общедоступный IP-адрес, посетив [bing.com](https://www.bing.com/) и ища **"мой IP"**.|
    |Диапазоны исходных портов|*|Разрешить трафик из любого порта.|
    |Назначение|IP-адреса|IP-адреса определяют, что исходящий трафик для конкретного IP-адреса источника будет разрешен или отказано в этом правиле.|
    |IP-адреса назначения|<ваш Vm общественности IP\>|Введите общедоступный IP-адрес вашей виртуальной машины. Вы можете найти это на странице **Обзор** вашей виртуальной машины.|
    |Диапазоны портов назначения|22|Открыть порт 22 для SSH.|
    |Приоритет|290|Отдавайте правило приоритету.|
    |name|ssh-databricks-tutorial-vm|Дайте правилу имя.|


    ![Добавление входящего правила безопасности для порта 22](./media/vnet-injection-sql-server/open-port.png)

8. Добавьте правило, чтобы открыть порт 1433 для S'L со следующими настройками:

    |Параметр|Рекомендуемое значение|Описание|
    |-------|---------------|-----------|
    |Источник|Любой|Источник уточняет, что входящий трафик с определенного IP-адреса источника будет разрешен или отклонен этим правилом.|
    |Диапазоны исходных портов|*|Разрешить трафик из любого порта.|
    |Назначение|IP-адреса|IP-адреса определяют, что исходящий трафик для конкретного IP-адреса источника будет разрешен или отказано в этом правиле.|
    |IP-адреса назначения|<ваш Vm общественности IP\>|Введите общедоступный IP-адрес вашей виртуальной машины. Вы можете найти это на странице **Обзор** вашей виртуальной машины.|
    |Диапазоны портов назначения|1433|Открыть порт 22 для сервера S'L.|
    |Приоритет|300|Отдавайте правило приоритету.|
    |name|sql-databricks-tutorial-vm|Дайте правилу имя.|

    ![Добавление входящего правила безопасности для порта 1433](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Запуск сервера S'L в контейнере Docker

1. Откройте [Ubuntu для Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab), или любой другой инструмент, который позволит вам SSH в виртуальную машину. Перейдите к виртуальной машине на портале Azure и выберите **Connect,** чтобы получить команду SSH, которую необходимо подключить.

    ![Подключение к виртуальной машине](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Введите команду в терминале Ubuntu и введите созданный пароль админ, созданный при настройке виртуальной машины.

    ![Ubuntu терминал SSH войти в](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. Используйте следующую команду для установки Docker на виртуальную машину.

    ```bash
    sudo apt-get install docker.io
    ```

    Проверьте установку Docker со следующей командой:

    ```bash
    sudo docker --version
    ```

4. Установите изображение.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    Проверьте изображения.

    ```bash
    sudo docker images
    ```

5. Выполнить контейнер с изображения.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Убедитесь, что контейнер работает.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>Создание базы данных SQL

1. Откройте студию управления серверами и подключитесь к серверу, используя имя сервера и аутентификацию. Вывеской в имени пользователя является **SA,** а паролем является пароль, установленный в команде Docker. Пароль в примере `Password1234`команды .

    ![Подключитесь к серверу S'L с помощью студии управления серверами S'L](./media/vnet-injection-sql-server/ssms-login.png)

2. После успешного подключения выберите **новый запрос** и введите следующий фрагмент кода, чтобы создать базу данных, таблицу и вставить некоторые записи в таблицу.

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

    ![Запрос на создание базы данных сервера S'L](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Сервер запросов S'L от Azure Databricks

1. Перейдите в рабочее пространство Azure Databricks и убедитесь, что вы создали кластер как часть предпосылок. Затем выберите **Создать блокнот**. Дайте блокноту имя, выберите *Python* в качестве языка и выберите созданный кластер.

    ![Новые настройки ноутбука Databricks](./media/vnet-injection-sql-server/create-notebook.png)

2. Используйте следующую команду для пинг внутреннего IP-адреса виртуальной машины сервера S'L Server. Это пинг должен быть успешным. В противном случае убедитесь, что контейнер работает, и просмотрите конфигурацию группы сетевой безопасности (NSG).

    ```python
    %sh
    ping 10.179.64.4
    ```

    Вы также можете использовать команду nslookup для рассмотрения.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. После успешного провоза сервера S'L можно загнать базу данных и таблицы. Выполнить следующий код питона:

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

Перейти к следующей статье, чтобы узнать, как извлечь, преобразовать и загрузить данные с помощью Azure Databricks.
> [!div class="nextstepaction"]
> [Учебник: Извлекайте, преобразовывали и загружали данные с помощью Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
