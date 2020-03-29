---
title: Используйте встроенные команды и функции ноутбуков в Azure Cosmos DB (предварительный просмотр)
description: Узнайте, как использовать встроенные команды и функции для проведения обычных операций с помощью встроенных ноутбуков Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: dech
ms.openlocfilehash: 61d46bbf0ccdeb5cd2e95e36e19f1aa81cfeeb48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513405"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-preview"></a>Используйте встроенные команды и функции ноутбуков в Azure Cosmos DB (предварительный просмотр)

Встроенные ноутбуки Jupyter в Azure Cosmos DB позволяют анализировать и визуализировать данные с портала Azure. В этой статье описывается, как использовать встроенные команды и функции записной книжки для выполнения стандартных операций.

## <a name="install-a-new-package"></a>Установка нового пакета
После включения поддержки ноутбуков для учетных записей Azure Cosmos можно открыть новый блокнот и установить пакет.

В новой ячейке кода вставьте и запустите следующий код, заменив ``PackageToBeInstalled`` нужный пакет Python.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
Этот пакет будет доступен для использования из любого ноутбука в рабочем пространстве учетной записи Azure Cosmos. 

> [!TIP]
> Если ваш ноутбук требует пользовательского пакета, мы рекомендуем добавить ячейку в блокнот для установки пакета, так как пакеты удаляются при [сбросить рабочее пространство.](#reset-notebooks-workspace)  

## <a name="run-a-sql-query"></a>Выполнить запрос на S'L

Можно использовать ``%%sql`` волшебную команду для выполнения [запроса s'L](sql-query-getting-started.md) против любого контейнера в вашей учетной записи. Используйте синтаксис:

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- ``{database_id}`` Замените ``{container_id}`` и с именем базы данных и контейнера в вашей учетной записи Космос. Если ``--database`` аргументы не ``--container`` предоставлены, запрос будет выполнен в [базе данных по умолчанию и контейнере.](#set-default-database-for-queries)
- Вы можете запустить любой запрос S'L, действительный в Azure Cosmos DB. Текст запроса должен быть на новой строке.

Пример: 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Выполнить ```%%sql?``` в ячейке, чтобы увидеть справочную документацию для команды sql магии в блокноте.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Выполнить запрос и вывести на вывод данных Pandas DataFrame

Результаты ``%%sql`` запроса можно выставить в [Pandas DataFrame.](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe) Используйте синтаксис: 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- ``{database_id}`` Замените ``{container_id}`` и с именем базы данных и контейнера в вашей учетной записи Космос. Если ``--database`` аргументы не ``--container`` предоставлены, запрос будет выполнен в [базе данных по умолчанию и контейнере.](#set-default-database-for-queries)
- Замените ``{outputDataFrameVar}`` имя переменной DataFrame, которая будет содержать результаты.
- Вы можете запустить любой запрос S'L, действительный в Azure Cosmos DB. Текст запроса должен быть на новой строке. 

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
## <a name="upload-json-items-to-a-container"></a>Загрузка элементов JSON в контейнер
Волшебная ``%%upload`` команда может загрузить данные из файла JSON в указанный контейнер Azure Cosmos. Используйте следующую команду для загрузки элементов:

```bash
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- ``{database_id}`` Замените ``{container_id}`` и с именем базы данных и контейнера в вашей учетной записи Azure Cosmos. Если ``--database`` аргументы не ``--container`` предоставлены, запрос будет выполнен в [базе данных по умолчанию и контейнере.](#set-default-database-for-queries)
- Замените ``{url_location_of_file}`` местоположение файла JSON. Файл должен быть массивом действительных объектов JSON и должен быть доступен через общедоступный Интернет.

Пример:

```bash
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```bash
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
С помощью статистики вывода можно рассчитать эффективные RU/s, используемые для загрузки элементов. Например, если 25 000 РУ были употребления в течение 38 секунд, то эффективный RU/s составляет 25 000 r.Ru / 38 секунд, 658 RU/s.

## <a name="set-default-database-for-queries"></a>Установка базы данных по умолчанию для запросов
Команды базы данных ```%%sql``` по умолчанию можно использовать для ноутбука. Замените ```{database_id}``` на имя вашей базы данных.

```bash
%database {database_id}
```
Выполнить ```%database?``` в ячейке, чтобы увидеть документацию в блокноте.

## <a name="set-default-container-for-queries"></a>Установка контейнера по умолчанию для запросов
Команды контейнера ```%%sql``` по умолчанию можно использовать для ноутбука. Замените ```{container_id}``` имя контейнера.

```bash
%container {container_id}
```
Выполнить ```%container?``` в ячейке, чтобы увидеть документацию в блокноте.

## <a name="use-built-in-nteract-data-explorer"></a>Используйте встроенный исследователь данных nteract
Для фильтрации и визуализации DataFrame можно использовать встроенный [исследователь данных nteract.](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) Для включения этой функции ``pd.options.display.html.table_schema`` ``True`` установите опцию и ``pd.options.display.max_rows`` желаемое значение (вы можете установить, ``pd.options.display.max_rows`` чтобы ``None`` показать все результаты).

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
![исследователь данных nteract](media/use-notebook-features-and-commands/nteract-built-in-chart.png)

## <a name="use-the-built-in-python-sdk"></a>Используйте встроенный Python SDK
Версия 4 [Azure Cosmos DB Python SDK для API для S'L](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) установлена и включена в среду для учетной записи Azure Cosmos.

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
Смотрите [образцы Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples). 

> [!IMPORTANT]
> Встроенный Python SDK поддерживается только для учетных записей API S'L (Core). Для других API необходимо [установить соответствующий драйвер Python,](#install-a-new-package) соответствующий API. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Создание пользовательского экземпляра``cosmos_client``
Для большей гибкости можно создать ``cosmos_client`` пользовательский экземпляр, чтобы:

- Настройка [политики подключения](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview)
- Запуск операций против другой учетной записи Azure Cosmos, чем тот, в который вы находитесь

Вы можете получить доступ к строке подключения и основному ключу текущего счета через [переменные среды.](#access-the-account-endpoint-and-primary-key-env-variables) 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=os.environ["COSMOS_ENDPOINT"], credential=os.environ["COSMOS_KEY"], connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Доступ к конечным точкам счета и основным ключевым переменным env
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> ``COSMOS_ENDPOINT`` Переменные ``COSMOS_KEY`` среды и среды применимы только для API S'L. Для других AA найдите конечную точку и ключ в лезвии **строк соединения** или **ключей** в вашей учетной записи Azure Cosmos.  

## <a name="reset-notebooks-workspace"></a>Рабочее пространство сбросить ноутбуки
Чтобы сбросить рабочее пространство ноутбуков в настройки по умолчанию, выберите **рабочее пространство сброса** в панели команд. Это позволит удалить все установленные пакеты и перезапустить сервер Jupyter. Ваши блокноты, файлы и ресурсы Azure Cosmos не будут затронуты.  

![Рабочее пространство сбросить ноутбуки](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте о преимуществах [ноутбуков Azure Cosmos DB Jupyter](cosmosdb-jupyter-notebooks.md)
- Узнайте о [SDK-канале Azure Cosmos DB Python для API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
