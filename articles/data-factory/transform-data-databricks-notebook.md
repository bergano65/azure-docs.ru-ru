---
title: Преобразование данных с помощью ноутбука Databricks
description: Узнайте, как обрабатывать и преобразовывать данные с помощью записной книжки Databricks.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: nabhishek
ms.author: abnarain
manager: shwang
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: c7a2aec35511ef066033c3d6462143ac31660e76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74923065"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Преобразование данных с помощью записной книжки Databricks

Деятельность ноутбука Azure Databricks в [конвейере Data Factory](concepts-pipelines-activities.md) запускает блокнот Databricks в рабочем пространстве Azure Databricks. Эта статья основывается на статье, в которой представлены действия по преобразованию [данных,](transform-data.md)в котором представлен общий обзор трансформации данных и поддерживаемых мероприятий преобразования.Azure Databricks — это управляемая платформа для запуска Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Определение действия Databricks Notebook

Пример определения JSON для действия Databricks Notebook:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Свойства действия Databricks Notebook

В следующей таблице приведено описание свойств, используемых в определении JSON.

|Свойство|Описание|Обязательно|
|---|---|---|
|name|Имя действия в конвейере.|Да|
|description|Описание действия.|нет|
|type|Тип действия Databricks Notebook — DatabricksNotebook.|Да|
|linkedServiceName|Имя связанной службы Databricks, в которой запускается записная книжка Databricks. Чтобы узнать об этой связанной службе, смотрите статью о  [подключенных службах Compute.](compute-linked-services.md)|Да|
|notebookPath|Абсолютный путь записной книжки, которая будет запущена в рабочей области Databricks. Этот путь должен начинаться с косой черты.|Да|
|baseParameters|Массив пар "ключ-значение". Для каждого выполнения действия можно использовать базовые параметры. Если записная книжка принимает параметр, который не был указан, используется значение по умолчанию из записной книжки. Дополнительные сведения о параметрах Databricks Notebook см. [здесь](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|нет|
|libraries|Список библиотек, которые должны быть установлены на кластере, на котором будет выполнено задание. Массив объектов \<строка, объект>.|нет|


## <a name="supported-libraries-for-databricks-activities"></a>Поддерживаемые библиотеки для действий Databricks

В приведенном выше определении активности Databricks вы указываете эти типы библиотеки: *банку,* *яйцо,* *whl,* *maven,* *pypi,* *кран.*

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
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
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

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>Прохождение параметров между ноутбуками и фабрикой данных

Параметры фабрики данных можно передавать в блокноты с помощью свойства *baseParameters* в деятельности databricks. 

В некоторых случаях может потребоваться вернуть определенные значения из блокнота обратно на фабрику данных, которые могут быть использованы для управления потоком (условные проверки) на фабрике данных или потреблены деятельностью ниже по течению (ограничение размера составляет 2 МБ). 

1. В блокноте вы можете вызвать [dbutils.notebook.exit ("returnValue")](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-workflows.html#notebook-workflows-exit) и соответствующие "returnValue" будут возвращены на фабрику данных.

2. Вы можете использовать выход на фабрике `'@activity('databricks notebook activity name').output.runOutput'`данных, используя такие выражения, как . 

   > [!IMPORTANT]
   > Если вы проходите объект JSON, вы можете получить значения, применяя имена свойств. Например, `'@activity('databricks notebook activity name').output.runOutput.PropertyName'`.

## <a name="how-to-upload-a-library-in-databricks"></a>Отправка библиотеки в Databricks

#### <a name="using-databricks-workspace-ui"></a>[С помощью пользовательского интерфейса рабочей области Databricks](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Чтобы получить путь к dbfs библиотеки, добавленной с помощью пользовательского интерфейса, можно использовать [интерфейс командной строки Databricks (установка)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Обычно библиотеки Jar, добавленные с помощью пользовательского интерфейса, хранятся в каталоге dbfs:/FileStore/jars. Вы можете получить список всех библиотек, выполнив следующую команду в интерфейсе командной строки: *databricks fs ls dbfs:/FileStore/jars*.



#### <a name="copy-library-using-databricks-cli"></a>[Копирование библиотеки с помощью интерфейса командной строки Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Пример: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*
