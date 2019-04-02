---
title: Руководство по регрессионной модели. Подготовка данных
titleSuffix: Azure Machine Learning service
description: В первой части этого руководства вы узнаете, как подготовить данные в Python для моделирования регрессии, используя пакет SDK для службы "Машинное обучение Azure".
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sihhu
ms.author: MayMSFT
ms.reviewer: trbye
ms.date: 02/04/2019
ms.custom: seodec18
ms.openlocfilehash: 0ee49299dbbe8095ef98be51dc8619c28891abc5
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361393"
---
# <a name="tutorial-prepare-data-for-regression-modeling"></a>Руководство по Подготовка данных для моделирования регрессии

Из этого руководства вы узнаете, как подготовить данные для моделирования регрессии, используя [пакет SDK службы "Машинное обучение Azure" для подготовки данных с использованием Python](https://aka.ms/data-prep-sdk). Вы выполните различные преобразования для фильтрации и объединения двух разных наборов данных о такси Нью-Йорка.

Это руководство представляет собой **первую часть серии, состоящей из двух частей**. После завершения этого цикла руководств вы сможете прогнозировать стоимость поездки на такси, обучив модель на основе признаков данных. К этим признакам относятся день и время посадки пассажира, количество пассажиров и расположение посадки.

Изучив это руководство, вы:

> [!div class="checklist"]
> * настроите среду Python и импортируете пакеты;
> * загрузите два набора данных с различными именами полей;
> * удалите данные для устранения аномалий;
> * преобразуете данные, используя интеллектуальные преобразования, чтобы создать компоненты;
> * сохраните объект потока данных, чтобы использовать его в модели регрессии.

## <a name="prerequisites"></a>Предварительные требования

Перейдите к разделу [Настройка среды разработки](#start), чтобы ознакомиться с шагами записной книжки, или используйте приведенные ниже инструкции, чтобы получить записную книжку и запустить ее в службе "Записные книжки Azure" или на собственном сервере записных книжек. Чтобы запустить записную книжку, вам потребуется:

* Сервер записных книжек Python 3.6, на котором установлены следующие компоненты:
    * пакет SDK службы "Машинное обучение Azure" для подготовки данных для Python;
* пример записной книжки.

Получите все необходимые компоненты, перейдя к нужному из разделов, щелкнув соответствующую ссылку ниже.

* Использование [Записных книжек Azure](#azure)
* Использование [собственного сервера записных книжек](#server)

### <a name="azure"></a>Использование Записных книжек Azure: к вашим услугам бесплатные записные книжки на основе Jupyter в облаке Azure

Начать работу с Записными книжками Azure очень просто. Пакет SDK службы "Машинное обучение Azure" для подготовки данных с использованием Python уже установлен и настроен в [Записных книжках Azure](https://notebooks.azure.com/). Службы Azure автоматически управляют установкой и последующими обновлениями.

Выполнив указанные ниже действия, запустите записную книжку **tutorials/regression-part1-data-prep.ipynb** в проекте **Начало работы**.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

### <a name="server"></a>Использование собственного сервера записных книжек Jupyter

Чтобы создать локальный сервер Jupyter Notebook на компьютере, выполните следующие действия.  Выполнив указанные действия, запустите записную книжку **tutorials/regression-part1-data-prep.ipynb**.

1. Чтобы создать среду Miniconda для службы "Машинное обучение Azure" с помощью Python, выполните действия по установке, описанные в статье [[Создание рабочей области службы машинного обучения Azure](quickstart-run-local-notebook.md)](setup-create-workspace.md#python).  Вы можете пропустить раздел **Создание рабочей области**, но рабочая область понадобится для [части 2](tutorial-auto-train-models.md) этой серии руководств.
1. Установите пакет SDK для подготовки данных в вашей среде с помощью `pip install azureml-dataprep`.
1. Клонируйте [репозиторий GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Запустите сервер записной книжки из клонированного каталога.

    ```shell
    jupyter notebook
    ```

## <a name="start"></a>Настройка среды разработки

Все настройки для работы по разработке можно сделать в записной книжке Python. Настройка включает следующие действия:

* Установка пакета SDK
* Импорт пакетов Python.

### <a name="install-and-import-packages"></a>Установка и импорт пакетов

Если у вас еще нет необходимых пакетов, установите их, выполнив такую команду:

```shell
pip install azureml-dataprep
```

Импортируйте пакет SDK.

```python
import azureml.dataprep as dprep
```

## <a name="load-data"></a>Загрузка данных

Скачайте два разных набора данных о такси Нью-Йорка в объекты потока данных. Поля в этих наборах данных немного отличаются. Метод `auto_read_file()` автоматически распознает тип входного файла.

```python
from IPython.display import display
dataset_root = "https://dprepdata.blob.core.windows.net/demo"

green_path = "/".join([dataset_root, "green-small/*"])
yellow_path = "/".join([dataset_root, "yellow-small/*"])

green_df_raw = dprep.read_csv(path=green_path, header=dprep.PromoteHeadersMode.GROUPED)
# auto_read_file automatically identifies and parses the file type, which is useful when you don't know the file type.
yellow_df_raw = dprep.auto_read_file(path=yellow_path)

display(green_df_raw.head(5))
display(yellow_df_raw.head(5))
```

Объект `Dataflow` похож на кадр данных и представляет собой ряд неизменяемых операций с данными с отложенным вычислением. Операции могут быть добавлены путем вызова различных доступных методов преобразования и фильтрации. Результатом добавления операции к `Dataflow` всегда будет новый объект `Dataflow`.

## <a name="cleanse-data"></a>Удаление данных

Теперь заполните некоторые переменные преобразованиями ярлыков, которые применяются ко всем потокам данных. Переменная `drop_if_all_null` используется для удаления записей, где все поля имеют значение null. Переменная `useful_columns` содержит массив описаний столбцов, которые сохраняются в каждом потоке данных.

```python
all_columns = dprep.ColumnSelector(term=".*", use_regex=True)
drop_if_all_null = [all_columns, dprep.ColumnRelationship(dprep.ColumnRelationship.ALL)]
useful_columns = [
    "cost", "distance", "dropoff_datetime", "dropoff_latitude", "dropoff_longitude",
    "passengers", "pickup_datetime", "pickup_latitude", "pickup_longitude", "store_forward", "vendor"
]
```

Сначала приступите к работе с данными о такси зеленого цвета, а затем преобразуйте их в допустимую форму, которую можно объединить с данными о такси желтого цвета. Вызовите функции `replace_na()`, `drop_nulls()` и `keep_columns()`, используя созданные переменные преобразования ярлыка. Кроме того, переименуйте все столбцы в кадре данных в соответствии с именами в переменной `useful_columns`.


```python
green_df = (green_df_raw
    .replace_na(columns=all_columns)
    .drop_nulls(*drop_if_all_null)
    .rename_columns(column_pairs={
        "VendorID": "vendor",
        "lpep_pickup_datetime": "pickup_datetime",
        "Lpep_dropoff_datetime": "dropoff_datetime",
        "lpep_dropoff_datetime": "dropoff_datetime",
        "Store_and_fwd_flag": "store_forward",
        "store_and_fwd_flag": "store_forward",
        "Pickup_longitude": "pickup_longitude",
        "Pickup_latitude": "pickup_latitude",
        "Dropoff_longitude": "dropoff_longitude",
        "Dropoff_latitude": "dropoff_latitude",
        "Passenger_count": "passengers",
        "Fare_amount": "cost",
        "Trip_distance": "distance"
     })
    .keep_columns(columns=useful_columns))
green_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>dropoff_datetime</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>distance</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 08:14:37</td>
      <td>2013-08-01 09:09:06</td>
      <td>Нет</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>21.25</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 09:13:00</td>
      <td>2013-08-01 11:38:00</td>
      <td>Нет</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>.00</td>
      <td>74,5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-01 09:48:00</td>
      <td>2013-08-01 09:49:00</td>
      <td>Нет</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-01 10:38:35</td>
      <td>2013-08-01 10:38:51</td>
      <td>Нет</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>3.25</td>
    </tr>
    <tr>
      <th>4.</th>
      <td>2</td>
      <td>2013-08-01 11:51:45</td>
      <td>2013-08-01 12:03:52</td>
      <td>Нет</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>8.5</td>
    </tr>
  </tbody>
</table>
</div>

Выполните те же шаги преобразования для данных о такси желтого цвета. Эти функции гарантируют, что значение NULL будет удалено из набора данных. Это поможет повысить точность модели машинного обучения.

```python
yellow_df = (yellow_df_raw
    .replace_na(columns=all_columns)
    .drop_nulls(*drop_if_all_null)
    .rename_columns(column_pairs={
        "vendor_name": "vendor",
        "VendorID": "vendor",
        "vendor_id": "vendor",
        "Trip_Pickup_DateTime": "pickup_datetime",
        "tpep_pickup_datetime": "pickup_datetime",
        "Trip_Dropoff_DateTime": "dropoff_datetime",
        "tpep_dropoff_datetime": "dropoff_datetime",
        "store_and_forward": "store_forward",
        "store_and_fwd_flag": "store_forward",
        "Start_Lon": "pickup_longitude",
        "Start_Lat": "pickup_latitude",
        "End_Lon": "dropoff_longitude",
        "End_Lat": "dropoff_latitude",
        "Passenger_Count": "passengers",
        "passenger_count": "passengers",
        "Fare_Amt": "cost",
        "fare_amount": "cost",
        "Trip_Distance": "distance",
        "trip_distance": "distance"
    })
    .keep_columns(columns=useful_columns))
yellow_df.head(5)
```

Вызовите функцию `append_rows()` для данных о зеленых такси, чтобы добавить данные о желтых такси. Создается новый объединенный кадр данных.

```python
combined_df = green_df.append_rows([yellow_df])
```

### <a name="convert-types-and-filter"></a>Типы и фильтры преобразования

Изучите сводные статистические данные о координатах посадки и высадки, чтобы увидеть, как распределены данные. Сначала определите объект `TypeConverter`, чтобы указать для поля долготы и широты тип десятичных чисел. Затем вызовите функцию `keep_columns()`, чтобы ограничить вывод только для полей долготы и широты, а затем вызовите функцию `get_profile()`. Эти вызовы функций создают сжатое представление потока данных, чтобы просто показать поля широты и долготы. Это упрощает вычисление координат, отсутствующих или не входящих в область.


```python
decimal_type = dprep.TypeConverter(data_type=dprep.FieldType.DECIMAL)
combined_df = combined_df.set_column_types(type_conversions={
    "pickup_longitude": decimal_type,
    "pickup_latitude": decimal_type,
    "dropoff_longitude": decimal_type,
    "dropoff_latitude": decimal_type
})
combined_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude",
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>type</th>
      <th>Min</th>
      <th>Max</th>
      <th>Count</th>
      <th>Отсутствующее количество</th>
      <th>Присутствующее количество</th>
      <th>Процент отсутствующих</th>
      <th>Количество ошибок</th>
      <th>Количество пустых</th>
      <th>0,1 % квантилей</th>
      <th>1 % квантилей</th>
      <th>5 % квантилей</th>
      <th>25 % квантилей</th>
      <th>50 % квантилей</th>
      <th>75 % квантилей</th>
      <th>95 % квантилей</th>
      <th>99 % квантилей</th>
      <th>99,9 % квантилей</th>
      <th>стандартное отклонение.</th>
      <th>Среднее значение</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115,179337</td>
      <td>0,000000</td>
      <td>7722,0</td>
      <td>0,0</td>
      <td>7722,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>-88,114046</td>
      <td>-73,961840</td>
      <td>-73,961964</td>
      <td>-73,947693</td>
      <td>-73,922097</td>
      <td>-73,846670</td>
      <td>0,000000</td>
      <td>0,000000</td>
      <td>0,000000</td>
      <td>18,792672</td>
      <td>-68,833579</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0,000000</td>
      <td>40,919121</td>
      <td>7722,0</td>
      <td>0,0</td>
      <td>7722,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,000000</td>
      <td>40,682889</td>
      <td>40,675541</td>
      <td>40,721075</td>
      <td>40,756159</td>
      <td>40,803909</td>
      <td>40,849406</td>
      <td>40,870681</td>
      <td>40,891244</td>
      <td>10,345967</td>
      <td>37,936742</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115,179337</td>
      <td>0,000000</td>
      <td>7722,0</td>
      <td>0,0</td>
      <td>7722,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>-87,699611</td>
      <td>-73,984734</td>
      <td>-73,985777</td>
      <td>-73,956250</td>
      <td>-73,928948</td>
      <td>-73,866208</td>
      <td>0,000000</td>
      <td>0,000000</td>
      <td>0,000000</td>
      <td>18,696526</td>
      <td>-68,896978</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0,000000</td>
      <td>41,008934</td>
      <td>7722,0</td>
      <td>0,0</td>
      <td>7722,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,000000</td>
      <td>40,662763</td>
      <td>40,654851</td>
      <td>40,717821</td>
      <td>40,756534</td>
      <td>40,784688</td>
      <td>40,852437</td>
      <td>40,879289</td>
      <td>40,937291</td>
      <td>10,290780</td>
      <td>37,963774</td>
    </tr>
  </tbody>
</table>



В выходных данных сводной статистики вы видите, что есть отсутствующие координаты и те, которые не относятся к Нью-Йорку (это определяется из субъективного анализа). Отфильтруйте координаты для учета расположений, которые находятся за пределами границ города. Объедините в цепочку команды фильтра столбцов в рамках функции `filter()` и определите минимальные и максимальные границы каждого поля. Затем снова вызовите функцию `get_profile()`, чтобы выполнить проверку преобразования.


```python
latlong_filtered_df = (combined_df
    .drop_nulls(
        columns=["pickup_longitude", "pickup_latitude", "dropoff_longitude", "dropoff_latitude"],
        column_relationship=dprep.ColumnRelationship(dprep.ColumnRelationship.ANY)
    )
    .filter(dprep.f_and(
        dprep.col("pickup_longitude") <= -73.72,
        dprep.col("pickup_longitude") >= -74.09,
        dprep.col("pickup_latitude") <= 40.88,
        dprep.col("pickup_latitude") >= 40.53,
        dprep.col("dropoff_longitude") <= -73.72,
        dprep.col("dropoff_longitude") >= -74.09,
        dprep.col("dropoff_latitude") <= 40.88,
        dprep.col("dropoff_latitude") >= 40.53
    )))
latlong_filtered_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude",
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>type</th>
      <th>Min</th>
      <th>Max</th>
      <th>Count</th>
      <th>Отсутствующее количество</th>
      <th>Присутствующее количество</th>
      <th>Процент отсутствующих</th>
      <th>Количество ошибок</th>
      <th>Количество пустых</th>
      <th>0,1 % квантилей</th>
      <th>1 % квантилей</th>
      <th>5 % квантилей</th>
      <th>25 % квантилей</th>
      <th>50 % квантилей</th>
      <th>75 % квантилей</th>
      <th>95 % квантилей</th>
      <th>99 % квантилей</th>
      <th>99,9 % квантилей</th>
      <th>стандартное отклонение.</th>
      <th>Среднее значение</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74,078156</td>
      <td>-73,736481</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>-74,076314</td>
      <td>-73,962542</td>
      <td>-73,962893</td>
      <td>-73,948975</td>
      <td>-73,927856</td>
      <td>-73,866662</td>
      <td>-73,830438</td>
      <td>-73,823160</td>
      <td>-73,769750</td>
      <td>0,048711</td>
      <td>-73,913865</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40,575485</td>
      <td>40,879852</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>40,632884</td>
      <td>40,713105</td>
      <td>40,711600</td>
      <td>40,721403</td>
      <td>40,758142</td>
      <td>40,805145</td>
      <td>40,848855</td>
      <td>40,867567</td>
      <td>40,877690</td>
      <td>0,048348</td>
      <td>40,765226</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74,085747</td>
      <td>-73,720871</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>-74,078828</td>
      <td>-73,985650</td>
      <td>-73,985813</td>
      <td>-73,959041</td>
      <td>-73,936681</td>
      <td>-73,884846</td>
      <td>-73,815507</td>
      <td>-73,776697</td>
      <td>-73,733471</td>
      <td>0,055961</td>
      <td>-73,920718</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40,583530</td>
      <td>40,879734</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>40,597741</td>
      <td>40,695376</td>
      <td>40,695115</td>
      <td>40,727549</td>
      <td>40,758160</td>
      <td>40,788378</td>
      <td>40,850372</td>
      <td>40,867968</td>
      <td>40,878586</td>
      <td>0,050462</td>
      <td>40,759487</td>
    </tr>
  </tbody>
</table>

### <a name="split-and-rename-columns"></a>Разделение и переименование столбцов

Просмотрите профиль данных для столбца `store_forward`. Это поле является логическим флагом, который имеет значение `Y`, если такси не было подключено к серверу после поездки. В результате данные поездки сохраняются в памяти и переадресовываются на сервер уже после установки подключения.


```python
latlong_filtered_df.keep_columns(columns='store_forward').get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>type</th>
      <th>Min</th>
      <th>Max</th>
      <th>Count</th>
      <th>Отсутствующее количество</th>
      <th>Присутствующее количество</th>
      <th>Процент отсутствующих</th>
      <th>Количество ошибок</th>
      <th>Количество пустых</th>
      <th>0,1 % квантилей</th>
      <th>1 % квантилей</th>
      <th>5 % квантилей</th>
      <th>25 % квантилей</th>
      <th>50 % квантилей</th>
      <th>75 % квантилей</th>
      <th>95 % квантилей</th>
      <th>99 % квантилей</th>
      <th>99,9 % квантилей</th>
      <th>стандартное отклонение.</th>
      <th>Среднее значение</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>Нет</td>
      <td>Да</td>
      <td>7059,0</td>
      <td>99,0</td>
      <td>6960,0</td>
      <td>0,014025</td>
      <td>0,0</td>
      <td>0,0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>



Обратите внимание, что в выходных данных профиля в столбце `store_forward` показано, что данные несогласованны и есть отсутствующие значения или значения NULL. Используйте функции `replace()` и `fill_nulls()` для замены этих значений строкой "N":


```python
replaced_stfor_vals_df = latlong_filtered_df.replace(columns="store_forward", find="0", replace_with="N").fill_nulls("store_forward", "N")
```

Выполните функцию `replace` в поле `distance`. Эта функция переформатирует значения расстояния, которые ошибочно помечены как `.00`, и заполнит все значения NULL нулями. Преобразуйте поле `distance` в числовой формат. Эти неверные точки данных, вероятно, являются аномалиями в системе сбора данных в такси.


```python
replaced_distance_vals_df = replaced_stfor_vals_df.replace(columns="distance", find=".00", replace_with=0).fill_nulls("distance", 0)
replaced_distance_vals_df = replaced_distance_vals_df.to_number(["distance"])
```

Разделите значения даты и времени посадки и высадки на соответствующие столбцы даты и времени. Используйте функцию `split_column_by_example()` для разбиения. В этом случае опускается необязательный параметр `example` функции `split_column_by_example()`. Таким образом, функция автоматически определит, где выполнить разделение на основе даты.


```python
time_split_df = (replaced_distance_vals_df
    .split_column_by_example(source_column="pickup_datetime")
    .split_column_by_example(source_column="dropoff_datetime"))
time_split_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>pickup_datetime_1</th>
      <th>pickup_datetime_2</th>
      <th>dropoff_datetime</th>
      <th>dropoff_datetime_1</th>
      <th>dropoff_datetime_2</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>distance</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17:22:00</td>
      <td>2013-08-01</td>
      <td>17:22:00</td>
      <td>2013-08-01 17:22:00</td>
      <td>2013-08-01</td>
      <td>17:22:00</td>
      <td>Нет</td>
      <td>-73,937767</td>
      <td>40,758480</td>
      <td>-73,937767</td>
      <td>40,758480</td>
      <td>1</td>
      <td>0,0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>2013-08-01</td>
      <td>17:24:00</td>
      <td>2013-08-01 17:25:00</td>
      <td>2013-08-01</td>
      <td>17:25:00</td>
      <td>Нет</td>
      <td>-73,937927</td>
      <td>40,757843</td>
      <td>-73,937927</td>
      <td>40,757843</td>
      <td>1</td>
      <td>0,0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06 06:51:19</td>
      <td>2013-08-06</td>
      <td>06:51:19</td>
      <td>2013-08-06 06:51:36</td>
      <td>2013-08-06</td>
      <td>06:51:36</td>
      <td>Нет</td>
      <td>-73,937721</td>
      <td>40,758404</td>
      <td>-73,937721</td>
      <td>40,758369</td>
      <td>1</td>
      <td>0,0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>2013-08-06</td>
      <td>13:26:34</td>
      <td>2013-08-06 13:26:57</td>
      <td>2013-08-06</td>
      <td>13:26:57</td>
      <td>Нет</td>
      <td>-73,937691</td>
      <td>40,758419</td>
      <td>-73,937790</td>
      <td>40,758358</td>
      <td>1</td>
      <td>0,0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4.</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>2013-08-06</td>
      <td>13:27:53</td>
      <td>2013-08-06 13:28:08</td>
      <td>2013-08-06</td>
      <td>13:28:08</td>
      <td>Нет</td>
      <td>-73,937805</td>
      <td>40,758396</td>
      <td>-73,937775</td>
      <td>40,758450</td>
      <td>1</td>
      <td>0,0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>

Переименуйте столбцы, созданные функцией `split_column_by_example()`, и дайте им информативные имена.

```python
renamed_col_df = (time_split_df
    .rename_columns(column_pairs={
        "pickup_datetime_1": "pickup_date",
        "pickup_datetime_2": "pickup_time",
        "dropoff_datetime_1": "dropoff_date",
        "dropoff_datetime_2": "dropoff_time"
    }))
renamed_col_df.head(5)
```

Вызовите функцию `get_profile()`, чтобы просмотреть полную сводную статистику после всех шагов очистки.

```python
renamed_col_df.get_profile()
```

## <a name="transform-data"></a>Преобразование данных

Разделите даты посадки и высадки на значение дня недели, числа месяца и названия месяца. Чтобы получить значение дня недели, используйте функцию `derive_column_by_example()`. В качестве массива параметра эта функция принимает примеры объектов, которые определяют входные данные и требуемые выходные данные. Функция затем автоматически определяет предпочитаемое преобразование. Разделите данные в столбцах времени посадки и высадки на час, минуты и секунды, используя функцию `split_column_by_example()` без примера параметра.

После создания новых признаков используйте функцию `drop_columns()`, чтобы удалить исходные поля, так как новые признаки являются предпочтительными. Переименуйте оставшиеся поля, предоставив им информативные описания.

Преобразование данных таким образом для создания новых основанных на времени признаков повысит точность модели машинного обучения. Например, создание нового признака дня недели поможет установить связь между днем недели и ценой за проезд в такси, которая часто дороже в определенные дни недели из-за высокого спроса.


```python
transformed_features_df = (renamed_col_df
    .derive_column_by_example(
        source_columns="pickup_date",
        new_column_name="pickup_weekday",
        example_data=[("2009-01-04", "Sunday"), ("2013-08-22", "Thursday")]
    )
    .derive_column_by_example(
        source_columns="dropoff_date",
        new_column_name="dropoff_weekday",
        example_data=[("2013-08-22", "Thursday"), ("2013-11-03", "Sunday")]
    )

    .split_column_by_example(source_column="pickup_time")
    .split_column_by_example(source_column="dropoff_time")
    # The following two calls to split_column_by_example reference the column names generated from the previous two calls.
    .split_column_by_example(source_column="pickup_time_1")
    .split_column_by_example(source_column="dropoff_time_1")
    .drop_columns(columns=[
        "pickup_date", "pickup_time", "dropoff_date", "dropoff_time",
        "pickup_date_1", "dropoff_date_1", "pickup_time_1", "dropoff_time_1"
    ])

    .rename_columns(column_pairs={
        "pickup_date_2": "pickup_month",
        "pickup_date_3": "pickup_monthday",
        "pickup_time_1_1": "pickup_hour",
        "pickup_time_1_2": "pickup_minute",
        "pickup_time_2": "pickup_second",
        "dropoff_date_2": "dropoff_month",
        "dropoff_date_3": "dropoff_monthday",
        "dropoff_time_1_1": "dropoff_hour",
        "dropoff_time_1_2": "dropoff_minute",
        "dropoff_time_2": "dropoff_second"
    }))

transformed_features_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>pickup_weekday</th>
      <th>pickup_hour</th>
      <th>pickup_minute</th>
      <th>pickup_second</th>
      <th>dropoff_datetime</th>
      <th>dropoff_weekday</th>
      <th>dropoff_hour</th>
      <th>dropoff_minute</th>
      <th>dropoff_second</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>distance</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17:22:00</td>
      <td>Четверг</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>2013-08-01 17:22:00</td>
      <td>Четверг</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>Нет</td>
      <td>-73,937767</td>
      <td>40,758480</td>
      <td>-73,937767</td>
      <td>40,758480</td>
      <td>1</td>
      <td>0,0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>Четверг</td>
      <td>17</td>
      <td>24</td>
      <td>00</td>
      <td>2013-08-01 17:25:00</td>
      <td>Четверг</td>
      <td>17</td>
      <td>25</td>
      <td>00</td>
      <td>Нет</td>
      <td>-73,937927</td>
      <td>40,757843</td>
      <td>-73,937927</td>
      <td>40,757843</td>
      <td>1</td>
      <td>0,0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06 06:51:19</td>
      <td>Вторник</td>
      <td>06</td>
      <td>51</td>
      <td>19</td>
      <td>2013-08-06 06:51:36</td>
      <td>Вторник</td>
      <td>06</td>
      <td>51</td>
      <td>36</td>
      <td>Нет</td>
      <td>-73,937721</td>
      <td>40,758404</td>
      <td>-73,937721</td>
      <td>40,758369</td>
      <td>1</td>
      <td>0,0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>Вторник</td>
      <td>13</td>
      <td>26</td>
      <td>34</td>
      <td>2013-08-06 13:26:57</td>
      <td>Вторник</td>
      <td>13</td>
      <td>26</td>
      <td>57</td>
      <td>Нет</td>
      <td>-73,937691</td>
      <td>40,758419</td>
      <td>-73,937790</td>
      <td>40,758358</td>
      <td>1</td>
      <td>0,0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4.</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>Вторник</td>
      <td>13</td>
      <td>27</td>
      <td>53</td>
      <td>2013-08-06 13:28:08</td>
      <td>Вторник</td>
      <td>13</td>
      <td>28</td>
      <td>08</td>
      <td>Нет</td>
      <td>-73,937805</td>
      <td>40,758396</td>
      <td>-73,937775</td>
      <td>40,758450</td>
      <td>1</td>
      <td>0,0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>

Обратите внимание, данные показывают, что компоненты даты и времени посадки и высадки, полученные из производных преобразований, являются правильными. Удалите столбцы `pickup_datetime` и `dropoff_datetime`, потому что они больше не требуются (детализированные признаки времени, например часы, минуты и секунды, более полезны для обучения модели).


```python
processed_df = transformed_features_df.drop_columns(columns=["pickup_datetime", "dropoff_datetime"])
```

Чтобы автоматически проверить тип данных каждого поля и отобразить результаты вывода, используйте функцию определения типа.


```python
type_infer = processed_df.builders.set_column_types()
type_infer.learn()
type_infer
```

Результат выполнения `type_infer` выглядит следующим образом.

    Column types conversion candidates:
    'pickup_weekday': [FieldType.STRING],
    'pickup_hour': [FieldType.DECIMAL],
    'pickup_minute': [FieldType.DECIMAL],
    'pickup_second': [FieldType.DECIMAL],
    'dropoff_hour': [FieldType.DECIMAL],
    'dropoff_minute': [FieldType.DECIMAL],
    'dropoff_second': [FieldType.DECIMAL],
    'store_forward': [FieldType.STRING],
    'pickup_longitude': [FieldType.DECIMAL],
    'dropoff_longitude': [FieldType.DECIMAL],
    'passengers': [FieldType.DECIMAL],
    'distance': [FieldType.DECIMAL],
    'vendor': [FieldType.STRING],
    'dropoff_weekday': [FieldType.STRING],
    'pickup_latitude': [FieldType.DECIMAL],
    'dropoff_latitude': [FieldType.DECIMAL],
    'cost': [FieldType.DECIMAL]

Результаты вывода на основе данных представляются правильными. Теперь примените преобразование типов к потоку данных.


```python
type_converted_df = type_infer.to_dataflow()
type_converted_df.get_profile()
```

Перед упаковкой для потока данных примените два последних фильтра к набору данных. Чтобы устранить неправильно записанные точки данных, отфильтруйте поток данных по записям, в которых значения переменных `cost` и `distance` — больше нуля. Этот шаг значительно улучшит точность модели машинного обучения, потому что точки данных с нулевой стоимостью или расстоянием представляют собой значительные выбросы, которые снижают точность прогнозирования.

```python
final_df = type_converted_df.filter(dprep.col("distance") > 0)
final_df = final_df.filter(dprep.col("cost") > 0)
```

Вы полностью преобразовали и подготовили объект потока данных, который можно использовать для модели машинного обучения. Пакет SDK включает функцию сериализации объектов. Ее использование показано в следующем коде.

```python
import os
file_path = os.path.join(os.getcwd(), "dflows.dprep")

package = dprep.Package([final_df])
package.save(file_path)
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы продолжить работу со второй частью руководства, вам потребуется файл **dflows.dprep** в текущем каталоге.

Если вы не планируете переходить ко второй части, удалите файл **dflows.dprep** из текущего каталога. Удалите этот файл, независимо от того, где запущено выполнение: локально или в [Записных книжках Azure](https://notebooks.azure.com/).

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве, которое является первой частью серии, вы:

> [!div class="checklist"]
> * настроили среду разработки;
> * загрузили и удалили наборы данных;
> * использовали интеллектуальные преобразования для прогнозирования логики на основе примера;
> * объединили и упаковали наборы данных для машинного обучения.

Вы готовы использовать данные для обучения во второй части руководства:

> [!div class="nextstepaction"]
> [Руководство. Создание регрессионной модели с помощью автоматического машинного обучения](tutorial-auto-train-models.md)
