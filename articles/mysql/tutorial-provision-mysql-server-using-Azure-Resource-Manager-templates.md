---
title: Руководство по Подготовка сервера Базы данных Azure для MySQL с помощью шаблона Azure Resource Manager
description: В этом руководстве объясняется, как подготовить и автоматизировать развертывания на сервере Базы данных Azure для MySQL с помощью шаблона Azure Resource Manager.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: json
ms.topic: tutorial
ms.date: 12/21/2018
ms.custom: mvc
ms.openlocfilehash: 6e4bb7622fe51c0cab4fc45e945e5bb07b1d32f1
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925839"
---
# <a name="tutorial-provision-an-azure-database-for-mysql-server-using-azure-resource-manager-template"></a>Руководство по Подготовка сервера Базы данных Azure для MySQL с помощью шаблона Azure Resource Manager

[REST API Базы данных Azure для MySQL](https://docs.microsoft.com/rest/api/mysql/) позволяет инженерам DevOps автоматизировать и интегрировать процессы подготовки, настройки и эксплуатации управляемых серверов MySQL и баз данных в Azure.  Этот интерфейс API позволяет создавать, перечислять, настраивать и удалять серверы MySQL и базы данных в службе Базы данных Azure для MySQL.

Azure Resource Manager с помощью базового REST API объявляет и программирует ресурсы Azure, необходимые для развертывания в любом масштабе, соблюдая концепцию "инфраструктура как код". Шаблон параметризует имя ресурса Azure, номер SKU, конфигурацию брандмауэра и другие параметры, что позволяет один раз создать шаблон для многократного использования.  Шаблоны Azure Resource Manager можно легко создать с помощью [портала Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal) или [Visual Studio Code](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code?tabs=CLI). Они позволяют организовать упаковку приложений, стандартизацию и автоматизацию развертывания, а также интеграцию в конвейер DevOps CI/CD.  Например, если вы намерены быстро развернуть службу веб-приложений с серверной частью на основе Базы данных Azure для MySQL, полное развертывание можно выполнить с помощью этого [шаблона быстрого запуска](https://azure.microsoft.com/resources/templates/101-webapp-managed-mysql/) из коллекции GitHub.

При работе с этим руководством вы будете использовать шаблон Azure Resource Manager и другие средства, чтобы выполнить следующие операции:

> [!div class="checklist"]
> * Создание Базы данных Azure для сервера MySQL с конечной точкой службы в виртуальной сети с помощью шаблона Azure Resource Manager
> * использование [программы командной строки MySQL](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) для создания базы данных.
> * Загрузка примера данных
> * Запрос данных
> * Обновление данных

Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.

## <a name="create-an-azure-database-for-mysql-server-with-vnet-service-endpoint-using-azure-resource-manager-template"></a>Создание Базы данных Azure для сервера MySQL с конечной точкой службы в виртуальной сети с помощью шаблона Azure Resource Manager

Чтобы получить ссылку на шаблон JSON для сервера Базы данных Azure для MySQL, откройте страницу шаблона для [сервера Microsoft.DBforMySQL](/azure/templates/microsoft.dbformysql/servers). Ниже приведен пример шаблона JSON, который позволяет создать новый сервер с Базой данных Azure для MySQL и конечной точкой службы в виртуальной сети.
```json
{
  "apiVersion": "2017-12-01",
  "type": "Microsoft.DBforMySQL/servers",
  "name": "string",
  "location": "string",
  "tags": "string",
  "properties": {
    "version": "string",
    "sslEnforcement": "string",
    "administratorLogin": "string",
    "administratorLoginPassword": "string",
    "storageProfile": {
      "storageMB": "string",
      "backupRetentionDays": "string",
      "geoRedundantBackup": "string"
    }
  },
  "sku": {
    "name": "string",
    "tier": "string",
    "capacity": "string",
    "family": "string"
  },
  "resources": [
    {
      "name": "AllowSubnet",
      "type": "virtualNetworkRules",
      "apiVersion": "2017-12-01",
      "properties": {
        "virtualNetworkSubnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
        "ignoreMissingVnetServiceEndpoint": true
      },
      "dependsOn": [
        "[concat('Microsoft.DBforMySQL/servers/', parameters('serverName'))]"
      ]
    }
  ]
}
```
В этом запросе нужно указать следующие значения.
+   `name`. Укажите имя сервера MySQL (без имени домена).
+   `location`. Укажите действительный регион Azure для центра обработки данных, где будет размещаться сервер MySQL. Например, westus2.
+   `properties/version`. Укажите версию сервера MySQL для развертывания. Например, 5.6 или 5.7.
+   `properties/administratorLogin`. Укажите имя администратора MySQL для входа на сервер. Не используйте для имени учетной записи администратора такие варианты: azure_superuser, admin, administrator, root, guest или public.
+   `properties/administratorLoginPassword`. Укажите пароль для администратора пользователя MySQL, имя которого указано выше.
+   `properties/sslEnforcement`. Укажите значение Enabled или Disabled, чтобы включить или отключить обязательное применение SSL.
+   `storageProfile/storageMB`. Укажите максимальный размер подготовленного хранилища, необходимый для сервера (в мегабайтах). Например, 5120.
+   `storageProfile/backupRetentionDays`. Укажите желаемый период удержания резервной копии (в днях). Например, 7. 
+   `storageProfile/geoRedundantBackup`. Укажите значение Enabled или Disabled, чтобы включить или отключить этот параметр в зависимости от требований Geo-DR.
+   `sku/tier`. Укажите для развертывания уровень Basic, GeneralPurpose или MemoryOptimized.
+   `sku/capacity`. Укажите количество виртуальных ядер. Возможные значения: 2, 4, 8, 16, 32 или 64.
+   `sku/family`. Укажите значение Gen5, чтобы выбрать поколение оборудования для развертывания сервера.
+   `sku/name`. Укажите значение TierPrefix_family_capacity. Например B_Gen5_1, GP_Gen5_16, MO_Gen5_32. Сведения о допустимых значениях для разных регионов и уровней можно найти в [документации по ценовым уровням](./concepts-pricing-tiers.md).
+   `resources/properties/virtualNetworkSubnetId`. Укажите идентификатор Azure для подсети в виртуальной сети, где следует разместить сервер Azure MySQL. 
+   `tags(optional)`. Укажите необязательные теги, то есть пары "ключ — значение" для распределения ресурсов по категориям для выставления счетов и (или) других целей.

Если вы намерены создать шаблон Azure Resource Manager для автоматизации развертываний Базы данных Azure для MySQL в своей организации, мы рекомендуем начать работу с [примера шаблона Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) из коллекции быстрого запуска для Azure в репозитории GitHub, а затем постепенно дорабатывать его. 

Если вы не знакомы с шаблонами Azure Resource Manager, но хотите попробовать работу с ними, начните с выполнения следующих действий.
+   Клонируйте или скачайте пример [шаблона Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) из коллекции быстрого запуска Azure.  
+   Измените файл Azuredeploy.parameters.json, указав нужные значения параметров, и сохраните этот файл. 
+   С помощью Azure CLI создайте сервер Azure MySQL, выполнив следующие команды.

Вы можете использовать Azure Cloud Shell в браузере или установить Azure CLI на локальный компьютер, чтобы выполнить блоки кода из этого руководства.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

```azurecli-interactive
az login
az group create -n ExampleResourceGroup  -l “West US2”
az group deployment create -g $ ExampleResourceGroup   --template-file $ {templateloc} --parameters $ {parametersloc}
```

## <a name="get-the-connection-information"></a>Получение сведений о подключении
Чтобы подключиться к серверу, необходимо указать сведения об узле и учетные данные для доступа.
```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Результаты выводятся в формате JSON. Запишите значения **fullyQualifiedDomainName** и **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus2",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
 "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-mysql"></a>Подключение к серверу с помощью MySQL
Используйте [программу командной строки MySQL](https://dev.mysql.com/doc/refman/5.6/en/mysql.html), чтобы подключиться к серверу базы данных Azure для MySQL. В этом примере используется следующая команда:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Создание пустой базы данных
Подключившись к серверу, создайте пустую базу данных.
```sql
mysql> CREATE DATABASE mysampledb;
```

Выполните следующую команду в командной строке, чтобы подключиться к созданной базе данных:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Создание таблиц в базе данных
Теперь, когда вы знаете, как подключиться к базе данных Azure для MySQL, выполните некоторые основные задачи.

Сначала создайте таблицу и заполните ее некоторыми данными. Давайте создадим таблицу, в которой хранятся данные инвентаризации.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Загрузка данных в таблицу
Теперь, когда таблица создана, мы можем вставить в нее данные. Чтобы вставить некоторые строки данных, в открытом окне командной строки выполните следующий запрос:
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Итак, в созданной ранее таблице содержится две строки данных.

## <a name="query-and-update-the-data-in-the-tables"></a>Запрос и обновление данных в таблицах
Чтобы извлечь сведения из таблицы базы данных, выполните приведенный ниже запрос.
```sql
SELECT * FROM inventory;
```

Вы можете также обновить данные в таблицах, выполнив следующую команду:
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

При извлечении данных строка будет обновляться соответствующим образом.
```sql
SELECT * FROM inventory;
```

## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как выполнять следующие операции:
> [!div class="checklist"]
> * Создание Базы данных Azure для сервера MySQL с конечной точкой службы в виртуальной сети с помощью шаблона Azure Resource Manager
> * использование [программы командной строки MySQL](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) для создания базы данных.
> * Загрузка примера данных
> * Запрос данных
> * Обновление данных
> 
> [Как подключить приложения к базе данных Azure для MySQL](./howto-connection-string.md)
