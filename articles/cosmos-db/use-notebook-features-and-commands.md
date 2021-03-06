---
title: Использование встроенных команд и функций записной книжки в Azure Cosmos DB
description: Узнайте, как использовать встроенные команды и функции для выполнения стандартных операций с помощью встроенных записных книжек Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: dech
ms.openlocfilehash: f5ab1491c8561c90b06374a0a58f160cbcdd1cad
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444644"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db"></a>Использование встроенных команд и функций записной книжки в Azure Cosmos DB

Встроенные записные книжки Jupyter в Azure Cosmos DB позволяют анализировать и визуализировать данные из портал Azure. В этой статье описывается, как использовать встроенные команды и функции записной книжки для выполнения стандартных операций.

## <a name="install-a-new-package"></a>Установка нового пакета
После включения поддержки ноутбуков для учетных записей Azure Cosmos можно открыть новую записную книжку и установить пакет.

В новой ячейке кода вставьте и выполните следующий код, заменив ``PackageToBeInstalled`` на нужный пакет Python.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
Этот пакет будет доступен для использования из любой записной книжки в рабочей области учетной записи Azure Cosmos. 

> [!TIP]
> Если записной книжке требуется пользовательский пакет, мы рекомендуем добавить в записную книжку ячейку для установки пакета, так как пакеты удаляются при [сбросе рабочей области](#reset-notebooks-workspace).  

## <a name="run-a-sql-query"></a>Выполнение SQL-запроса

Команду ``%%sql`` Magic можно использовать для выполнения [SQL-запроса](sql-query-getting-started.md) к любому контейнеру в вашей учетной записи. Используйте синтаксис:

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Замените ``{database_id}`` и ``{container_id}`` именем базы данных и контейнера в учетной записи Cosmos. Если аргументы ``--database`` и ``--container`` не указаны, запрос будет выполнен для [базы данных по умолчанию и контейнера](#set-default-database-for-queries).
- Можно выполнить любой SQL-запрос, допустимый в Azure Cosmos DB. Текст запроса должен находиться на новой строке.

Пример. 
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
- Замените ``{database_id}`` и ``{container_id}`` именем базы данных и контейнера в учетной записи Cosmos. Если аргументы ``--database`` и ``--container`` не указаны, запрос будет выполнен для [базы данных по умолчанию и контейнера](#set-default-database-for-queries).
- Замените ``{outputDataFrameVar}`` именем переменной кадров данных, которая будет содержать результаты.
- Можно выполнить любой SQL-запрос, допустимый в Azure Cosmos DB. Текст запроса должен находиться на новой строке. 

Пример.

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
## <a name="upload-json-items-to-a-container"></a>Отправка элементов JSON в контейнер
Команду ``%%upload`` Magic можно использовать для передачи данных из JSON-файла в указанный контейнер Azure Cosmos. Для отправки элементов используйте следующую команду:

```bash
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Замените ``{database_id}`` и ``{container_id}`` именем базы данных и контейнера в учетной записи Azure Cosmos. Если аргументы ``--database`` и ``--container`` не указаны, запрос будет выполнен для [базы данных по умолчанию и контейнера](#set-default-database-for-queries).
- Замените ``{url_location_of_file}`` расположением файла JSON. Файл должен быть массивом допустимых объектов JSON, и он должен быть доступен через общедоступный Интернет.

Пример.

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
С помощью выходной статистики можно вычислить действующие единицы запросов в секунду, используемые для отправки элементов. Например, если 25 000 RUs использовался свыше 38 секунд, то действующий единица запросов в секунду составляет 25 000/38 секунд = 658 единиц запросов в секунду.

## <a name="set-default-database-for-queries"></a>Задать базу данных по умолчанию для запросов
Вы можете задать ```%%sql``` базы данных по умолчанию, которые будут использоваться для записной книжки. Замените ```{database_id}``` на имя вашей базы данных.

```bash
%database {database_id}
```
Выполните ```%database?``` в ячейке, чтобы просмотреть документацию в записной книжке.

## <a name="set-default-container-for-queries"></a>Задать контейнер по умолчанию для запросов
Вы можете задать контейнер по умолчанию, ```%%sql``` команды будут использоваться для записной книжки. Замените ```{container_id}``` именем своего контейнера.

```bash
%container {container_id}
```
Выполните ```%container?``` в ячейке, чтобы просмотреть документацию в записной книжке.

## <a name="use-built-in-nteract-data-explorer"></a>Использование встроенного обозревателя данных нтеракт
С помощью встроенного [обозревателя данных нтеракт](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) можно фильтровать и визуализировать кадры данные. Чтобы включить эту функцию, задайте для параметра ``pd.options.display.html.table_schema`` значение ``True`` и ``pd.options.display.max_rows`` требуемому значению (можно задать ``pd.options.display.max_rows`` ``None``, чтобы отобразить все результаты).

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
![Обозреватель данных нтеракт](media/use-notebook-features-and-commands/nteract-built-in-chart.png)

## <a name="use-the-built-in-python-sdk"></a>Использование встроенного пакета SDK для Python
Версия 4 [пакета SDK для Azure Cosmos DB Python для SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) установлена и включена в среду записных книжек для учетной записи Azure Cosmos.

Используйте встроенный экземпляр ``cosmos_client`` для выполнения любой операции SDK. 

Пример.

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

## <a name="create-a-custom-instance-of-cosmos_client"></a>Создание пользовательского экземпляра ``cosmos_client``
Для большей гибкости можно создать пользовательский экземпляр ``cosmos_client`` в следующих целях:

- Настройка [политики подключения](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview)
- Выполнение операций с другой учетной записью Azure Cosmos, отличной от вашей

Вы можете получить доступ к строке подключения и первичному ключу текущей учетной записи через [переменные среды](#access-the-account-endpoint-and-primary-key-env-variables). 

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
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Доступ к конечной точке учетной записи и переменным основного ключа env
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> Переменные среды ``COSMOS_ENDPOINT`` и ``COSMOS_KEY`` применимы только для API SQL. Для других API-интерфейсов найдите конечную точку и ключ в колонке **строки подключения** или **ключи** в учетной записи Azure Cosmos.  

## <a name="reset-notebooks-workspace"></a>Сброс рабочей области записных книжек
Чтобы сбросить рабочую область записных книжек до значений по умолчанию, выберите **сбросить рабочую область** на панели команд. При этом будут удалены все пользовательские установленные пакеты и перезапущен сервер Jupyter. Ваши записные книжки, файлы и ресурсы Azure Cosmos не будут затронуты.  

![Сброс рабочей области записных книжек](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте о преимуществах [записных книжек Azure Cosmos DB Jupyter](cosmosdb-jupyter-notebooks.md)
- Дополнительные сведения о [пакете SDK для Azure Cosmos DB Python для API SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
