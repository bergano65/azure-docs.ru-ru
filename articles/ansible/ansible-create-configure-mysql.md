---
title: Создание и настройка Базы данных Azure для MySQL с помощью Ansible
description: Узнайте, как с помощью Ansible создать и настроить Базу данных Azure для сервера MySQL
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, mysql, database
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/23/2018
ms.openlocfilehash: 85f3957af599c80c46871a126681d29dfa513431
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051022"
---
# <a name="create-and-configure-an-azure-database-for-mysql-server-by-using-ansible"></a>Создание и настройка Базы данных Azure для MySQL с помощью Ansible
[База данных Azure для MySQL](https://docs.microsoft.com/azure/mysql/) — это управляемая служба, которая позволяет запускать, администрировать и масштабировать высокодоступные базы данных MySQL в облаке. Ansible позволяет автоматизировать развертывание и настройку ресурсов в среде. 

В этом кратком руководстве описано, как с помощью Ansible создать Базу данных Azure для сервера MySQL и настроить ее правила брандмауэра. С помощью портала Azure эти задачи можно выполнить за пять минут.

## <a name="prerequisites"></a>Предварительные требования
- **Подписка Azure.** Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Для выполнения примеров сборников схем в этом руководстве требуется Ansible 2.7. 

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими.  

В следующем примере создается группа ресурсов с именем **myResourceGroup** в расположении **eastus**.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

Сохраните упомянутый выше сборник схем, присвоив ему имя **rg.yml**. Чтобы запустить сборник схем, используйте команду **ansible-playbook** следующим образом:
```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>Создание сервера и базы данных MySQL
В следующем примере создается сервер MySQL с именем **mysqlserveransible** и экземпляр Базы данных Azure для MySQL с именем **mysqldbansible**. Это сервер 5-го поколения основного назначения с одним виртуальным ядром. 

Значение **mysqlserver_name** должно быть уникальным. Допустимые значения для каждого региона и каждого уровня указаны в документации по [ценовым категориям](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers). Замените `<server_admin_password>` паролем.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus
    mysqlserver_name: mysqlserveransible
    mysqldb_name: mysqldbansible
    admin_username: mysqladmin
    admin_password: <server_admin_password> 
  tasks:
    - name: Create MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        sku:
          name: B_Gen5_1
          tier: Basic
        location: "{{ location }}"
        version: 5.6
        enforce_ssl: True
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        storage_mb: 51200
    - name: Create instance of MySQL Database
      azure_rm_mysqldatabase:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
        name: "{{ mysqldb_name }}"
```

Сохраните упомянутый выше сборник схем, присвоив ему имя **mysql_create.yml**. Чтобы запустить сборник схем, используйте команду **ansible-playbook** следующим образом:
```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Настройка правила брандмауэра
Правило брандмауэра на уровне сервера позволяет внешним приложениям подключаться к серверу через брандмауэр службы Azure MySQL. Примером внешнего приложения является программа командной строки **mysql** или MySQL Workbench.
В приведенном ниже примере создается правило брандмауэра с именем **extenalaccess**, которое разрешает подключения с любого внешнего IP-адреса. 

Введите свои значения для **startIpAddress** и **endIpAddress**. Используйте диапазон IP-адресов, которые соответствуют расположению, из которого будет устанавливаться подключение. 

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
  - name: Open firewall to access MySQL Server from outside
    azure_rm_resource:
      api_version: '2017-12-01'
      resource_group: "{{ resource_group }}"
      provider: dbformysql
      resource_type: servers
      resource_name: "{{ mysqlserver_name }}"
      subresource:
        - type: firewallrules
          name: externalaccess
      body:
        properties:
          startIpAddress: "0.0.0.0"
          endIpAddress: "255.255.255.255"
```

> [!NOTE]
> Подключитесь к базе данных Azure для MySQL через порт 3306. Если вы пытаетесь подключиться из корпоративной сети, исходящий трафик через порт 3306 может быть запрещен. В таком случае вы не сможете подключиться к серверу. Для этого ваш ИТ-отдел должен открыть порт 3306.
> 

Здесь модуль **azure_rm_resource** используется для выполнения этой задачи. Он позволяет непосредственно использовать REST API.

Сохраните упомянутый выше сборник схем, присвоив ему имя **mysql_firewall.yml**. Чтобы запустить сборник схем, используйте команду **ansible-playbook** следующим образом:
```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server-by-using-the-command-line-tool"></a>Подключение к серверу с помощью программы командной строки
Вы можете [скачать MySQL](https://dev.mysql.com/downloads/) и установить на компьютер. Вместо этого можно выбрать кнопку **Попробовать** в примерах кода или кнопку **>_** на верхней правой панели инструментов на портале Azure и открыть **Azure Cloud Shell**.

Введите следующие команды. 

1. Подключитесь к серверу с помощью программы командной строки **mysql**:
```azurecli-interactive
 mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
```

2. Просмотрите состояние сервера:
```sql
 mysql> status
```

Если все работает правильно, в программе командной строки должен отобразиться следующий текст:

```
demo@Azure:~$ mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65233
Server version: 5.6.39.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64) using  EditLine wrapper

Connection id:          65233
Current database:
Current user:           mysqladmin@13.76.42.93
SSL:                    Cipher in use is AES256-SHA
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         5.6.39.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mysqlserveransible.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    utf8
Conn.  characterset:    utf8
TCP port:               3306
Uptime:                 36 min 21 sec

Threads: 5  Questions: 559  Slow queries: 0  Opens: 96  Flush tables: 3  Open tables: 10  Queries per second avg: 0.256
--------------
```

## <a name="using-facts-to-query-mysql-servers"></a>Использование фактов для запросов к серверам MySQL
В следующем примере запрашиваются серверы MySQL в **myResourceGroup**, а затем все базы данных на серверах:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Query MySQL Servers in current resource group
      azure_rm_mysqlserver_facts:
        resource_group: "{{ resource_group }}"
      register: mysqlserverfacts

    - name: Dump MySQL Server facts
      debug:
        var: mysqlserverfacts

    - name: Query MySQL Databases
      azure_rm_mysqldatabase_facts:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
      register: mysqldatabasefacts

    - name: Dump MySQL Database Facts
      debug:
        var: mysqldatabasefacts
```

Сохраните упомянутый выше сборник схем, присвоив ему имя **mysql_query.yml**. Чтобы запустить сборник схем, используйте команду **ansible-playbook** следующим образом:

```bash
ansible-playbook mysql_query.yml
```

Затем вы увидите следующие выходные данные для сервера MySQL: 
```json
"servers": [
    {
        "admin_username": "mysqladmin",
        "enforce_ssl": false,
        "fully_qualified_domain_name": "mysqlserveransible.mysql.database.azure.com",
        "id": "/subscriptions/685ba005-af8d-4b04-8f16-a7bf38b2eb5a/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysqlserveransible",
        "location": "eastus",
        "name": "mysqlserveransible",
        "resource_group": "myResourceGroup",
        "sku": {
            "capacity": 1,
            "family": "Gen5",
            "name": "B_Gen5_1",
            "tier": "Basic"
        },
        "storage_mb": 5120,
        "user_visible_state": "Ready",
        "version": "5.6"
    }
]
```

Вы увидите также следующие выходные данные для базы данных MySQL:
```json
"databases": [
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "information_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysql",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransibler"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysqldbansible",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "performance_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    }
]
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вам не нужны эти ресурсы, вы можете удалить их, выполнив следующий пример. Будет удалена группа ресурсов с именем **myResourceGroup**. 

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Сохраните упомянутый выше сборник схем, присвоив ему имя **rg_delete.yml**. Чтобы запустить сборник схем, используйте команду **ansible-playbook** следующим образом:
```bash
ansible-playbook rg_delete.yml
```

Если вы хотите удалить только один недавно созданный сервер MySQL, выполните следующий пример:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Delete MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        state: absent
```

Сохраните упомянутый выше сборник схем, присвоив ему имя **mysql_delete.yml**. Чтобы запустить сборник схем, используйте команду **ansible-playbook** следующим образом:
```bash
ansible-playbook mysql_delete.yml
```

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"] 
> [Документация по Ansible в Azure](https://docs.microsoft.com/azure/ansible/)
