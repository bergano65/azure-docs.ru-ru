---
title: Создание, разработка и обслуживание записных книжек Azure Synapse Studio (предварительная версия)
description: Из этой статьи вы узнаете, как создавать и разрабатывать записные книжки Azure Synapse Studio (предварительная версия) для подготовки данных и визуализации.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2020
ms.author: ruxu
ms.reviewer: ''
ms.openlocfilehash: 21e3ba8cbf60cbbdc6480719016fc48db4fe390c
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83702093"
---
# <a name="create-develop-and-maintain-azure-synapse-studio-preview-notebooks"></a>Создание, разработка и обслуживание записных книжек Azure Synapse Studio (предварительная версия)

Записная книжка Azure Synapse Studio (предварительная версия) — это веб-интерфейс, позволяющий создавать файлы, содержащие реальный код, визуализации и текст описания. Записные книжки отлично подходят для проверки идей и использования быстрых экспериментов, чтобы получить аналитические сведения по данным. Они также широко используются при подготовке и визуализации данных, машинном обучении и других сценариях с большими данными.

С помощью записной книжки Azure Synapse Studio можно выполнять следующие задачи.

* Начните работу, потратив минимум усилий на настройку.
* Обеспечьте безопасность данных благодаря встроенным функциям безопасности предприятия.
* Анализируйте данные в необработанных форматах (CSV, txt, JSON и т. д.), обработанных форматах файлов (parquet, Delta Lake, ORC и т. д.) и файлах табличных данных SQL с использованием Spark и SQL.
* Повысьте продуктивность труда с расширенными возможностями разработки и встроенной визуализацией данных.

В этой статье описывается использование записных книжек в Azure Synapse Studio.

## <a name="create-a-notebook"></a>Создание записной книжки

Существует два способа создания записной книжки. Вы можете создать записную книжку или импортировать имеющуюся записную книжку в рабочую область Azure Synapse из **обозревателя объектов**. Записные книжки Azure Synapse Studio могут распознавать стандартные IPYNB-файлы Jupyter Notebook.

![synapse-create-import-notebook](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Разработка записных книжек

Записные книжки состоят из ячеек, являющихся отдельными блоками кода или текста, которые можно запускать независимо или в группе.

### <a name="add-a-cell"></a>Добавление ячейки

Существует несколько способов добавить в записную книжку новую ячейку.

1. Разверните верхнюю левую кнопку **+ Cell** (Добавить ячейку) и выберите **Добавить ячейку кода** или **Добавить текстовую ячейку**.

    ![add-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Наведите указатель мыши между двумя ячейками и выберите **Добавить код** или **Добавьте текст**.

    ![add-cell-between-space](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Используйте [сочетания клавиш в режиме команд](#shortcut-keys-under-command-mode). Нажмите клавишу **A**, чтобы вставить ячейку над текущей ячейкой. Нажмите клавишу **B**, чтобы вставить ячейку под текущей ячейкой.

### <a name="set-a-primary-language"></a>Установка основного языка

Записные книжки Azure Synapse Studio поддерживают четыре языка Apache Spark:

* PySpark (Python)
* Spark (Scala)
* SparkSQL
* .NET для Apache Spark (C#)

Установить основной язык для новых добавленных ячеек можно из раскрывающегося списка на верхней панели команд.

   ![default-synapse-language](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Использование нескольких языков

В одной записной книжке можно использовать несколько языков, указав магическую языковую команду в начале ячейки. В следующей таблице перечислены магические команды, которые используются для переключения языков ячеек.

|Магические команды |Язык | Описание |  
|---|------|-----|
|%%pyspark| Python | Выполните запрос **Python** к контексту Spark.  |
|%%spark| Scala | Выполните запрос **Scala** к контексту Spark.  |  
|%%sql| SparkSQL | Выполните запрос **SparkSQL** к контексту Spark.  |
|%%csharp | .NET для Spark C# | Выполните запрос **.NET для Spark C#** к контексту Spark. |

На следующем рисунке показан пример того, как можно написать запрос PySpark с помощью магической команды **%%pyspark** или запроса SparkSQL с магической командой **%%sql** в записной книжке **Spark(Scala)** . Обратите внимание, что основным языком для записной книжки является pySpark.

   ![synapse-spark-magics](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Использование временных таблиц для ссылки на данные на разных языках

В записной книжке Synapse Studio вы не можете ссылаться на данные или переменные напрямую на разных языках. В Spark на временную таблицу можно ссылаться на разных языках. Ниже приведен пример того, как прочитать `Scala` DataFrame в `PySpark` и `SparkSQL`, используя в качестве обходного решения временную таблицу Spark.

1. В ячейке 1 выполните чтение DataFrame из соединителя пула SQL с помощью Scala и создайте временную таблицу.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. В ячейке 2 выполните запрос на данные с помощью SQL Spark.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. В ячейке 3 используйте данные в PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>Функция IntelliSense в IDE

Записные книжки Azure Synapse Studio интегрированы с редактором Monaco, что позволяет работать с функцией IntelliSense в IDE в редакторе ячеек. Выделение синтаксиса, маркер ошибок и автоматическое завершение кода помогут быстрее писать код и выявлять проблемы.

Возможности IntelliSense находятся на разных уровнях зрелости для разных языков. В приведенной ниже таблице указаны сведения о поддерживаемых функциях.

|Языки| Выделение синтаксиса | Маркер синтаксических ошибок  | Завершение кода синтаксиса | Завершение кода переменной| Завершение кода системной функции| Завершение кода пользовательской функции| Автоматический отступ | Свертывание кода|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Да|Да|Да|Да|Да|Да|Да|Да|
|Spark (Scala)|Да|Да|Да|Да|-|-|-|Да|
|SparkSQL|Да|Да|-|-|-|-|-|-|
|.NET для Spark C#|Да|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Форматирование текстовой ячейки с помощью кнопок панели инструментов

Для выполнения общих действий markdown можно использовать кнопки форматирования на панели инструментов текстовых ячеек. Они включают выделение текста полужирным шрифтом, выделение курсивом, вставку фрагментов кода, вставку неупорядоченного списка, вставку упорядоченного списка, а также вставку изображения из URL.

  ![synapse-text-cell-toolbar](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>Отмена операций с ячейками
Нажмите кнопку **Отменить** или **Ctrl+Z**, чтобы отменить последнюю операцию с ячейкой. Теперь вы можете отменить до последних 20 исторических действий с ячейками. 

   ![synapse-undo-cells](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>Перемещение ячейки

Нажмите кнопку с многоточием (…), чтобы открыть дополнительное меню действий с ячейкой в правом углу. Затем выберите **Move cell up** (Переместить ячейку верх) или **Move cell down** (Переместить ячейку вниз), чтобы переместить текущую ячейку. 

Вы также можете использовать [сочетания клавиш в режиме команд](#shortcut-keys-under-command-mode). Нажмите клавиши **Ctrl+Alt+↑**, чтобы переместить текущую ячейку вверх. Нажмите клавиши **Ctrl+Alt+↓**, чтобы переместить текущую ячейку вниз.

   ![move-a-cell](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>Удаление ячейки

Нажмите кнопку с многоточием (…), чтобы открыть дополнительное меню действий с ячейкой справа, а затем выберите **Удалить ячейку**. 

Вы также можете использовать [сочетания клавиш в режиме команд](#shortcut-keys-under-command-mode). Нажмите **D,D**, чтобы удалить текущую ячейку.
  
   ![delete-a-cell](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>Сворачивание входных данных ячейки
Нажмите кнопку со стрелкой внизу текущей ячейки, чтобы свернуть ее. Чтобы развернуть ее, нажмите кнопку со стрелкой, когда ячейка свернута.

   ![collapse-cell-input](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>Сворачивание выходных данных ячейки

Нажмите кнопку **collapse output** (Свернуть выходные данные) в верхнем левом углу выходных данных текущей ячейки, чтобы свернуть их. Чтобы развернуть их, щелкните **Show cell output** (Показать выходные данные ячейки), когда выходные данные ячейки свернуты.

   ![collapse-cell-output](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Запуск записных книжек

Вы можете выполнять ячейки кода в записной книжке по отдельности или все сразу. Состояние и ход выполнения каждой ячейки представлены в записной книжке.

### <a name="run-a-cell"></a>Выполнение ячейки

Есть несколько способов запустить код в ячейке.

1. Наведите указатель мыши на необходимую ячейку и нажмите кнопку **Выполнить ячейку** или нажмите клавиши **CTRL+ВВОД**.

   ![run-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. Чтобы открыть дополнительное меню действий с ячейками справа, нажмите кнопку с многоточием ( **…** ). Затем выберите **Выполнить ячейку**.

   ![run-cell-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. Используйте [сочетания клавиш в режиме команд](#shortcut-keys-under-command-mode). Нажмите клавиши **SHIFT+ВВОД**, чтобы выполнить текущую ячейку и выбрать ячейку ниже. Нажмите клавиши **ALT+ВВОД**, чтобы выполнить текущую ячейку и вставить новую ячейку ниже.


### <a name="run-all-cells"></a>Выполнение всех ячеек
Нажмите кнопку **Выполнить все**, чтобы последовательно выполнить все ячейки в текущей записной книжке.

   ![run-all-cells](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>Выполнение всех ячеек выше или ниже

Чтобы открыть дополнительное меню действий с ячейками справа, нажмите кнопку с многоточием ( **…** ). Затем выберите **Run cells above** (Выполнить ячейки выше), чтобы последовательно запустить все ячейки выше текущей. Выберите **Run cells below** (Выполнить ячейки ниже), чтобы последовательно запустить все ячейки ниже текущей.

   ![run-cells-above-or-below](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cell-status-indicator"></a>Индикатор состояния ячейки

Пошаговое состояние выполнения ячейки отображается под ячейкой, чтобы можно было просматривать ее текущий ход выполнения. После завершения выполнения ячейки отображается сводная информация об итогах выполнения с общей длительностью и временем окончания и сохраняется там для дальнейшего использования.

![cell-status](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Индикатор хода выполнения Spark

Записная книжка Azure Synapse Studio основана исключительно на Spark. Ячейки кода выполняются в пуле Spark удаленно. Индикатор хода выполнения задания Spark снабжен индикатором хода выполнения в реальном времени, который поможет вам понять состояние хода выполнения задания.


![spark-progress-indicator](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Конфигурация сеанса Spark

Вы можете указать время ожидания, число и размер исполнителей, которые будут переданы текущему сеансу Spark в области **Настройка сеанса**. Перезапустите сеанс Spark, чтобы изменения конфигурации вступили в силу. Все кэшированные переменные записной книжки очищаются.

![session-mgmt](./media/apache-spark-development-using-notebooks/synapse-spark-session-mgmt.png)


## <a name="bring-data-to-a-notebook"></a>Перенесение данных в записную книжку

Вы можете загрузить данные из хранилища BLOB-объектов Azure, Azure Data Lake Store Gen 2 и пула SQL, как показано в приведенных ниже примерах кода.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Считывание CSV из Azure Data Lake Store Gen2 в качестве Spark DataFrame

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (blob_container_name, blob_account_name,  blob_relative_path)

spark.conf.set("fs.azure.account.auth.type.%s.dfs.core.windows.net" %account_name, "SharedKey")
spark.conf.set("fs.azure.account.key.%s.dfs.core.windows.net" %account_name ,"Your ADLSg2 Primary Key")

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Считывание CSV из хранилища BLOB-объектов Azure в качестве Spark DataFrame

```python

from pyspark.sql import SparkSession
from pyspark.sql.types import *

blob_account_name = "Your blob account name"
blob_container_name = "Your blob container name"
blob_relative_path = "Your blob relative path"
blob_sas_token = "Your blob sas token"

wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)

```

### <a name="read-data-from-the-primary-storage-account"></a>Считывание данных из основной учетной записи хранения

Доступ к данным можно получить в основной учетной записи хранения напрямую. Нет необходимости предоставлять секретные ключи. В Data Explorer щелкните файл правой кнопкой мыши и выберите **Создать записную книжку**, чтобы просмотреть новую записную книжку с автоматически созданным средством извлечения данных.

![data-to-cell](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>Визуализация данных в записной книжке

### <a name="display"></a>Display()

Представление табличных результатов предоставляется с возможностью создания линейчатой диаграммы, графика, круговой диаграммы, точечной диаграммы и диаграммы с областями. Визуализировать данные можно без написания кода. Диаграммы можно настроить в области **Параметры диаграммы**. 

Выходные данные магических команд **%%sql** отображаются в представлении отображаемой таблицы по умолчанию. Вы можете вызвать **display(`<DataFrame name>`)** для Spark DataFrames или функцию устойчивых распределенных наборов данных (RDD), чтобы создать представление отображаемой таблицы.

   ![builtin-charts](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)

### <a name="displayhtml"></a>DisplayHTML()

Вы можете отображать HTML или интерактивные библиотеки, такие как **bokeh**, используя **displayHTML()** .

Ниже приведен пример построения глифов на карте с использованием **bokeh**.

   ![bokeh-example](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

Выполните следующий пример кода, чтобы нарисовать изображение выше.

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)

```

## <a name="save-notebooks"></a>Сохранение записных книжек

Вы можете сохранить одну или все записные книжки в рабочей области.

1. Чтобы сохранить изменения, сделанные в одной записной книжке, нажмите кнопку **Опубликовать** на панели команд записной книжки.

   ![publish-notebook](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Чтобы сохранить все записные книжки в рабочей области, нажмите кнопку **Опубликовать все** на панели команд рабочей области. 

   ![publish-all](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

В свойствах записной книжки вы можете указать, следует ли при сохранении включать выходные данные ячейки.

   ![notebook-properties](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Магические команды
В записных книжках Azure Synapse Studio можно использовать привычные магические команды Jupyter. Текущие доступные магические команды указаны в приведенном ниже списке. Поделитесь с нами своими вариантами использования на GitHub, чтобы мы могли продолжать создавать еще больше магических команд с учетом ваших потребностей.

Доступные магические команды строки: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit).

Доступные магические команды ячейки: [%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql](#use-multiple-languages), [%%pyspark](#use-multiple-languages), [%%spark](#use-multiple-languages), [%%csharp](#use-multiple-languages).

## <a name="shortcut-keys"></a>Сочетания клавиш

Аналогично Jupyter Notebook в записных книжках Azure Synapse Studio есть модальный пользовательский интерфейс. Клавиатура выполняет разные действия в зависимости от того, в каком режиме находится ячейка записной книжки. Записные книжки Synapse Studio поддерживают для данной ячейки кода следующие два режима: режим команд и режим правки.

1. Ячейка находится в режиме команд, когда нет текстового курсора для ввода текста. Когда ячейка находится в режиме команд, вы можете редактировать записную книжку целиком, но не вводить ее в отдельные ячейки. Войдите в режим команд, нажав `ESC` или щелкнув за пределами области редактора ячейки с помощью мыши.

   ![command-mode](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. Режим правки обозначается текстовым курсором для ввода текста в области редактора. Когда ячейка находится в режиме правки, вы не можете ввести текст в нее. Войдите в режим правки, нажав `Enter` или щелкнув область редактора ячейки с помощью мыши.
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>Сочетания клавиш в режиме команд

С помощью приведенных ниже сочетаний клавиш вы можете легче перемещаться и выполнять код в записных книжках Azure Synapse.

| Действие |Сочетания клавиш для записной книжки Synapse Studio  |
|--|--|
|Запуск текущей ячейки и выбор ниже | SHIFT+ВВОД |
|Запуск текущей ячейки и вставка ниже | ALT+ВВОД |
|Выбор ячейки выше| Вверх |
|Выбор ячейки ниже| Вниз |
|Вставка ячейки выше| Объект |
|Вставка ячейки ниже| B |
|Расширение выбранных ячеек выше| Shift+Up |
|Расширение выбранных ячеек ниже| Shift+ВНИЗ|
|Перемещение ячейки вверх| CTRL+ALT+↑ |
|Перемещение ячейки вниз| CTRL+ALT+↓ |
|Удаление выбранных ячеек| D, D |
|Переключение в режим правки| Введите: |

### <a name="shortcut-keys-under-edit-mode"></a>Сочетания клавиш в режиме правки

С помощью приведенных ниже сочетаний клавиш вы можете легче перемещаться и выполнять код в записных книжках Azure Synapse в режиме правки.

| Действие |Сочетания клавиш для записной книжки Synapse Studio  |
|--|--|
|Перемещение курсора вверх | Вверх |
|Перемещение курсора вниз|Вниз|
|Отмена|Ctrl+Z|
|Повторить|CTRL+Y|
|Добавление и удаление комментариев|Ctrl+/|
|Удаление слова перед|Ctrl+Backspace|
|Удаление слова после|Ctrl+Delete|
|Переход к началу ячейки|Ctrl+Home|
|Передох к концу ячейки |Ctrl+End|
|Переход на одно слово влево|Ctrl+Left|
|Переход на одно слово вправо|Ctrl+Right|
|Выбрать все|Ctrl+A|
|Отступ| Ctrl+]|
|Понижение уровня|Ctrl+[|
|Переключение в режим команд| ESC |

## <a name="next-steps"></a>Дальнейшие действия

- [Краткое руководство. Создание пула Apache Spark (предварительная версия) в Azure Synapse Analytics с помощью веб-инструментов](../quickstart-apache-spark-notebook.md)
- [Что такое Apache Spark в Azure Synapse Analytics](apache-spark-overview.md)
- [Использование .NET для Apache Spark с помощью Azure Synapse Analytics](spark-dotnet.md)
- [Документация по .NET для Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
