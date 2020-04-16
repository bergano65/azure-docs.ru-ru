---
title: Создание, разработка и обслуживание ноутбуков Azure Synapse Studio (предварительный просмотр)
description: В этой статье вы узнаете, как создавать и разрабатывать ноутбуки Azure Synapse Studio (предварительный просмотр) для подготовки и визуализации данных.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: ruxu
ms.reviewer: ''
ms.openlocfilehash: 506339cefa90fb17bedfc946f70cb4d7d8047cf2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430231"
---
# <a name="create-develop-and-maintain-azure-synapse-studio-preview-notebooks"></a>Создание, разработка и обслуживание ноутбуков Azure Synapse Studio (предварительный просмотр)

Ноутбук Azure Synapse Studio (предварительный просмотр) — это веб-интерфейс для создания файлов, содержащих живой код, визуализации и текст повествования. Ноутбуки являются хорошим местом для проверки идей и использования быстрых экспериментов, чтобы получить информацию из ваших данных. Ноутбуки также широко используются в подготовке данных, визуализации данных, машинном обучении и других сценариях больших данных.

С ноутбуком Azure Synapse Studio вы можете:

* Начало работы с нулевым усилием установки.
* Обеспечение безопасности данных с помощью встроенных функций корпоративной безопасности.
* Анализ данных в необработанных форматах (CSV, txt, JSON и т.д.), обработанных форматах файлов (паркет, Дельта-Лейк, ORC и т.д.), а также табулярных файлах s'L в отношении Spark и S'L.
* Будьте продуктивны с расширенными авторскими возможностями и встроенной визуализацией данных.

В этой статье описывается, как использовать ноутбуки в студии Azure Synapse.

## <a name="create-a-notebook"></a>Создание записной книжки

Существует два способа создания ноутбука. Можно создать новый блокнот или импортировать существующий блокнот в рабочее пространство Azure Synapse из **Object Explorer.** Ноутбуки Azure Synapse Studio могут распознавать стандартные файлы Jupyter Notebook IPYNB.

![синапс-создать-импорт-ноутбук](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Разработка записных книжек

Ноутбуки состоят из ячеек, которые представляют собой отдельные блоки кода или текста, которые могут быть проложены независимо или как группа.

### <a name="add-a-cell"></a>Добавить ячейку

Есть несколько способов добавить новую ячейку в блокнот.

1. Расширьте левую верхнюю кнопку **и** выберите **Добавить кодовую ячейку** или **добавить текстовые ячейки.**

    ![добавить-ячейка-с-клеточной кнопкой](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Нависните над пространством между двумя ячейками и выберите **Добавить код** или **Добавить текст.**

    ![добавить-ячейку-между пространством](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Используйте [клавиши Shortcut в режиме команды.](#shortcut-keys-under-command-mode) Нажмите **A,** чтобы вставить ячейку над текущей ячейкой. Нажмите **B,** чтобы вставить ячейку под текущей ячейкой.

### <a name="set-a-primary-language"></a>Установка основного языка

Ноутбуки Azure Synapse Studio поддерживают четыре языка искры:

* pyspark (питон)
* искра (Скала)
* искраС-Зл
* Spark.NET (КЗ)

Основной язык можно установить для новых добавленных ячеек из списка выпадающих в верхней панели команд.

   ![синапс-язык по умолчанию](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Использование нескольких языков

Вы можете использовать несколько языков в одном блокноте, указав правильную команду магии языка в начале ячейки. В следующей таблице перечислены магические команды для переключения языков ячейки.

|Волшебная команда |Язык | Описание |  
|---|------|-----|
|%%писпарк| Python | Выполните запрос **Python** против контекста Spark.  |
|%% искра| Scala | Выполните запрос **Scala** против контекста Spark.  |  
|%%кв.м| СпаркССЗЛ | Выполните запрос **SparkS'L** против контекста Spark.  |
|%%csharp | Spark.NET C # | Выполните **запрос Spark.NET C's** против spark Context. |

Следующее изображение является примером того, как вы можете написать запрос PySpark, используя волшебную команду **%%pyspark** или запрос SparkS'L с волшебной командой **%%sql** в блокноте **Spark (Scala).** Обратите внимание, что основной язык для ноутбука установлен на Scala.

   ![синапс-искра-магия](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Используйте таблицы временных топор для ссылки на данные на разных языках

Вы не можете ссылаться на данные или переменные непосредственно на разных языках в блокноте Synapse Studio. В Spark можно ссылаться на временную таблицу на разных языках. Вот пример того, как `Scala` читать DataFrame `SparkSQL` и использовать таблицу температура Spark в `PySpark` качестве обходного пути.

1. В ячейке 1 прочитайте DataFrame из разъема пула S'L с помощью Scala и создайте временную таблицу.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. В ячейке 2 запрос исмотки данных с помощью Spark S'L.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. В ячейке 3 используйте данные в PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IntelliSense в стиле IDE

Ноутбуки Azure Synapse Studio интегрированы с редактором Monaco, чтобы принести Виолончелисту IntelliSense в редакцию iDE. Выделение Syntax, создатель ошибок и автоматические завершения кода помогут вам быстрее писать код и выявлять проблемы.

Функции IntelliSense находятся на разных уровнях зрелости для разных языков. Используйте таблицу ниже, чтобы увидеть, что поддерживается.

|Языки| Выделение синтаксиса | Маркер ошибки синтаксиса  | Завершение кодов Синтаксиса | Завершение переменного кода| Завершение кода функции системы| Завершение работы функционального кода пользователя| Автоматический отступ | Складные коды|
|--|--|--|--|--|--|--|--|--|
|Пайспарк (Питон)|Да|Да|Да|Да|Да|Да|Да|Да|
|Искра (Скала)|Да|Да|Да|Да|-|-|-|Да|
|СпаркССЗЛ|Да|Да|-|-|-|-|-|-|
|Spark.NET (КЗ)|Да|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Формат текстовой ячейки с кнопками панели инструментов

Кнопки формата в панели инструментов текстовых ячеек можно использовать для обычных действий разметки. Она включает в себя смелый текст, курсивом текста, вставки фрагментов кода, вставку неупорядоченного списка, вставки упорядоченного списка и вставки изображения из URL.

  ![синапс-текст-ячейка-инструмент-панель](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>Отменить операции ячейки
Нажмите кнопку **отменить** или нажмите **Ctrl,** чтобы отменить самую недавнюю операцию ячейки. Теперь вы можете отменить до последних 20 исторических действий ячейки. 

   ![синапс-отменить-клетки](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>Перемещение ячейки

Выберите эллипсы (...) для доступа к дополнительному меню действий ячейки в крайнем правом. Затем выберите **Перемещение ячейки вверх** или **Переместить ячейку вниз,** чтобы переместить текущую ячейку. 

Вы также можете использовать [клавиши ярлыка в режиме команды.](#shortcut-keys-under-command-mode) Нажмите **на Ctrl-Alt,** чтобы переместить вверх текущую ячейку. Нажмите **на Ctrl-Alt,** чтобы переместить текущую ячейку вниз.

   ![двигаться-клетка](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>Удаление ячейки

Чтобы удалить ячейку, выберите эллипсы (...) для доступа к дополнительному меню действий ячейки в крайнем правом, а затем выберите **ячейку Delete.** 

Вы также можете использовать [клавиши ярлыка в режиме команды.](#shortcut-keys-under-command-mode) Нажмите **D,D,** чтобы удалить текущую ячейку.
  
   ![удаление-ячейка](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>Свернуть вход ячейки
Нажмите кнопку стрелки в нижней части текущей ячейки, чтобы свернуть его. Чтобы расширить его, нажмите кнопку стрелки, пока ячейка разрушается.

   ![ввод коллапса-ячейки](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>Свернуть выход ячейки

Нажмите кнопку **вывода коллапса** в левом верхнем углу текущего вывода ячейки, чтобы свернуть ее. Чтобы расширить его, щелкните **выход ячейки Show,** пока выход ячейки разрушается.

   ![выход коллапса-ячейки](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Запуск ноутбуков

Вы можете запустить ячейки кода в блокноте по отдельности или все сразу. Статус и прогресс каждой ячейки представлены в блокноте.

### <a name="run-a-cell"></a>Запуск ячейки

Существует несколько способов запуска кода в ячейке.

1. Наведите курсор на ячейку, которая вы хотите запустить, и выберите кнопку **Run Cell** или нажмите **ctrl-Enter.**

   ![запустить ячейку-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. Чтобы получить доступ к дополнительному меню действий ячейки в крайнем правом, выберите эллипсы **(...**). Затем выберите **ячейку Run.**

   ![бег-ячейка-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. Используйте [клавиши Shortcut в режиме команды.](#shortcut-keys-under-command-mode) Нажмите **Shift-Enter** для запуска текущей ячейки и выберите ячейку ниже. Нажмите **Alt-Enter,** чтобы запустить текущую ячейку и вставьте новую ячейку ниже.


### <a name="run-all-cells"></a>Запуск всех ячеек
Нажмите кнопку **Run All,** чтобы запустить все ячейки в текущем блокноте в последовательности.

   ![запустить все-клетки](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>Запуск всех ячеек выше или ниже

Чтобы получить доступ к дополнительному меню действий ячейки в крайнем правом, выберите эллипсы **(...**). Затем выберите **ячейки Run выше,** чтобы запустить все ячейки выше тока в последовательности. Выберите **ячейки Run ниже,** чтобы запустить все ячейки ниже тока в последовательности.

   ![запустить-клетки выше или ниже](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cell-status-indicator"></a>Индикатор статуса ячейки

Под ячейкой отображается пошаговой статус выполнения ячейки, чтобы увидеть ее текущий прогресс. После завершения выполнения ячейки отображается и хранится там резюме выполнения с общей продолжительностью и временем окончания.

![статус ячейки](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Индикатор прогресса искры

Ноутбук Azure Synapse Studio основан исключительно на Spark. Кодовые ячейки выполняются в пуле Spark удаленно. Индикатор выполнения задач Spark снабжен баром прогресса в реальном времени, который поможет вам понять состояние выполнения задания.


![искра-прогресс-индикатор](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Искра сессии конлигация

Можно указать продолжительность тайм-аута, число и размер исполнителей, которые можно дать текущей сессии Spark в **сеансе настройки.** Перезагрузка сеанса Spark предназначена для вхотворемых изменений конфигурации. Все кэшированные переменные ноутбука очищаются.

![сессия-mgmt](./media/apache-spark-development-using-notebooks/synapse-spark-session-mgmt.png)


## <a name="bring-data-to-a-notebook"></a>Принесите данные в блокнот

Вы можете загрузить данные из Azure Blob Storage, Azure Data Lake Store Gen 2 и пула S'L, как показано в образцах кода ниже.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Прочитайте CSV из Магазина озера Лазурные данные Gen2 как Spark DataFrame

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

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Прочитайте CSV из хранилища Azure Blob как Spark DataFrame

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

### <a name="read-data-from-the-primary-storage-account"></a>Чтение данных из основной учетной записи хранилища

Вы можете получить доступ к данным в основной учетной записи хранения непосредственно. Нет необходимости предоставлять секретные ключи. В Data Explorer, право нажмите на файл и выберите **новый ноутбук,** чтобы увидеть новый ноутбук с экстрактором данных autogenerated.

![данные к ячейке](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>Визуализация данных в блокноте

### <a name="display"></a>Дисплей()

Представление таблоярных результатов предоставляется с возможностью создания диаграммы бара, диаграммы линии, круговой диаграммы, диаграммы рассеяния и диаграммы области. Вы можете визуализировать свои данные без необходимости писать код. Диаграммы могут быть настроены в **параметры диаграммы.** 

Выход магических **команд%%sql** отображается в представлении отображаемой таблицы по умолчанию. Вы можете вызвать **дисплей ()`<DataFrame name>`** на Spark DataFrames или функции устойчивых распределенных наборов данных (RDD) для создания отрисованной таблицы.

   ![встроенные диаграммы](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)

### <a name="displayhtml"></a>DisplayHTML()

Вы можете сделать HTML или интерактивные библиотеки, такие как **bokeh,** с помощью **displayHTML()**.

Следующее изображение является примером построения глифов над картой с помощью **bokeh**.

   ![bokeh-пример](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

Выполнить следующий пример кода, чтобы нарисовать изображение выше.

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

## <a name="save-notebooks"></a>Сохранить ноутбуки

Вы можете сохранить один ноутбук или все ноутбуки в рабочем пространстве.

1. Чтобы сохранить изменения, внесенные в один блокнот, выберите кнопку **«Публикация»** в панели команд ноутбука.

   ![опубликовать-блокнот](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Чтобы сохранить все ноутбуки в рабочем пространстве, выберите **кнопку «Опубликовать все** кнопки» в панели команд рабочего пространства. 

   ![публиковать-все](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

В свойствах ноутбука можно настроить, следует ли включать выход ячейки при сохранении.

   ![ноутбук-свойства](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Волшебные команды
Вы можете использовать знакомые магические команды Jupyter в блокнотах Azure Synapse Studio. Проверьте список ниже, как текущие доступные магические команды. Сообщите нам свои примеры использования на GitHub, чтобы мы могли продолжать создавать больше волшебных команд для удовлетворения ваших потребностей.

Доступные магии линии: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Доступные магии клеток: [%%время](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%захват](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql](#use-multiple-languages), [%%pyspark](#use-multiple-languages), [%%искра](#use-multiple-languages), [%%csharp](#use-multiple-languages)

## <a name="shortcut-keys"></a>Сочетание клавиш

Как и ноутбуки Jupyter Notebooks, ноутбуки Azure Synapse Studio имеют модальный пользовательский интерфейс. Клавиатура делает разные вещи в зависимости от того, в каком режиме находится ячейка ноутбука. Ноутбуки Synapse Studio поддерживают следующие два режима для данной ячейки кода: режим команды и режим отодвилость.

1. Ячейка находится в командном режиме, когда нет текстового курсора, побуждающий вас ввести. Когда ячейка находится в режиме командования, можно отобразить блокнот в целом, но не ввести в отдельные ячейки. Введите командный режим, нажав `ESC` или используя мышь, чтобы нажать за пределами области редактора ячейки.

   ![командный режим](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. Режим редактирования указывается текстовым курсором, побуждающими ввести в области редактора. Когда ячейка находится в режиме edit, вы наклоняю введите в ячейку. Введите режим `Enter` редактировать, нажав или используя мышь, чтобы нажать на область редактора ячейки.
   
   ![режим-редактирования](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>Ключи с коротким срезом в режиме команды

Используя следующие ярлыки нажатия клавиш, вы можете более легко перемещаться и запускать код в блокнотах Azure Synapse.

| Действие |Синапс студии ноутбук ажиотаж  |
|--|--|
|Выполнить текущую ячейку и выбрать ниже | SHIFT+ВВОД |
|Выполнить текущую ячейку и вставить ниже | ALT+ВВОД |
|Выберите ячейку выше| Вверх |
|Выберите ячейку ниже| Вниз |
|Вставьте ячейку выше| Объект |
|Вставьте ячейку ниже| B |
|Расширить выбранные ячейки выше| SHIFT+СТРЕЛКА ВВЕРХ |
|Расширить выбранные ячейки ниже| SHIFT+СТРЕЛКА ВНИЗ|
|Перемещение ячейки вверх| Ктрл-Альт |
|Перемещение ячейки вниз| Ctrl-Alt |
|Удаление выбранных ячеек| D, D |
|Переключиться в режим еде| Введите: |

### <a name="shortcut-keys-under-edit-mode"></a>Ключи ярлыка в режиме edit

Используя следующие ярлыки нажатия клавиш, вы можете более легко перемещаться и запускать код в ноутбуках Azure Synapse, когда в режиме Edit.

| Действие |Синапс студии ноутбук ярлыки  |
|--|--|
|Перемещение курсора вверх | Вверх |
|Перемещение курсора вниз|Вниз|
|Отмена|CTRL+Z|
|Повторить|CTRL+Y|
|Добавление и удаление комментариев|Ctrl q /|
|Удаление слова до|Ctrl и Backspace|
|Удаление слова после|Ctrl и Удалить|
|Перейти к началу ячейки|Ctrl + Home|
|Перейти к концу ячейки |Ctrl + End|
|Перейти одно слово влево|Ctrl - Слева|
|Перейти одним словом право|Ctrl - Справа|
|Выбрать все|CTRL+A|
|Отступ| Ctrl (ctrl)|
|Дендент|Ctrl|
|Переключитесь в командный режим| ESC |

## <a name="next-steps"></a>Дальнейшие действия

- [.NET для документации Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
