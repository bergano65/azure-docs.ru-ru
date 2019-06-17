---
title: Подключение к Apache Hadoop с помощью средств Data Lake для Visual Studio (Azure HDInsight)
description: Узнайте, как установить и использовать средства Data Lake для Visual Studio, чтобы подключиться к кластерам Apache Hadoop в Azure HDInsight, а затем выполнить запросы Hive.
keywords: средства hadoop, запрос hive, visual studio, visual studio hadoop
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 42ef03d604caacf5ba18773b88e892237b5f0eae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66688528"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Подключение к Azure HDInsight и выполнение запросов Apache Hive с помощью средств Data Lake для Visual Studio

Сведения об использовании [Microsoft Azure Data Lake и Stream Analytics Tools для Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) кластеров (называемых также Data Lake Tools) для подключения к Apache Hadoop в [Azure HDInsight](../hdinsight-hadoop-introduction.md) и отправлять запросы Hive.  

Дополнительные сведения об использовании HDInsight см. в статьях [Введение в экосистему Hadoop в Azure HDInsight](../hdinsight-hadoop-introduction.md) и [Руководство по Hadoop. Приступая к работе с Hadoop в HDInsight](apache-hadoop-linux-tutorial-get-started.md).  

Дополнительные сведения о подключении к кластеру Apache Storm см. в статье [Разработка топологий для Apache Storm на C# с помощью средств Data Lake для Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Средства Data Lake для Visual Studio можно использовать для доступа к Azure Data Lake Analytics и HDInsight. Дополнительные сведения см. в статье [Разработка скриптов U-SQL с помощью средств Data Lake для Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Технические условия

Для работы с этим руководством и использования средств Data Lake для Visual Studio потребуются следующие компоненты:

* Кластер Azure HDInsight. Сведения о создании этого кластера см. в статье [Краткое руководство. Использование Apache Hadoop и Apache Hive в Azure HDInsight с шаблоном Resource Manager](apache-hadoop-linux-tutorial-get-started.md). Чтобы выполнять интерактивные запросы Apache Hive, вам потребуется кластер с [интерактивными запросами HDInsight](../interactive-query/apache-interactive-query-get-started.md).  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) (2013 или более поздней версии).  [Visual Studio Community edition](https://visualstudio.microsoft.com/vs/community/) предоставляется бесплатно.  Кроме того, см. в разделе [установки Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) и [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Существует небольшая интерфейс вариантов с помощью Visual Studio 2019.

  > [!IMPORTANT]  
  > Средства Data Lake для Visual Studio 2013 больше не поддерживается.

## <a name="install-data-lake-tools-for-visual-studio"></a>Установка инструментов Data Lake для Visual Studio  
<a name="install-or-update-data-lake-tools-for-visual-studio"></a>

* Visual Studio 2017 или Visual Studio 2019 г.  
  Во время установки, убедитесь, по крайней мере включены рабочие нагрузки **разработки Azure** или **хранение и обработка данных**.  

  Для существующих установок, в строке меню перейдите к **средства** > **получить средства и компоненты...**  открыть установщик Visual Studio.  Выберите по крайней мере рабочих нагрузок **разработки Azure** или **хранение и обработка данных**.

  ![Снимок экрана установщика Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/vs2017_installation.png)

* Visual Studio 2013 и 2015 г.  
  [Скачать Data Lake Tools](https://www.microsoft.com/download/details.aspx?id=49504). Выберите версию средств Data Lake, которая соответствует вашей версии Visual Studio.  

> [!NOTE]  
> Сейчас доступна только английская версия средств Data Lake для Visual Studio.

## <a name="update-data-lake-tools-for-visual-studio"></a>Обновление средств Data Lake для Visual Studio  

1. Откройте Visual Studio.

2. В строке меню перейдите к **средства** > **расширения и обновления...** .

3. Из **расширения и обновления** окне разверните **обновления** слева.

4. Если обновление доступно, **Azure Data Lake и Stream аналитическим средствам** будет отображаться в главном окне.  Нажмите кнопку **Обновить**.

> [!NOTE]  
> Чтобы подключиться к кластерам Interactive Query и выполнять интерактивные запросы Hive, можно использовать средства Data Lake только версии 2.3.0.0 или более поздней.

## <a name="connect-to-azure-subscriptions"></a>Подключение к подпискам Azure
Средства Data Lake для Visual Studio позволяют подключаться к кластерам HDInsight, выполнять некоторые базовые операции управления и запросы Hive.

> [!NOTE]  
> Дополнительные сведения о подключении к универсальному кластеру Hadoop см. в записи блога [How to write and submit Hive queries using Visual Studio](https://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx) (Создание и отправка запросов Hive с помощью Visual Studio).

Чтобы подключиться к подписке Azure, сделайте следующее:

1. Откройте Visual Studio.

2. В строке меню перейдите к **представление** > **обозревателя серверов**.

3. В обозревателе сервера щелкните правой кнопкой мыши **Azure**выберите **подключиться к подписке Microsoft Azure...** и завершения входа в систему.

4. В обозревателе сервера отобразится список существующих кластеров HDInsight. Если кластеров нет в наличии, их можно создать с помощью портала Azure, оболочки Azure PowerShell или пакета SDK для HDInsight. Дополнительные сведения см. в статье [Создание кластеров Hadoop в HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Снимок экрана со списком кластеров для средств Data Lake для Visual Studio в обозревателе сервера](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png "Список кластеров для средств Data Lake для Visual Studio в обозревателе сервера")

5. Разверните кластер HDInsight. **Базы данных Hive**, учетную запись хранения по умолчанию, связанные учетные записи хранения, и **журнал службы Hadoop** отображаются. Развертывание объектов можно продолжить.

После подключения к подписке Azure, можно выполнять следующие задачи.

Чтобы подключиться к порталу Azure из Visual Studio, сделайте следующее:

1. В обозревателе сервера откройте **Azure** > **HDInsight** и выберите свой кластер.

2. Щелкните правой кнопкой мыши кластер HDInsight, а затем выберите **управление кластером на портале Azure [ычная]** .

Чтобы задать вопросы и/или отзывов из Visual Studio:

1. В обозревателе сервера откройте **Azure** > **HDInsight**.

2. Щелкните правой кнопкой мыши **HDInsight** и выберите либо **форум MSDN** возможность задавать вопросы, или **отзыв** Чтобы отправить отзыв.

## <a name="link-a-cluster"></a>Связывание кластера
Его можно связать кластер, щелкните правой кнопкой мыши **HDInsight** выберите **связать кластер HDInsight**. Введите **URL-адрес подключения**, **имя пользователя** и **пароль**, нажмите кнопку **Далее** затем **Готово**, кластера должен быть указан в узел HDInsight успешно.

![Снимок экрана средств Data Lake для Visual Studio диалоговое окно связывания кластера](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

Щелкнуть правой кнопкой мыши связанный кластер, выберите **изменить**, пользователь может обновить сведения о кластере. Добавление в кластер HDInsight поддерживается только Hive сейчас.

![Снимок экрана средств Data Lake для Visual Studio с обновлением кластера ссылку](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Анализ связанных ресурсов
В обозревателе сервера отобразятся учетная запись хранения по умолчанию и все связанные учетные записи хранения. Если развернуть учетную запись хранения по умолчанию, отобразятся контейнеры в учетной записи хранения. Учетная запись хранения по умолчанию и контейнер по умолчанию отмечены. Можно просмотреть содержимое любого контейнера, щелкнув его правой кнопкой мыши.

![Снимок экрана со списком связанных ресурсов для средств Data Lake для Visual Studio в обозревателе сервера](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png "Список связанных ресурсов")

Когда контейнер откроется, с помощью следующих кнопок вы сможете передавать, удалять и скачивать большие двоичные объекты.

![Снимок экрана операций с большими двоичными объектами для средств Data Lake для Visual Studio в обозревателе сервера](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png "Обновление, удаление и загрузка больших двоичных объектов в обозревателе сервера")

## <a name="run-interactive-apache-hive-queries"></a>Выполнение интерактивных запросов Apache Hive
[Apache Hive](https://hive.apache.org) — это инфраструктура хранилища данных, встроенная в Hadoop. Hive используется, чтобы обобщать, запрашивать и анализировать данные. Средства Data Lake для Visual Studio поддерживают выполнение запросов Hive из Visual Studio. Дополнительные сведения о Hive см. в статье [Обзор Apache Hive и HiveQL в Azure HDInsight](hdinsight-use-hive.md).

[Интерактивный запрос](../interactive-query/apache-interactive-query-get-started.md) использует [Hive в LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) в Apache Hive версии 2.1. Он обеспечивает интерактивность для сложных запросов в хранилище данных в больших хранимых наборах данных. Запросы Hive в кластере интерактивных запросов выполняются гораздо быстрее по сравнению с традиционными пакетными заданиями Hive. 

> [!NOTE]  
> Выполнение интерактивных запросов Hive поддерживается только при подключении к кластеру [интерактивных запросов HDInsight](../interactive-query/apache-interactive-query-get-started.md).

Вы также можете использовать средства Data Lake для Visual Studio, чтобы увидеть задание Hive изнутри. Средства Data Lake для Visual Studio собирают и обрабатывают журналы YARN определенных заданий Hive.

В обозревателе сервера откройте **Azure** > **HDInsight** и выберите свой кластер.  Это будет Начальная точка в обозревателе серверов для разделов для выполнения.

### <a name="view-hivesampletable"></a>Представление hivesampletable
У всех кластеров HDInsight по умолчанию образец Hive таблицы с именем `hivesampletable`.  

Из кластера, перейдите к **базы данных Hive** > **по умолчанию** > **hivesampletable**.

* Чтобы просмотреть `hivesampletable` схемы:  
Разверните **hivesampletable**.

* Чтобы просмотреть `hivesampletable` данных:  
Щелкните правой кнопкой мыши **hivesampletable**и выберите **просмотреть первые 100 строк**.  Это эквивалентно выполнению следующего запроса Hive с помощью драйвера Hive ODBC:

   `SELECT * FROM hivesampletable LIMIT 100`

  Подсчет строк можно настроить.

  ![Снимок экрана запроса схемы Hive в HDInsight для Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-hive-schema.png "Результаты запроса Hive")

### <a name="create-hive-tables"></a>Создание таблиц Hive
Таблицу Hive можно создать с помощью графического интерфейса пользователя (GUI) или запросов Hive. Дополнительные сведения об использовании запросов Hive см. в разделе [Выполнение запросов Hive](#run.queries).

1. Из кластера, перейдите к **базы данных Hive** > **по умолчанию**.

2. Щелкните правой кнопкой мыши **по умолчанию**и выберите **Create Table**.

3. Настройка таблицы, при необходимости.  

4. Выберите **Создать таблицу**, чтобы отправить задание для создания таблицы Hive.

    ![Снимок экрана окна создания таблицы в средствах Visual Studio для HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png "Создание таблицы Hive")

### <a name="run.queries"></a>Создание и выполнение запросов Hive
Есть два способа создания и выполнения запросов Hive.

* Создание ad-hoc-запросов
* Создание приложения Hive

Создание и выполнение запросов ad-hoc:

1. Щелкните правой кнопкой мыши кластер, где вы хотите выполнить запрос и выберите **написать запрос Hive**.  

2. Введите следующий запрос Hive:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

    Редактор Hive поддерживает технологию IntelliSense. Средства Data Lake для Visual Studio поддерживают загрузку удаленных метаданных при редактировании скрипта Hive. Например, если ввести `SELECT * FROM`, IntelliSense отобразит все предлагаемые имена таблиц. Если указано имя таблицы, IntelliSense выведет список имен столбцов. Эти инструменты поддерживают почти все инструкции, подзапросы и встроенные определяемые пользователем функции Hive DML.

    ![Снимок экрана примера 1 IntelliSense в средствах HDInsight для Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png "U-SQL IntelliSense")

    ![Снимок экрана примера 2 IntelliSense в средствах HDInsight для Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png "U-SQL IntelliSense")

   > [!NOTE]  
   > IntelliSense предлагает только метаданные кластеров, выбранных на панели инструментов HDInsight.

3. Выберите режим выполнения:

    * **Интерактивный**  

      Убедитесь, **интерактивные** выбран, а затем выберите **Execute**.

      ![Снимок экрана запроса и выполнение](./media/apache-hadoop-visual-studio-tools-get-started/execute.png)  

    * **Пакетная служба**  

      Убедитесь, **пакета** выбран, а затем выберите **отправить**.  Если выбран параметр расширенной отправки, настройте **имя задания**, **аргументы**, **дополнительные конфигурации**, и **каталог состояния**для скрипта.

      ![Снимок экрана запроса и пакетной службы](./media/apache-hadoop-visual-studio-tools-get-started/batch.png)  

      ![Снимок экрана запроса Hive в HDInsight Hadoop](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png "Отправка запросов")

      > [!NOTE]  
      > Не может отправить пакеты к кластерам Interactive Query.  Необходимо использовать интерактивный режим.

Для создания и запуска решения Hive сделайте следующее:

1. В строке меню перейдите к **файл** > **New** > **проекта...** .

2. В области слева, перейдите к **установленные** > **Azure Data Lake** > **HIVE (HDInsight)** .  

3. В средней области выберите **Hive Application** (Приложение Hive). Введите свойства, а затем нажмите кнопку **ОК**.

    ![Снимок экрана нового проекта Hive в средствах HDInsight для Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png "Создание приложений Hive из Visual Studio")

4. В **обозревателе решений** дважды щелкните файл **Script.hql**, чтобы открыть его.

### <a name="view-job-summary-and-output"></a>Сводка по заданию представление и выходные данные

Сводка по заданию изменяется незначительно отличаются **пакета** и **интерактивные** режим.

![Сводка по заданию](./media/apache-hadoop-visual-studio-tools-get-started/jobSummary.png "Сводка по заданию Hive")

Используйте **обновить** кнопку, чтобы обновить состояние, пока состояние задания изменится на **Finished**.  

* Для сведений о задании из **пакета** режиме, перейдите по ссылкам внизу, чтобы см. в разделе **запрос задания**, **выходные данные задания**, **журнал задания**, или **Журнал yarn**.

* Для сведений о задании из **интерактивные** режим, см. вкладки **вывода** и **HiveServer2 Output**.

  ![сведения о задании](./media/apache-hadoop-visual-studio-tools-get-started/tabs.png "Hive сведения о задании")

### <a name="view-job-graph"></a>Представление графа задания

В настоящее время графики заданий отображаются только для заданий Hive, использующих Tez в качестве подсистемы выполнения.  Сведения о том, как включить Tez, см. в статье [Обзор Apache Hive и HiveQL в Azure HDInsight](hdinsight-use-hive.md).  Кроме того, см. в разделе [использование Apache Tez вместо Map Reduce](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Чтобы увидеть все операторы вершины, дважды щелкните вершину графа задания. Можно выбрать также определенный оператор, чтобы увидеть о нем дополнительные сведения.

Граф задания может отображаться, даже если Tez указан в качестве подсистемы выполнения, если приложение запускается.  Это может произойти, так как задание не содержит инструкций DML, могут возвращать данные без запуска приложения Tez. Например `SELECT * FROM table1` будет не запускает приложение Tez.

![Граф задания](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png "Сводка по заданию Hive")


### <a name="task-execution-detail"></a>Сведения о выполнении задачи

Граф задания, можно выбрать **сведения о выполнении задачи** получить подробные и структурированные сведения о заданиях Hive. Но можно также получить и дополнительные сведения. При появлении проблем с производительностью представление можно использовать для получения дополнительных сведений о проблеме. Например, вы можете получить сведения о работе всех задач и подробную информацию о каждой задаче (чтение и запись данных, время запланированного запуска и завершения и т. д.). Эти наглядные сведения помогут вам изменить конфигурацию задания или архитектуру системы.

![Снимок экрана средств Data Lake для Visual Studio: представление выполнения заданий](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png "Представление выполнения заданий")


### <a name="view-hive-jobs"></a>Просмотр заданий Hive
Для заданий Hive можно просмотреть запросы задания, выходные данные задания, журнал задания и журналы YARN.

Самая последняя версия средств позволяет увидеть, что находится внутри заданий Hive, собирая и отображая журналы YARN. Журнал YARN может помочь исследовать проблемы производительности. Дополнительные сведения о том, как HDInsight собирает журналы YARN, см. в статье [Доступ к журналам приложений YARN в HDInsight под управлением Windows](../hdinsight-hadoop-access-yarn-app-logs.md).

Для просмотра заданий Hive сделайте следующее:

1. Щелкните правой кнопкой мыши кластер HDInsight, а затем выберите **Просмотр заданий**. Появится список заданий Hive, выполняющихся в кластере.  

2. Выберите задание. В окне **сводки заданий Hive** выберите один из пунктов ниже.
    - **Запрос задания**
    - **Выходные данные задания**
    - **Журнал задания**  
    - **Журнал YARN**

    ![Снимок экрана окна просмотра заданий Hive в средствах HDInsight для Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png "Просмотр заданий Hive")


## <a name="run-apache-pig-scripts"></a>Запуск скриптов Apache Pig

1. В строке меню перейдите к **файл** > **New** > **проекта...** .

2. В области слева, перейдите к **установленные** > **Azure Data Lake** > **Pig (HDInsight)** .  

3. В средней области выберите **приложение Pig**. Введите свойства, а затем нажмите кнопку **ОК**.

4. В **обозревателе решений**, дважды щелкните **Script.pig** чтобы открыть скрипт.

## <a name="feedback-and-known-issues"></a>Отзывы и известные проблемы
* Проблема, при которой результаты, начинающиеся со значений Null, не отображались, исправлена. Если эта проблема мешает вам работать, обратитесь в нашу службу поддержки.
* Скрипт HQL, созданный Visual Studio, кодируется в зависимости от параметров региона пользователя. Скрипт выполняется неправильно, если вы загружаете его в кластер как двоичный файл.

## <a name="next-steps"></a>Дальнейшие действия
Из этой статьи вы узнали, как подключаться к кластерам HDInsight из Visual Studio, используя пакет средств Data Lake для Visual Studio, и выполнять запросы Hive. Дополнительные сведения вы найдете в следующих статьях:

* [Выполнения запросов Apache Hive, с помощью средств Data Lake для Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Использование Hive и HiveQL с Hadoop в HDInsight](hdinsight-use-hive.md)
* [Краткое руководство. Использование Apache Hadoop и Apache Hive в Azure HDInsight с шаблоном Resource Manager](apache-hadoop-linux-tutorial-get-started.md)
* [Отправка заданий Hadoop в HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Анализ данных Twitter с помощью Apache Hive в HDInsight в Azure](../hdinsight-analyze-twitter-data.md)

