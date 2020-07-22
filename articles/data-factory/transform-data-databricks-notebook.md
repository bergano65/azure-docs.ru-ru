---
title: Преобразование данных с помощью записной книжки кирпичей
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
ms.openlocfilehash: 6d3c9f0df0d834ffe75d0b56e3c80a432c27ea38
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81419023"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Преобразование данных с помощью записной книжки Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Действие Azure Databricks записной книжке в [конвейере фабрики данных](concepts-pipelines-activities.md) запускает записную книжку "кирпичы" в рабочей области Azure Databricks. Эта статья посвящена [действиям преобразования данных](transform-data.md)   , в которых представлен общий обзор преобразования данных и поддерживаемых действий преобразования.Azure Databricks — это управляемая платформа для запуска Apache Spark.

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
|description|Описание действия.|Нет|
|type|Тип действия Databricks Notebook — DatabricksNotebook.|Да|
|linkedServiceName|Имя связанной службы Databricks, в которой запускается записная книжка Databricks. Дополнительные сведения об этой связанной службе см. в статье [связанные службы вычислений](compute-linked-services.md)   .|Да|
|notebookPath|Абсолютный путь записной книжки, которая будет запущена в рабочей области Databricks. Этот путь должен начинаться с косой черты.|Да|
|baseParameters|Массив пар "ключ-значение". Для каждого выполнения действия можно использовать базовые параметры. Если записная книжка принимает параметр, который не был указан, используется значение по умолчанию из записной книжки. Дополнительные сведения о параметрах Databricks Notebook см. [здесь](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Нет|
|libraries|Список библиотек, которые должны быть установлены на кластере, на котором будет выполнено задание. Это может быть массив \<string, object> .|Нет|


## <a name="supported-libraries-for-databricks-activities"></a>Поддерживаемые библиотеки для действий Databricks

В приведенном выше определении действия "кирпичы данных" указываются следующие типы библиотек: *JAR*, *Egg*, *WHL*, *Maven*, *PyPI*, *Cran*.

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

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>Передача параметров между записными книжками и фабрикой данных

Параметры фабрики данных можно передать в записные книжки с помощью свойства *басепараметерс* в действии модуля данных. 

В некоторых случаях может потребоваться передача определенных значений из записной книжки обратно в фабрику данных, которую можно использовать для потока управления (условные проверки) в фабрике данных или для использования нисходящими действиями (ограничение размера — 2 МБ). 

1. В записной книжке вы можете вызвать [дбутилс. Notebook. Exit ("ReturnValue")](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-workflows.html#notebook-workflows-exit) , и в фабрике данных будет возвращен соответствующий результат "ReturnValue".

2. Выходные данные в фабрике данных можно использовать с помощью выражения, такого как `'@activity('databricks notebook activity name').output.runOutput'` . 

   > [!IMPORTANT]
   > При передаче объекта JSON значения можно получить, добавив имена свойств. Пример: `'@activity('databricks notebook activity name').output.runOutput.PropertyName'`

## <a name="how-to-upload-a-library-in-databricks"></a>Отправка библиотеки в Databricks

#### <a name="using-databricks-workspace-ui"></a>[С помощью пользовательского интерфейса рабочей области Databricks](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Чтобы получить путь к dbfs библиотеки, добавленной с помощью пользовательского интерфейса, можно использовать [интерфейс командной строки Databricks (установка)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Обычно библиотеки Jar, добавленные с помощью пользовательского интерфейса, хранятся в каталоге dbfs:/FileStore/jars. Вы можете получить список всех библиотек, выполнив следующую команду в интерфейсе командной строки: *databricks fs ls dbfs:/FileStore/jars*.



#### <a name="copy-library-using-databricks-cli"></a>[Копирование библиотеки с помощью интерфейса командной строки Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Пример: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*
