---
title: Преобразование данных с помощью кирпичей Python
description: Сведения об обработке или преобразовании данных с помощью Databricks Python.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/15/2018
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: anandsub
ms.custom: devx-track-python
ms.openlocfilehash: a42c7d911793e1b4ef4183c5d1f02f8be7d4ded3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87872299"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Преобразование данных с помощью выполнения действий Python в Azure Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Действие Python в Azure Databricks в [конвейере фабрики данных](concepts-pipelines-activities.md) позволяет запустить файл Python в кластере Azure Databricks. Эта статья посвящена [действиям преобразования данных](transform-data.md)   , в которых представлен общий обзор преобразования данных и поддерживаемых действий преобразования.Azure Databricks — это управляемая платформа для запуска Apache Spark.

Уделите 11 минут вашего времени, чтобы просмотреть следующее видео с кратким обзором и демонстрацией этой функции:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Определение действия Python в Databricks

Пример определения JSON для действия Python в Databricks:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Свойства действия Python в Databricks

В следующей таблице приведено описание свойств, используемых в определении JSON.

|Свойство|Описание|Обязательно|
|---|---|---|
|name|Имя действия в конвейере.|Да|
|description|Описание действия.|Нет|
|type|Тип действия Python в Databricks — DatabricksSparkPython.|Да|
|linkedServiceName|Имя связанной службы Databricks, в которой выполняется действие Python. Дополнительные сведения об этой связанной службе см. в статье [связанные службы вычислений](compute-linked-services.md)   .|Да|
|pythonFile|URI файла Python, который будет выполнен. Поддерживаются только пути DBFS.|Да|
|параметры|Параметры командной строки, которые будут переданы в файл Python. Массив строк.|Нет|
|libraries|Список библиотек, которые должны быть установлены на кластере, на котором будет выполнено задание. Массив объектов <строка, объект>|Нет|

## <a name="supported-libraries-for-databricks-activities"></a>Поддерживаемые библиотеки для действий Databricks

В приведенном выше определении действия Databricks необходимо указать следующие типы библиотек: *jar*, *egg*, *maven*, *pypi*, *cran*.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Дополнительные сведения см. в [документации Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) по типам библиотек.

## <a name="how-to-upload-a-library-in-databricks"></a>Отправка библиотеки в Databricks

#### <a name="using-databricks-workspace-ui"></a>[С помощью пользовательского интерфейса рабочей области Databricks](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Чтобы получить путь к dbfs библиотеке, добавленной с помощью пользовательского интерфейса, можно использовать [интерфейс командной строки Databricks (установка)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Обычно библиотеки Jar, добавленные с помощью пользовательского интерфейса, хранятся в каталоге dbfs:/FileStore/jars. Вы можете вывести список всех с помощью интерфейса командной строки: *dbfss FS Ls:/хранилище файлов/JAR* 



#### <a name="copy-library-using-databricks-cli"></a>[Копирование библиотеки с помощью интерфейса командной строки Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Пример: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*
