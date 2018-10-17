---
title: Создание и настройка Базы данных Azure для сервера MySQL с помощью Ansible (предварительная версия)
description: Узнайте, как использовать Ansible для создания и настройки Базы данных Azure для сервера MySQL
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, mysql, database
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/23/2018
ms.openlocfilehash: 508274d11a9693d28a9b3a01bd6ebbd7198e8711
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2018
ms.locfileid: "47586706"
---
# <a name="create-and-configure-an-azure-database-for-mysql-server-using-ansible-preview"></a>Создание и настройка Базы данных Azure для сервера MySQL с помощью Ansible (предварительная версия)
[База данных Azure для MySQL](https://docs.microsoft.com/azure/mysql/) — это управляемая служба, которая позволяет запускать, администрировать и масштабировать высокодоступные базы данных MySQL в облаке. В этом кратком руководстве описывается, как за пять минут создать сервер службы "База данных Azure для MySQL" с помощью портала Azure. 

Ansible позволяет автоматизировать развертывание и настройку ресурсов в среде. В этой статье описано, как с помощью Ansible создать Базу данных Azure для сервера MySQL и настроить ее правила брандмауэра за пять минут. 

## <a name="prerequisites"></a>Предварительные требования
- **Подписка Azure.** Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Для выполнения примеров сборников схем в этом руководстве требуется Ansible 2.7. Чтобы установить версию RC Ansible 2.7, выполните команду `sudo pip install ansible[azure]==2.7.0rc2`. Выпуск Ansible 2.7 запланирован на октябрь 2018 г. После этой даты указывать версию не нужно, так как по умолчанию будет использоваться версия 2.7.

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

Сохраните сборник схем выше как *rg.yml*. Чтобы запустить сборник схем, используйте команду **ansible-playbook** следующим образом:
```bash
ansible-playbook rg.yml
```

## <a name="create-mysql-server-and-database"></a>Создание сервера и базы данных MySQL
В следующем примере создается сервер MySQL с именем **mysqlserveransible** и База данных Azure для MySQL с именем **mysqldbansible**. Это сервер 5-го поколения основного назначения с 1 виртуальным ядром. Обратите внимание, что значение **mysqlserver_name** должно быть уникальным. Ознакомьтесь с [документацией по ценовым категориям](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers), чтобы понять допустимые значения для региона и каждого уровня. 

Введите свой пароль `<server_admin_password>`:

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

Сохраните сборник схем выше как *mysql_create.yml*. Чтобы запустить сборник схем, используйте команду **ansible-playbook** следующим образом:
```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-firewall-rule"></a>Настройка правила брандмауэра
Правило брандмауэра на уровне сервера позволяет внешним приложениям, таким как программа командной строки **mysql** или MySQL Workbench, подключаться к серверу через брандмауэр службы Azure MySQL. В приведенном ниже примере создается правило брандмауэра с именем **extenalaccess**, которое разрешает подключения с любого внешнего IP-адреса. 

Подставьте свои значения **startIpAddress** и **endIpAddress** с диапазоном IP-адресов, которые соответствуют расположению, из которого будет устанавливаться подключение. 

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
> Подключитесь к базе данных Azure для MySQL через порт 3306. Если вы пытаетесь подключиться из корпоративной сети, исходящий трафик через порт 3306 может быть запрещен. В таком случае вы не сможете подключиться к серверу. Для этого ваш ИТ-отдел должен открыть порт 3306.
> 

Здесь для выполнения этой задачи используется модуль **azure_rm_resource**, который позволяет напрямую использовать REST API.

Сохраните сборник схем выше как *mysql_firewall.yml*. Чтобы запустить сборник схем, используйте команду **ansible-playbook** следующим образом:
```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server-using-command-line-tool"></a>Подключение к серверу с помощью программы командной строки
MySQL можно скачать [здесь](https://dev.mysql.com/downloads/) и установить на компьютер. Вместо этого можно также нажать кнопку **Попробуйте!** в примерах кода или кнопку `>_` на панели инструментов в правом верхнем углу портала Azure и запустить **Azure Cloud Shell**.

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
В следующем примере запрашиваются серверы MySQL в **myResourceGroup**, а затем все базы данных на сервере:

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

Сохраните сборник схем выше как *mysql_query*.yml. Чтобы запустить сборник схем, используйте команду **ansible-playbook** следующим образом:

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

А также увидите следующий результат для базы данных MySQL:
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

Если вам не нужны эти ресурсы, вы можете удалить их, выполнив пример ниже. Будет удалена группа ресурсов с именем **myResourceGroup**. 

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

Сохраните сборник схем выше как *rg_delete*.yml. Чтобы запустить сборник схем, используйте команду **ansible-playbook** следующим образом:
```bash
ansible-playbook rg_delete.yml
```

Если вы просто хотите удалить один созданный сервер MySQL, выполните команду, как показано в примере ниже:

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

Сохраните сборник схем выше как *mysql_delete.yml*. Чтобы запустить сборник схем, используйте команду **ansible-playbook** следующим образом:
```bash
ansible-playbook mysql_delete.yml
```

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"] 
> [Документация по Ansible в Azure](https://docs.microsoft.com/azure/ansible/)