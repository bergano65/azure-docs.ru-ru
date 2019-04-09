---
title: Доступ к ресурсам данных из записных книжек Jupyter в Azure
description: Сведения о получении доступа к файлам, REST API, базам данных и различным ресурсам службы хранилища Azure из записной книжки Jupyter.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: ee867303-a5e5-4686-b2da-8a0108247d18
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 14a4191612a5d42836ae4be3ff902ca47a6b06d4
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271524"
---
# <a name="access-cloud-data-in-a-notebook"></a>Доступ к облачным данным в записной книжке

Выполнение различных операций в записной книжке Jupyter требует использования данных. Данные, действительно, являются основополагающим элементом записных книжек.

Конечно, вы можете [импортировать файлы данных в проект](work-with-project-data-files.md), даже используя такие команды, как `curl`, из записной книжки для загрузки файла напрямую. Однако, вероятно, вам нужно будет работать с более обширными данными, доступными из нефайловых источников, таких как API REST, реляционные базы данных и облачное хранилище, например таблицы Azure.

В данной статье кратко описаны эти различные варианты. Так как доступ к данным лучше всего описывать в действии, см. исполняемый код на [сайте GitHub](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

## <a name="rest-apis"></a>REST API

В целом, к огромному количеству данных, доступных в Интернете, можно получить доступ не через файлы, а через REST API. К счастью, вы можете использовать код для отправки запросов и получения данных JSON, так как ячейка записной книжки может содержать любой код. Затем вы можете преобразовать этот JSON в любой необходимый формат, например кадр данных Pandas.

Чтобы получить доступ к данным с помощью REST API, используйте код в ячейках записной книжки, применяемый в любом другом приложении. Общая структура с использованием библиотеки запросов выглядит следующим образом:

```python
import pandas
import requests

# New York City taxi data for 2014
data_url = 'https://data.cityofnewyork.us/resource/gkne-dk5s.json'

# General data request; include other API keys and credentials as needed in the data argument
response = requests.get(data_url, data={"limit" : "20"})

if response.status_code == 200:
    dataframe_rest2 = pandas.DataFrame.from_records(response.json())
    print(dataframe_rest2)
```

## <a name="azure-sql-databases"></a>Базы данных SQL Azure

Вы можете получить доступ к базам данных SQL Server с помощью библиотек pyodbc или pymssql.

В статье [Краткое руководство. Использование Python для создания запросов к базе данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) приведены инструкции по созданию базы данных, содержащей данные AdventureWorks, и выполнению запроса к данным. Тот же код использован в примере записной книжки в этой статье.

## <a name="azure-storage"></a>Служба хранилища Azure

Служба хранилища Azure предоставляет несколько различных типов нереляционных хранилищ, в зависимости от типа имеющихся данных и способа доступа к ним:

- Хранилище таблиц. Предоставляет недорогое хранилище большого объема для табличных данных, таких как собранные журналы датчиков, журналы диагностики и т. д.
- Хранилище BLOB-объектов. Предоставляет файловое хранилище для любых типов данных.

В этом примере записной книжки демонстрируется работа с таблицами и большими двоичными объектами, в том числе использование подписанного URL-адреса для предоставления доступа на чтение к большим двоичным объектам.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB предоставляет полностью индексируемое хранилище NoSQL для документов JSON. В следующих статьях представлены различные способы работы с Cosmos DB с помощью Python:

- [Приложения сборки SQL API с помощью Python](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Создание приложения Flask с помощью API Azure Cosmos DB для MongoDB](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Создать базу данных графов, используя Python и Gremlin API](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Создание приложения Cassandra с помощью Python и Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Создание приложения API таблиц с помощью Python и Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

При работе с Cosmos DB вы можете использовать библиотеку [azure-cosmosdb-table](https://pypi.org/project/azure-cosmosdb-table/).

## <a name="other-azure-databases"></a>Другие базы данных Azure

Azure предоставляет ряд других доступных для использования типов баз данных. Приведенные ниже статьи содержат рекомендации по получению доступа к этим базам данных с помощью Python:

- [База данных Azure для PostgreSQL: подключение и запрос данных с помощью Python](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Краткое руководство. Использование кэша redis для Azure с Python](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [База данных Azure для MySQL: подключение и запрос данных с помощью Python](https://docs.microsoft.com/azure/mysql/connect-python)
- [Фабрика данных Azure](https://azure.microsoft.com/services/data-factory/)
  - [Мастер копирования фабрики данных Azure](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Дальнейшие действия

- [Практическое руководство. Работа с файлами данных проекта](work-with-project-data-files.md)
