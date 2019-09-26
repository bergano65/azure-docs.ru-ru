---
title: Использование встроенных команд и функций записной книжки в Azure Cosmos DB
description: Узнайте, как использовать встроенные команды и функции для выполнения стандартных операций с помощью встроенных записных книжек Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: dech
ms.openlocfilehash: 1eda8271a3b8aa2c9e247252bd755279d23b6e10
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310351"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db"></a>Использование встроенных команд и функций записной книжки в Azure Cosmos DB

Встроенные записные книжки Jupyter в Azure Cosmos DB позволяют анализировать и визуализировать данные из портал Azure. В этой статье описывается, как использовать встроенные команды и функции записной книжки для выполнения стандартных операций.

## <a name="install-a-new-package"></a>Установка нового пакета
После включения поддержки ноутбуков для учетных записей Azure Cosmos можно открыть новую записную книжку и установить пакет.

В новой ячейке кода вставьте и выполните следующий код, заменив ``PackageToBeInstalled`` его на нужный пакет Python.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
Этот пакет будет доступен для использования из любой записной книжки в учетной записи Azure Cosmos. 

## <a name="run-a-sql-query"></a>Выполнение SQL-запроса

С помощью ``%%sql`` команды Magic можно выполнить [SQL-запрос](sql-query-getting-started.md) к любому контейнеру в вашей учетной записи. Используйте синтаксис:

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Замените ``{database_id}`` и``{container_id}`` именем базы данных и контейнера в учетной записи Cosmos. Если аргументы ``--container``ине указаны, запрос будет выполнен в [базе данных и контейнере по умолчанию.](#set-default-database-for-queries) ``--database``
- Можно выполнить любой SQL-запрос, допустимый в Azure Cosmos DB. Текст запроса должен находиться на новой строке.

Пример: 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Выполните ```%%sql?``` в ячейке, чтобы просмотреть справочную документацию по команде SQL Magic в записной книжке.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Выполнение SQL-запроса и выход в кадр данных Pandas

Результаты ``%%sql`` запроса можно вывести в [кадр данных Pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe). Используйте синтаксис: 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Замените ``{database_id}`` и``{container_id}`` именем базы данных и контейнера в учетной записи Cosmos. Если аргументы ``--container``ине указаны, запрос будет выполнен в [базе данных и контейнере по умолчанию.](#set-default-database-for-queries) ``--database``
- Замените ``{outputDataFrameVar}`` на имя переменной кадра данных, которая будет содержать результаты.
- Можно выполнить любой SQL-запрос, допустимый в Azure Cosmos DB. Текст запроса должен находиться на новой строке. 

Пример:

```bash
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```bash
df_cosmos.head(10)

    Action  ItemRevenue Country Item
0   Viewed  9.00    Tunisia Black Tee
1   Viewed  19.99   Antigua and Barbuda Flannel Shirt
2   Added   3.75    Guinea-Bissau   Socks
3   Viewed  3.75    Guinea-Bissau   Socks
4   Viewed  55.00   Czech Republic  Rainjacket
5   Viewed  350.00  Iceland Cosmos T-shirt
6   Added   19.99   Syrian Arab Republic    Button-Up Shirt
7   Viewed  19.99   Syrian Arab Republic    Button-Up Shirt
8   Viewed  33.00   Tuvalu  Red Top
9   Viewed  14.00   Cape Verde  Flip Flop Shoes
```

## <a name="set-default-database-for-queries"></a>Задать базу данных по умолчанию для запросов
Вы можете задать команды базы данных ```%%sql``` по умолчанию, которые будут использоваться для записной книжки. Замените ```{database_id}``` на имя базы данных.

```bash
%database {database_id}
```
Выполните ```%database?``` в ячейке, чтобы просмотреть документацию в записной книжке.

## <a name="set-default-container-for-queries"></a>Задать контейнер по умолчанию для запросов
Вы можете задать команды контейнера ```%%sql``` по умолчанию, которые будут использоваться для записной книжки. Замените ```{container_id}``` на имя контейнера.

```bash
%container {container_id}
```
Выполните ```%container?``` в ячейке, чтобы просмотреть документацию в записной книжке.

## <a name="use-the-built-in-python-sdk"></a>Использование встроенного пакета SDK для Python
Версия 4 [пакета SDK для Azure Cosmos DB Python для SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) установлена и включена в среду записной книжки для учетной записи Cosmos.

Используйте встроенный ``cosmos_client`` экземпляр для выполнения любой операции SDK. 

Пример:

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
См. раздел [примеры пакетов SDK для Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples). 

> [!IMPORTANT]
> Встроенный пакет SDK для Python поддерживается только для учетных записей API SQL (ядра). Для других API необходимо [установить соответствующий драйвер Python](#install-a-new-package) , соответствующий API. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Создание пользовательского экземпляра``cosmos_client``
Для большей гибкости можно создать пользовательский экземпляр ``cosmos_client`` , чтобы:

- Настройка [политики подключения](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview)
- Выполнение операций с другой учетной записью Cosmos, отличной от вашей

Вы можете получить доступ к строке подключения и первичному ключу текущей учетной записи через [переменные среды](#access-the-account-endpoint-and-primary-key-env-variables). 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(os.environ["COSMOS_ENDPOINT"], {'masterKey': os.environ["COSMOS_KEY"]}, connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Доступ к конечной точке учетной записи и переменным основного ключа env
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> Переменные среды ``COSMOS_KEY`` и применимы только для API SQL. ``COSMOS_ENDPOINT`` Для других API-интерфейсов найдите конечную точку и ключ в колонке **строки подключения** или **ключи** в учетной записи Cosmos.  

## <a name="next-steps"></a>Следующие шаги

- Узнайте о преимуществах [записных книжек Azure Cosmos DB Jupyter](cosmosdb-jupyter-notebooks.md)
- Дополнительные сведения о [пакете SDK для Azure Cosmos DB Python для API SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
