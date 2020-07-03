---
title: Создание, разработка и обслуживание записных книжек Azure синапсе Studio (Предварительная версия)
description: Из этой статьи вы узнаете, как создавать и разрабатывать записные книжки Azure синапсе Studio (Предварительная версия) для подготовки и визуализации данных.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: ruxu
ms.reviewer: ''
ms.openlocfilehash: 506339cefa90fb17bedfc946f70cb4d7d8047cf2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430231"
---
# <a name="create-develop-and-maintain-azure-synapse-studio-preview-notebooks"></a>Создание, разработка и обслуживание записных книжек Azure синапсе Studio (Предварительная версия)

Записная книжка Azure синапсе Studio (Предварительная версия) — это веб-интерфейс для создания файлов, содержащих код в реальном времени, визуализации и текст описания. Записные книжки — это хорошее место для проверки идей и использования быстрых экспериментов для получения ценной информации из данных. Записные книжки также широко используются при подготовке данных, визуализации данных, машинном обучении и других сценариях обработки больших данных.

С помощью записной книжки Azure синапсе Studio вы можете:

* Начните работу с нулевыми усилиями по установке.
* Обеспечьте безопасность данных с помощью встроенных функций безопасности предприятия.
* Анализ данных в необработанных форматах (CSV, txt, JSON и т. д.), обработанных форматах файлов (Parquet, Дельта Lake, ORC и т. д.) и файлов табличных данных SQL в Spark и SQL.
* Повышение производительности благодаря расширенным возможностям разработки и встроенной визуализации данных.

В этой статье описывается, как использовать записные книжки в Azure синапсе Studio.

## <a name="create-a-notebook"></a>Создание записной книжки

Существует два способа создания записной книжки. Вы можете создать новую записную книжку или импортировать существующую записную книжку в рабочую область Azure синапсе из **обозревателя объектов**. Записные книжки Azure синапсе Studio могут распознать стандартные файлы Jupyter Notebook IPYNB.

![синапсе. Создание и импорт-Записная книжка](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Разработка записных книжек

Записные книжки состоят из ячеек, представляющих собой отдельные блоки кода или текста, которые могут быть выполнены независимо или в виде группы.

### <a name="add-a-cell"></a>Добавление ячейки

Существует несколько способов добавить новую ячейку в записную книжку.

1. Разверните кнопку Верхний левый **+ параметр ячейки** и выберите **добавить ячейку кода** или **добавить текстовую ячейку**.

    ![Кнопка "добавить ячейку" с кнопкой "ячейка"](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Наведите указатель мыши на пространство между двумя ячейками и выберите **добавить код** или **Добавить текст**.

    ![добавить ячейку в интервал между пробелами](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Используйте [сочетания клавиш в режиме команд](#shortcut-keys-under-command-mode). Нажмите клавишу **A** , чтобы вставить ячейку над текущей ячейкой. Нажмите клавишу **B** , чтобы вставить ячейку под текущей ячейкой.

### <a name="set-a-primary-language"></a>Установка основного языка

Записные книжки Azure синапсе Studio поддерживают четыре языка Spark:

* pyspark (Python)
* Spark (Scala)
* sparkSQL
* Spark.NET (C#)

Можно задать основной язык для новых добавленных ячеек из раскрывающегося списка на верхней панели команд.

   ![по умолчанию-синапсе-Language](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Использование нескольких языков

Вы можете использовать несколько языков в одной записной книжке, указав нужную команду Magic Language в начале ячейки. В следующей таблице перечислены волшебные команды для переключения языков ячеек.

|Волшебная команда |Язык | Описание |  
|---|------|-----|
|%% pyspark| Python | Выполните запрос **Python** к контексту Spark.  |
|%% Spark| Scala | Выполните запрос **Scala** к контексту Spark.  |  
|%% SQL| SparkSQL | Выполните запрос **SparkSQL** к контексту Spark.  |
|%% CSharp | Spark.NET C # | Выполнение запроса **C# Spark.NET** в контексте Spark. |

На следующем рисунке показан пример того, как можно написать запрос PySpark с помощью команды **%% PySpark** Magic или запроса SparkSQL с командой **%% SQL** Magic в записной книжке **Spark (Scala)** . Обратите внимание, что для параметра основной язык записной книжки задано значение Scala.

   ![синапсе-Spark-Magics](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Использование временных таблиц для ссылки на данные на разных языках

Вы не можете ссылаться на данные или переменные непосредственно на разных языках в записной книжке синапсе Studio. В Spark можно ссылаться на временную таблицу на разных языках. Ниже приведен пример того, как можно прочитать `Scala` кадр данных в `PySpark` и `SparkSQL` использовать временную таблицу Spark в качестве обходного пути.

1. В ячейке 1 прочтите кадр данных из соединителя пула SQL с помощью Scala и создайте временную таблицу.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. В ячейке 2 запросите данные с помощью Spark SQL.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. В ячейке 3 Используйте данные в PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IntelliSense в стиле интегрированной среды разработки

Записные книжки Azure синапсе Studio интегрированы с редактором Монако для перевода IntelliSense в стиле IDE в редактор ячеек. Выделение синтаксиса, ошибка Maker и автоматическое завершение кода позволяют быстрее создавать код и выделять проблемы.

Функции IntelliSense на разных языках имеют разный уровень зрелости. Используйте приведенную ниже таблицу, чтобы узнать о поддерживаемых возможностях.

|Языки| Выделение синтаксиса | Маркер ошибки синтаксиса  | Завершение синтаксиса кода | Завершение кода переменной| Завершение кода системной функции| Завершение кода пользовательской функции| Автоматический отступ | Свертывание кода|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Да|Да|Да|Да|Да|Да|Да|Да|
|Spark (Scala)|Да|Да|Да|Да|-|-|-|Да|
|SparkSQL|Да|Да|-|-|-|-|-|-|
|Spark.NET (C#)|Да|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Форматирование ячейки текста с помощью кнопок на панели инструментов

С помощью кнопок формат на панели инструментов текстовые ячейки можно выполнять стандартные действия Markdown. Он включает текст с полужирным шрифтом, италиЦизинг текст, вставку фрагментов кода, вставку неупорядоченного списка, вставку упорядоченного списка и вставку изображения из URL-адреса.

  ![синапсе-Text-Cell-панель инструментов](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>Отменить операции с ячейками
Нажмите кнопку **отменить** или сочетание клавиш **CTRL + Z** , чтобы отменить последнюю операцию с ячейкой. Теперь можно отменять до последних 20 действий с ячейками в прошлом. 

   ![синапсе-Undo-Cells](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>Переместить ячейку

Нажмите кнопку с многоточием (...), чтобы открыть меню «действия» в правом углу. Затем выберите **переместить ячейку вверх** или **переместить ячейку вниз** , чтобы переместить текущую ячейку. 

[В режиме команд](#shortcut-keys-under-command-mode)можно также использовать сочетания клавиш. Нажмите клавиши **CTRL + ALT + ↑** , чтобы переместить текущую ячейку вверх. Нажмите клавиши **CTRL + ALT + ↓** , чтобы переместить текущую ячейку вниз.

   ![переместить-a-ячейку](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>Удаление ячейки

Чтобы удалить ячейку, нажмите кнопку с многоточием (...) для доступа к меню дополнительных действий в ячейке справа, а затем выберите **Удалить ячейку**. 

[В режиме команд](#shortcut-keys-under-command-mode)можно также использовать сочетания клавиш. Нажмите клавишу **d, d,** чтобы удалить текущую ячейку.
  
   ![Удалить ячейку](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>Сворачивание входных данных ячейки
Нажмите кнопку со стрелкой в нижней части текущей ячейки, чтобы свернуть ее. Чтобы развернуть его, нажмите кнопку со стрелкой, когда ячейка свернута.

   ![Сворачивание-ячейка — входные данные](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>Сворачивание выходных данных ячейки

Нажмите кнопку **Свернуть выходные** данные в левом верхнем углу текущей ячейки, чтобы свернуть ее. Чтобы развернуть его, нажмите кнопку **Показывать выходные данные ячейки** , когда выводится свернутая ячейка.

   ![свернуть-ячейка-вывод](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Выполнение записных книжек

Вы можете выполнять ячейки кода в записной книжке по отдельности или все сразу. Состояние и ход выполнения каждой ячейки представлены в записной книжке.

### <a name="run-a-cell"></a>Выполнение ячейки

Существует несколько способов выполнения кода в ячейке.

1. Наведите указатель мыши на ячейку, которую необходимо запустить, и нажмите кнопку **выполнить в ячейке** или сочетание клавиш **CTRL + ВВОД**.

   ![выполнение в ячейке 1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. Для доступа к меню дополнительных действий с ячейками в правом углу щелкните многоточие (**...**). Затем выберите **выполнить ячейку**.

   ![Запуск-ячейка-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. Используйте [сочетания клавиш в режиме команд](#shortcut-keys-under-command-mode). Нажмите клавиши **SHIFT + ВВОД** , чтобы выполнить текущую ячейку и выбрать ячейку, расположенную ниже. Нажмите клавиши **ALT + ВВОД** , чтобы выполнить текущую ячейку и вставить новую ячейку, расположенную ниже.


### <a name="run-all-cells"></a>Выполнить все ячейки
Нажмите кнопку **запустить все** , чтобы выполнить последовательное выполнение всех ячеек в текущей записной книжке.

   ![выполнение-все ячейки](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>Выполнить все ячейки, расположенные выше или ниже

Для доступа к меню дополнительных действий с ячейками в правом углу щелкните многоточие (**...**). Затем выберите **выполнить ячейки выше** , чтобы выполнить все ячейки, расположенные выше текущей последовательности. Выберите **Run Cells (запустить ячейки** ), чтобы выполнить все ячейки ниже текущей последовательности.

   ![Run-Cells-on-or-снизу](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cell-status-indicator"></a>Индикатор состояния ячейки

Пошаговое состояние выполнения ячейки отображается под ячейкой, чтобы видеть ее текущий ход выполнения. После завершения выполнения ячейки отображаются сводные данные о выполнении с общей длительностью и временем окончания, которые будут храниться для дальнейшего обращения.

![состояние ячейки](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Индикатор хода выполнения Spark

Записная книжка Azure синапсе Studio полностью основана на Spark. Ячейки кода выполняются удаленно в пуле Spark. Индикатор хода выполнения задания Spark с появлением индикатора выполнения в режиме реального времени, помогающего понять состояние выполнения задания.


![Spark-Progress-индикатор](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Конфигурация сеанса Spark

Вы можете указать длительность времени ожидания, число и размер исполнителей, которые будут присвоены текущему сеансу Spark в **настройке сеанса**. Перезапустите сеанс Spark, чтобы изменения конфигурации вступили в силу. Все кэшированные переменные записной книжки очищаются.

![Управление сеансами](./media/apache-spark-development-using-notebooks/synapse-spark-session-mgmt.png)


## <a name="bring-data-to-a-notebook"></a>Перенести данные в записную книжку

Вы можете загрузить данные из хранилища BLOB-объектов Azure, Azure Data Lake Store Gen 2 и пула SQL, как показано в приведенных ниже примерах кода.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Чтение CSV-файла из Azure Data Lake Store Gen2 в качестве кадра данных Spark

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

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Чтение CSV-файла из хранилища BLOB-объектов Azure в качестве кадра данных Spark

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

### <a name="read-data-from-the-primary-storage-account"></a>Чтение данных из основной учетной записи хранения

Вы можете получить доступ к данным в первичной учетной записи хранения напрямую. Нет необходимости предоставлять секретные ключи. В обозреватель данных щелкните правой кнопкой мыши файл и выберите пункт **создать записную книжку** , чтобы просмотреть новую записную книжку с автоматически созданным средство извлечения данных.

![данные в ячейку](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>Визуализация данных в записной книжке

### <a name="display"></a>Отображение ()

Представление табличных результатов предоставляется с возможностью создания линейчатой диаграммы, графика, круговой диаграммы, точечной диаграммы и диаграммы с областями. Вы можете визуализировать данные без написания кода. Диаграммы можно настроить в **параметрах диаграммы**. 

Выходные данные команд для **%% SQL** Magic отображаются по умолчанию в представлении отображаемой таблицы. Вы можете вызвать метод **отображения`<DataFrame name>`()** в таблицах данных Spark или в функции отказоустойчивого распределенного набора (RDD) для создания представления таблицы, подготовленного к просмотру.

   ![Встроенные диаграммы](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)

### <a name="displayhtml"></a>Дисплайхтмл ()

Можно визуализировать HTML или интерактивные библиотеки, например **Боке**, с помощью **дисплайхтмл ()**.

На следующем рисунке показан пример построения глифов на карте с помощью **Боке**.

   ![Боке — пример](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

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

1. Чтобы сохранить изменения, внесенные в одну записную книжку, нажмите кнопку **опубликовать** на панели команд записной книжки.

   ![Публикация — Записная книжка](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Чтобы сохранить все записные книжки в рабочей области, нажмите кнопку **опубликовать все** на панели команд рабочей области. 

   ![опубликовать все](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

В Свойства записной книжки можно указать, следует ли включать выходные данные ячейки при сохранении.

   ![Записная книжка — свойства](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Волшебные команды
Вы можете использовать привычные команды Magic Jupyter в записных книжках Azure синапсе Studio. Просмотрите список ниже в качестве текущих доступных команд Magic. Расскажите нам о вариантах использования на GitHub, чтобы мы могли продолжить создавать более волшебные команды в соответствии с вашими потребностями.

Доступные магическые строки: [% лсмагик](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Доступные волшебия ячеек: [%% времени](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% Capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%% WriteFile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%% SQL](#use-multiple-languages), [%% pyspark](#use-multiple-languages), [%% Spark](#use-multiple-languages), [%% CSharp](#use-multiple-languages)

## <a name="shortcut-keys"></a>Сочетание клавиш

Как и в записных книжках Jupyter, в записных книжках Azure синапсе Studio есть модальный пользовательский интерфейс. Клавиатура выполняет различные действия в зависимости от того, в каком режиме находится ячейка записной книжки. Записные книжки синапсе Studio поддерживают следующие два режима для заданной ячейки кода: режим команд и режим редактирования.

1. Ячейка находится в режиме команд при отсутствии текстового курсора, предлагающего ввести. Если ячейка находится в режиме команд, можно изменить записную книжку как единую, но не тип в отдельные ячейки. Введите режим команд, нажав `ESC` или нажимая кнопку мыши вне области редактора ячейки.

   ![режим командной строки](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. Режим редактирования обозначается текстовым курсором, предлагающим ввести текст в область редактора. Если ячейка находится в режиме редактирования, нельзя ввести в ячейку. Войдите в режим правки, `Enter` нажав или нажимая кнопку мыши, чтобы щелкнуть область редактора ячейки.
   
   ![режим-редактирования](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>Сочетания клавиш в режиме команд

С помощью следующих сочетаний клавиш можно легко перемещаться и выполнять код в записных книжках Azure синапсе.

| Действие |Ярлыки записной книжки синапсе Studio  |
|--|--|
|Запустить текущую ячейку и выбрать ниже | SHIFT+ВВОД |
|Выполнить текущую ячейку и вставить ниже | ALT+ВВОД |
|Выделить ячейку выше| Вверх |
|Выбрать ячейку ниже| Вниз |
|Вставить ячейку выше| Объект |
|Вставить ячейку ниже| B |
|Расширить выбранные ячейки выше| SHIFT+СТРЕЛКА ВВЕРХ |
|Расширить выбранные ячейки ниже| SHIFT+СТРЕЛКА ВНИЗ|
|Переместить ячейку вверх| Ctrl + Alt + ↑ |
|Переместить ячейку вниз| Ctrl + Alt + ↓ |
|Удалить выбранные ячейки| D, D |
|Переключиться в режим редактирования| Введите: |

### <a name="shortcut-keys-under-edit-mode"></a>Сочетания клавиш в режиме редактирования

С помощью следующих сочетаний клавиш можно легко перемещаться и выполнять код в записных книжках Azure синапсе в режиме редактирования.

| Действие |Ярлыки записной книжки синапсе Studio  |
|--|--|
|Переместить курсор вверх | Вверх |
|Переместить курсор вниз|Вниз|
|Отмена|CTRL+Z|
|Повторить|CTRL+Y|
|Добавление и удаление комментариев|Ctrl +/|
|Удалить слово перед|Ctrl + Backspace|
|Удалить слово после|CTRL + DELETE|
|Перейти к началу ячейки|Ctrl + Home|
|Переход к концу ячейки |Ctrl + End|
|Переход на одно слово влево|Ctrl + стрелка влево|
|Переход на одно слово вправо|Ctrl + стрелка вправо|
|Выбрать все|CTRL+A|
|Отступ| Ctrl +]|
|Понижение уровня|Ctrl + [|
|Переключиться в режим команд| ESC |

## <a name="next-steps"></a>Дальнейшие шаги

- [Документация по .NET для Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
