---
title: 'Запись: пакет SDK Python для подготовки данных'
titleSuffix: Azure Machine Learning service
description: Узнайте, как записывать данные с помощью пакета SDK для подготовки данных Машинного обучения Azure. Можно записать данные в любой точке потока данных, а также в файлы в любом из поддерживаемых расположений (локальная файловая система, хранилище BLOB-объектов Azure и Azure Data Lake Storage).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 6206ad1a7356221bf94134e5d293c27d778cc187
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752869"
---
# <a name="write-and-configure-data--with-the-azure-machine-learning-data-prep-sdk"></a>Запись и настроить данные с помощью пакета SDK Azure Machine Learning данных подготовки

В этой статье вы узнаете, различные методы для записи данных с помощью [машины обучения данные подготовки пакета SDK Azure Python](https://aka.ms/data-prep-sdk) и настройке этих данных для экспериментов с [Azure Machine Learning и пакет SDK для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Выходные данные могут быть написаны в любой момент в потоке данных. Записи добавляются в результирующий поток данных и эти шаги выполнения каждый раз запусков потока данных. Если разрешить параллельные операции записи, данные будут записываться в несколько файлов раздела.

> [!Important]
> Если вы создаете новое решение, попробуйте [наборы данных обучения машины Azure](how-to-explore-prepare-data.md) (Предварительная версия) для преобразования данных, данные моментального снимка и хранения определений с версией набора данных. Наборы данных — следующая версия пакета SDK, предлагая расширенными функциональными возможностями для управления наборами данных в решения искусственного Интеллекта подготовки данных.
> Если вы используете `azureml-dataprep` пакет, чтобы создать поток данных с помощью преобразования вместо использования `azureml-datasets` пакета для создания набора данных, вы не сможете использовать моментальные снимки или наборов данных с версией более поздней версии.

Так как нет никаких ограничений по количеству шагов записи в конвейере, можно легко добавить дополнительные шаги записи, чтобы устранить неполадки или передать их для других конвейеров.

Каждый раз, когда вы запускаете шаг записи, происходит полное извлечение данных в потоке данных. Например, поток данных с тремя шагами записи будет считывать и обрабатывать каждую запись в наборе данных три раза.

## <a name="supported-data-types-and-location"></a>Поддерживаемые типы данных и расположение

Поддерживаются следующие форматы файлов:
-   файлы с разделителями (CSV, TSV и т. д.);
-   файлы Parquet;

Пакет SDK данных подготовки Python Azure Machine Learning может записывать данные:
+ локальную файловую систему;
+ Хранилище BLOB-объектов Azure
+ Azure Data Lake Storage

## <a name="spark-considerations"></a>Рекомендации для Spark

Выполнение потока данных в Spark необходимо записывать в пустую папку. Попытка выполнить запись в существующую папку завершится ошибкой. Убедитесь, что папка целевого объекта пуста или использует другое целевое расположение для каждого запуска, иначе запись завершится ошибкой.

## <a name="monitoring-write-operations"></a>Мониторинг операций записи

Для вашего удобства после завершения записи создается отдельный файл с именем SUCCESS. Его наличие поможет определить, когда закончится запись в промежуточном режиме, без необходимости ожидать завершения всего конвейера.

## <a name="example-write-code"></a>Пример кода записи

Например, начните с загрузки данных в поток данных с помощью `auto_read_file()`. Эти данные будут повторно использоваться в разных форматах.

```python
import azureml.dataprep as dprep
t = dprep.auto_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(5)
```

Выходные данные примера:

| | Column1 | Column2 | Column3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000,0 | 99999,0 | Нет | НЕТ | НЕТ | ENRS | NaN | NaN | NaN |   
|1| 10003,0 | 99999,0 | Нет | НЕТ | НЕТ | ENSO | NaN | NaN | NaN |   
|2| 10010,0 | 99999,0 | Нет | НЕТ | JN | ENJA | 70933,0 | –8667,0 | 90,0 |
|3| 10013,0 | 99999,0 | Нет | НЕТ | НЕТ |      | NaN | NaN | NaN |
|4.| 10014,0 | 99999,0 | Нет | НЕТ | НЕТ | ENSO | 59783,0 | 5350,0 |  500,0|

### <a name="delimited-file-example"></a>Пример файла с разделителями

В следующем коде используется [ `write_to_csv()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#write-to-csv-directory-path--datadestination--separator--str--------na--str----na---error--str----error------azureml-dataprep-api-dataflow-dataflow) функцию для записи данных в файл с разделителями.

```python
# Create a new data flow using `write_to_csv` 
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))

# Run the data flow to begin the write operation.
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

Выходные данные примера:

| | Column1 | Column2 | Column3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000,0 | 99999,0 | ОШИБКА | НЕТ | НЕТ | ENRS | NaN    | NaN | NaN |   
|1| 10003,0 | 99999,0 | ОШИБКА | НЕТ | НЕТ | ENSO |    NaN | NaN | NaN |   
|2| 10010,0 | 99999,0 | ОШИБКА | НЕТ | JN | ENJA |    70933,0 | –8667,0 | 90,0 |
|3| 10013,0 | 99999,0 | ОШИБКА | НЕТ | НЕТ |     | NaN | NaN | NaN |
|4.| 10014,0 | 99999,0 | ОШИБКА | НЕТ | НЕТ | ENSO |    59783,0 | 5350,0 |  500,0|

В предыдущих выходных данных в числовых столбцах есть несколько ошибок из-за чисел, которые не были правильно проанализированы. При записи в CSV-файл эти значения NULL заменяются строкой ERROR (Ошибка) по умолчанию.

Можно добавить параметры как часть вызова записи и указать строки, которые используются для представления значений NULL.

```python
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

Предыдущий код производит такие выходные данные:

| | Column1 | Column2 | Column3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000,0 | 99999,0 | BadData | НЕТ | НЕТ | ENRS | NaN  | NaN | NaN |   
|1| 10003,0 | 99999,0 | BadData | НЕТ | НЕТ | ENSO |  NaN | NaN | NaN |   
|2| 10010,0 | 99999,0 | BadData | НЕТ | JN | ENJA |  70933,0 | –8667,0 | 90,0 |
|3| 10013,0 | 99999,0 | BadData | НЕТ | НЕТ |   | NaN | NaN | NaN |
|4.| 10014,0 | 99999,0 | BadData | НЕТ | НЕТ | ENSO |  59783,0 | 5350,0 |  500,0|

### <a name="parquet-file-example"></a>Пример файла PARQUET

Аналогичную `write_to_csv()`, [ `write_to_parquet()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#write-to-parquet-file-path--typing-union--datadestination--nonetype----none--directory-path--typing-union--datadestination--nonetype----none--single-file--bool---false--error--str----error---row-groups--int---0-----azureml-dataprep-api-dataflow-dataflow) функция возвращает поток данных с помощью записи Parquet шаг, который выполняется при выполнения потока данных.

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```

Запустите поток данных, чтобы начать операцию записи.

```python
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(5)
```

Предыдущий код производит такие выходные данные:

|   | Column1 | Column2 | Column3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |-------- |
|0| 10000,0 | 99999,0 | MiscreantData | НЕТ | НЕТ | ENRS | MiscreantData | MiscreantData | MiscreantData |
|1| 10003,0 | 99999,0 | MiscreantData | НЕТ | НЕТ | ENSO | MiscreantData | MiscreantData | MiscreantData |   
|2| 10010,0 | 99999,0 | MiscreantData | НЕТ| JN| ENJA|   70933,0|    –8667,0 |90,0|
|3| 10013,0 | 99999,0 | MiscreantData | НЕТ| НЕТ| |   MiscreantData|    MiscreantData|    MiscreantData|
|4.| 10014,0 | 99999,0 | MiscreantData | НЕТ| НЕТ| ENSO|   59783,0|    5350,0| 500,0|

## <a name="configure-data-for-automated-machine-learning-training"></a>Настройка данных для автоматических машины обучения

Передайте файл данные, записываемые в [ `AutoMLConfig` ](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#automlconfig) объекта в процессе подготовки к автоматической машины обучения. 

В следующем примере кода показано, как преобразование потока данных в таблице данных Pandas, впоследствии, разбив ее на обучающий и проверочный наборы данных для обучения автоматических машины.

```Python
from azureml.train.automl import AutoMLConfig
from sklearn.model_selection import train_test_split

dflow = dprep.auto_read_file(path="")
X_dflow = dflow.keep_columns([feature_1,feature_2, feature_3])
y_dflow = dflow.keep_columns("target")

X_df = X_dflow.to_pandas_dataframe()
y_df = y_dflow.to_pandas_dataframe()

X_train, X_test, y_train, y_test = train_test_split(X_df, y_df, test_size=0.2, random_state=223)

# flatten y_train to 1d array
y_train.values.flatten()

#configure 
automated_ml_config = AutoMLConfig(task = 'regression',
                               X = X_train.values,  
                   y = y_train.values.flatten(),
                   iterations = 30,
                       Primary_metric = "AUC_weighted",
                       n_cross_validation = 5
                       )

```

Если все действия по подготовке интеллектуальному анализу данных, такие как в предыдущем примере не требуется, вы можете передать потока данных непосредственно в `AutoMLConfig`.

```Python
automated_ml_config = AutoMLConfig(task = 'regression', 
                   X = X_dflow,   
                   y = y_dflow, 
                   iterations = 30, 
                   Primary_metric = "AUC_weighted",
                   n_cross_validation = 5
                   )
```

## <a name="next-steps"></a>Дальнейшие действия
* Пакет SDK см. в разделе [Обзор](https://aka.ms/data-prep-sdk) конструктивные шаблоны и примеры использования 
* Автоматические машинного обучения см. в разделе [руководстве](tutorial-auto-train-models.md) пример модели регрессии
