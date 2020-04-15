---
title: Apache Hadoop & Visual Studio Data Lake Tools - Azure HDInsight
description: Узнайте, как установить и использовать Data Lake Tools для Visual Studio. Используйте инструмент для подключения к кластерам Apache Hadoop в Azure HDInsight, а затем запускать запросы Hive.
keywords: средства hadoop, запрос hive, visual studio, visual studio hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 7504826f267d717f30c5e88621578412c744e5f9
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383510"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Подключение к Azure HDInsight и выполнение запросов Apache Hive с помощью средств Data Lake для Visual Studio

Узнайте, как использовать Microsoft Azure Data Lake и инструменты аналитики потоков для визуальной студии (Data Lake Tools). Используйте инструмент для подключения к [кластерам Apache Hadoop в Azure HDInsight](apache-hadoop-introduction.md) и отправки запросов Hive.  

Для получения дополнительной информации об использовании HDInsight, смотрите [Начало с HDInsight](apache-hadoop-linux-tutorial-get-started.md).  

Для получения дополнительной информации о подключении к Apache Storm [см.](../storm/apache-storm-develop-csharp-visual-studio-topology.md)

Средства Data Lake для Visual Studio можно использовать для доступа к Azure Data Lake Analytics и HDInsight. Дополнительные сведения см. в статье [Разработка скриптов U-SQL с помощью средств Data Lake для Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Предварительные требования

Для завершения этой статьи и использования Data Lake Tools для Visual Studio необходимы следующие элементы:

* Кластер Azure HDInsight. Сведения о создании этого кластера см. в статье [Краткое руководство. Использование Apache Hadoop и Apache Hive в Azure HDInsight с шаблоном Resource Manager](apache-hadoop-linux-tutorial-get-started.md). Чтобы выполнять интерактивные запросы Apache Hive, вам потребуется кластер с [интерактивными запросами HDInsight](../interactive-query/apache-interactive-query-get-started.md).  

* [Визуальная студия](https://visualstudio.microsoft.com/downloads/). Визуальное [издание сообщества Studio](https://visualstudio.microsoft.com/vs/community/) является бесплатным. Инструкции, приведенные здесь для [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

## <a name="install-data-lake-tools-for-visual-studio"></a>Установка инструментов Data Lake для Visual Studio  

Следуйте соответствующим инструкциям по установке Data Lake Tools для вашей версии Visual Studio:

* Для Visual Studio 2017 или Visual Studio 2019:

    Во время установки Visual Studio убедитесь, что вы включаете рабочую нагрузку **разработки Azure** или рабочую нагрузку **на хранение и обработку данных.**  

    Для существующих установок Visual Studio перейдите в панель меню IDE и выберите **Инструменты** > **Получить инструменты и функции,** чтобы открыть Visual Studio Installer. Во **вкладке "Рабочие нагрузки"** выберите по крайней мере рабочую нагрузку **разработки Azure** (под **Web & Cloud).** Или выберите рабочую нагрузку **для хранения и обработки данных** (под **другими наборами инструментов).**

  ![Выбор рабочей нагрузки, Визуальный установщик студии](./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png)

* Для визуальной студии 2015:

    [Скачать Data Lake Tools](https://www.microsoft.com/download/details.aspx?id=49504). Выберите версию средств Data Lake, которая соответствует вашей версии Visual Studio.

## <a name="update-data-lake-tools-for-visual-studio"></a>Обновление данных озера Инструменты для визуальной студии  

Далее убедитесь, что вы обновляете Data Lake Tools до самой последней версии.

1. Запустите Visual Studio.

2. В окне **старта** выберите **Продолжить без кода**.

3. В меню Visual Studio IDE выберите **расширения.** > **Manage Extensions**

4. В диалоговом окне **расширения управления** расширьте узло **обновления.**

5. Если список доступных обновлений включает **Azure Data Lake и Stream Analytic Tools,** выберите его. Затем выберите кнопку **обновления.** После того, как диалоговый ящик **Download and Install** появляется и исчезает, Visual Studio добавляет расширение **Azure Data Lake и Stream Analytic Tools** в расписание обновлений.

6. Закройте все окна Visual Studio. Появляется **диалоговая коробка VSIX Installer.**

7. Выберите **лицензию** для чтения условий лицензии, а затем выберите **Close,** чтобы вернуться в диалоговую будку **VSIX Installer.**

8. Выберите **Изменить**. Начинается установка обновления расширения. Через некоторое время диалоговая коробка меняется, чтобы показать, что это сделано вносить изменения. Выберите **Закрыть**, а затем перезапустить Visual Studio для завершения установки.

> [!NOTE]  
> Чтобы подключиться к кластерам Interactive Query и выполнять интерактивные запросы Hive, можно использовать средства Data Lake только версии 2.3.0.0 или более поздней.

## <a name="connect-to-azure-subscriptions"></a>Подключение к подпискам Azure

Вы можете использовать Data Lake Tools для визуальной студии для подключения к кластерам HDInsight, выполнения некоторых основных операций по управлению и запуска запросов Hive.

> [!NOTE]  
> Для получения информации о подключении к общему кластеру Hadoop [см. Как писать и отправлять запросы Hive с помощью Visual Studio](https://blogs.msdn.microsoft.com/xiaoyong/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio/).

### <a name="connect-to-an-azure-subscription"></a>Подключение к подписке Azure

Чтобы подключиться к подписке Azure, сделайте следующее:

1. Запустите Visual Studio.

2. В окне **старта** выберите **Продолжить без кода**.

3. В панели меню IDE выберите **View** > **Server Explorer.**

4. В **Server Explorer**, правой кнопкой мыши **Azure**, выберите **Подключитесь к подписке Microsoft Azure**и завершите процесс проверки подлинности. От **Server Explorer**— расширьте **Azure** > **HDInsight,** чтобы просмотреть список существующих кластеров HDInsight.

5. Если у вас нет кластеров, создайте их с помощью портала Azure, Azure PowerShell или HDInsight SDK. Для получения дополнительной информации смотрите [Настройка кластеров в HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Список кластеров HDInsight, Исследователь серверов, Визуальная студия](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png)

6. Разверните кластер HDInsight. Кластер содержит узлы для **баз данных Hive.** Кроме того, учетная запись хранения по умолчанию, любые дополнительные связанные учетные записи хранения, и **журнал службы Hadoop**. Развертывание объектов можно продолжить.

После подключения к подписке Azure можно будет выполнить следующие задачи.

### <a name="connect-to-azure-from-visual-studio"></a>Подключение к Azure из визуальной студии

Чтобы подключиться к порталу Azure из Visual Studio, сделайте следующее:

1. В **Server Explorer**расширьте **Azure** > **HDInsight** и выберите кластер.

2. Нажмите правой кнопкой мыши кластера HDInsight и выберите **кластер управления на портале Azure.**

### <a name="offer-questions-and-feedback-from-visual-studio"></a>Предлагайте вопросы и отзывы от Visual Studio

Задавать вопросы и или предоставлять обратную связь от Visual Studio:

1. На сервере Explorer выберите **Azure** > **HDInsight.**

2. Нажмите **правой** кнопкой МЫШИ HDInsight и выбрать либо **MSDN форум** задавать вопросы, или **дать обратную связь,** чтобы дать обратную связь.

## <a name="link-to-or-edit-a-cluster"></a>Ссылка или отодвинет кластер

> [!NOTE]
> В настоящее время единственным типом кластера HDInsight, к который можно связать, является тип Hive.

Чтобы связать кластер HDInsight:

1. Нажмите **правой**кнопкой мыши HDInsight , а затем выберите **ссылку HDInsight кластера** для отображения **ссылку HDInsight кластера** диалоговое окно.

2. Введите **Url** соединения `https://CLUSTERNAME.azurehdinsight.net`в форме. **Название кластера** автоматически заполняет часть названия кластера вашего URL-адреса при пересчете на другое поле. Затем введите **имя пользователя** и **пароль**и выберите **Следующий**.

    ![Свяжите кластер, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

3. Нажмите кнопку **Готово**. Если кластерная связь успешна, кластер перечисляется под узлы **HDInsight.**

Чтобы обновить связанный кластер, нажмите вправо на кластер и выберите **Edit.** Затем можно обновить информацию о кластере.

![Отобразить связанный кластер, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Анализ связанных ресурсов

В обозревателе сервера отобразятся учетная запись хранения по умолчанию и все связанные учетные записи хранения. Если развернуть учетную запись хранения по умолчанию, отобразятся контейнеры в учетной записи хранения. Учетная запись хранения по умолчанию и контейнер по умолчанию отмечены.

![Data Lake Tools для визуальной студии связаны с ресурсами в Server Explorer](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png)

Нажмите справа на контейнер и выберите **Контейнер view** для просмотра содержимого контейнера. После открытия контейнера, вы можете использовать кнопки панели инструментов, чтобы **обновить** список содержимого, **Загрузить Blob**, **Удалить выбранные капли,** **Open Blob**, и скачать (**Сохранить как**) выбранные капли.

![Контейнерный список и операции капли, кластер HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png)

## <a name="run-interactive-apache-hive-queries"></a>Выполнение интерактивных запросов Apache Hive

[Apache Hive](https://hive.apache.org) — это инфраструктура хранилища данных, встроенная в Hadoop. Hive используется, чтобы обобщать, запрашивать и анализировать данные. Средства Data Lake для Visual Studio поддерживают выполнение запросов Hive из Visual Studio. Для получения дополнительной информации о Hive, [см. Что такое Apache Hive и Hive'L на Azure HDInsight?](hdinsight-use-hive.md).

[Интерактивный запрос в Azure HDInsight](../interactive-query/apache-interactive-query-get-started.md) использует [Hive на LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) в Apache Hive 2.1. Интерактивный запрос приводит к интерактивности сложных запросов в стиле хранилища данных на больших, хранимых наборах данных. Запуск запросов Hive в Интерактивном Запросе намного быстрее, чем традиционные задания по пакету Hive. 

> [!NOTE]  
> Выполнение интерактивных запросов Hive поддерживается только при подключении к кластеру [интерактивных запросов HDInsight](../interactive-query/apache-interactive-query-get-started.md).

Вы также можете использовать Data Lake Tools для визуальной студии, чтобы увидеть, что находится внутри работы Hive. Средства Data Lake для Visual Studio собирают и обрабатывают журналы YARN определенных заданий Hive.

На **server Explorer**выберите **Azure** > **HDInsight** и выберите кластер.  Этот узло является отправной точкой в **Server Explorer** для разделов для подражания.

### <a name="view-hivesampletable"></a>Просмотр таблицы hivesampletable

Все кластеры HDInsight имеют образец `hivesampletable`таблицы Hive по умолчанию под названием.  

Из вашего кластера выберите **Hive Databases** > **по умолчанию** > **hivesampletable.**

* Для просмотра `hivesampletable` схемы:

    Расширить **улей.** Отображаются имена `hivesampletable` и типы данных столбцов.

* Для просмотра `hivesampletable` данных:

    Правый клик **hivesampletable**, и выберите **Посмотреть Топ 100 строк**. Список из 100 результатов отображается в **таблице Hive Table: hivesampletable** window. Это действие эквивалентно запуску следующего запроса Hive с помощью драйвера Hive ODBC:

    `SELECT * FROM hivesampletable LIMIT 100`

    Вы можете настроить количество строк, **изменив количество строк;** Вы можете выбрать 50, 100, 200 или 1000 строк из списка выпадающих.

### <a name="create-hive-tables"></a>Создание таблиц Hive

Таблицу Hive можно создать с помощью графического интерфейса пользователя (GUI) или запросов Hive. Для получения информации об использовании запросов Hive [см.](#create-and-run-hive-queries)

1. Из кластера выберите **базы данных** > Hive**по умолчанию.**

2. Нажмите по **умолчанию,** и выберите **Создать таблицу**.

3. Настройка таблицы.

4. Выберите кнопку **«Создание таблицы»,** чтобы отправить задание, которое создает новую таблицу Hive.

    ![Создание окна таблицы, Hive, кластер HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png)

### <a name="create-and-run-hive-queries"></a>Создание и запуск запросов Hive

Есть два способа создания и выполнения запросов Hive.

* Создание ad-hoc-запросов
* Создание приложения Hive

#### <a name="create-an-ad-hoc-query"></a>Создание специального запроса

Для создания и запуска специального запроса:

1. Нажмите на кластер, где вы хотите запустить запрос, и выберите **Написать запрос Hive.**  

2. Введите запрос Hive.

    Редактор Hive поддерживает технологию IntelliSense. Средства Data Lake для Visual Studio поддерживают загрузку удаленных метаданных при редактировании скрипта Hive. Например, при `SELECT * FROM`вводе IntelliSense перечислены все предложенные названия таблиц. Если указано имя таблицы, IntelliSense выведет список имен столбцов. Эти инструменты поддерживают почти все инструкции, подзапросы и встроенные определяемые пользователем функции Hive DML.

    ![Пример IntelliSense 1, специальный запрос Hive, кластер HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png)

    ![IntelliSense пример 2, Hive специальный запрос, КЛАСТЕР HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png)

    > [!NOTE]  
    > IntelliSense предлагает только метаданные кластеров, выбранных на панели инструментов HDInsight.

    Вот пример запроса, который можно использовать:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. Выберите режим выполнения:

    * **Интерактивная**  

        В первом списке выпадающих вниз, выберите **Интерактивный**, а затем выберите **Выполнить**.

        ![Интерактивный режим, специальный запрос Hive, кластер HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Пакетная служба Azure**  

        В первом списке выпадающих вниз, выберите **пакет,** а затем выберите **Отправить**. Или выберите выпадающий значок рядом с **отправкой** и выберите **Advanced.**

        ![Режим пакета, специальный запрос Hive, кластер HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)

        При выборе расширенной опции отправки появляется диалоговый ящик **Отправки сценария.** Нанастройка **названия вакансий,** **аргументов,** **дополнительных конфигураций**и **каталога состояния** для сценария.

        ![Отправить поле диалога сценариев, Чате специальный запрос, кластер HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png)

      > [!NOTE]  
      > Нельзя отправлять пакеты в кластеры интерактивных запросов.  Вы должны использовать интерактивный режим.

#### <a name="create-a-hive-application"></a>Создание приложения Hive

Для создания и запуска решения Hive сделайте следующее:

1. Из меню бар, выберите **файл** > **новый** > **проект**.

2. В **новом** окне проекта выберите окно поиска и **введите Hive.** Затем выберите **приложение Hive** и выберите **Следующую.**

3. В **новом** окне проекта введите **имя проекта,** выберите или создайте **местоположение**проекта, а затем выберите **Создать.**

    ![Новое приложение Hive, Наверстывайте новое окно проекта, HDInsight Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png)

4. В **обозревателе решений** дважды щелкните файл **Script.hql**, чтобы открыть его.

### <a name="view-job-summary-and-output"></a>Просмотр резюме и вывода вакансий

Резюме задания немного варьируется между **пакетом** и **интерактивным** режимом.

![Hive работу резюме окна, пакет и интерактивный режим, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png)

Используйте значок **Обновления** для обновления статуса до тех пор, пока статус задания не изменится на **готовое.**  

* Для получения сведений о работе из режима **Batch** выберите ссылки внизу, чтобы просмотреть **запрос о вакансиях,** **выход на работу**или **журнал вакансий,** или **просмотреть журналы yarn.**

* Подробную информацию о работе из **интерактивного** режима можно узнать на **выходных** и выходных стекол **HiveServer2.**

    ![Улей интерактивный выход задания, HDInsight кластера, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png)

### <a name="view-job-graph"></a>Просмотр графика задания

В настоящее время графики заданий отображаются только для заданий Hive, которые используют Tez в качестве двигателя исполнения.  Для получения информации о включении Tez, [см. Что такое Apache Hive и Hive'L на Azure HDInsight?](hdinsight-use-hive.md).  Смотрите также, [используйте Apache Tez вместо карты Уменьшить](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Чтобы просмотреть все операторы внутри вершины, дважды щелкните вершины графика задания. Можно выбрать также определенный оператор, чтобы увидеть о нем дополнительные сведения.

Даже если Tez указан как движок выполнения, график выполнения может не отображаться, если приложение Tez не запущено.  Такая ситуация может возникнуть из-за того, что задание не содержит dML-инструкций. Или потому, что dML-операторы могут возвращаться без запуска приложения Tez. Например, `SELECT * FROM table1` не запустит приложение Tez.

![График работы Apache Hive, Визуальная студия](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png)

### <a name="view-task-execution-detail"></a>Просмотр детали выполнения задачи

Из графика заданий можно выбрать **деталь выполнения задач,** чтобы получить структурированную и визуализированную информацию для заданий Hive. Вы также можете получить более подробную информацию о работе. При появлении проблем с производительностью представление можно использовать для получения дополнительных сведений о проблеме. Например, можно получить информацию о том, как работает каждая задача, и подробную информацию о каждой задаче (данные, прочитаные/записные данные, расписание/время начала/окончания и многое другое). Эти наглядные сведения помогут вам изменить конфигурацию задания или архитектуру системы.

![Окно представления выполнения задач, инструменты визуальной студии Data Lake](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png)

### <a name="view-hive-jobs"></a>Просмотр заданий Hive

Для заданий Hive можно просмотреть запросы задания, выходные данные задания, журнал задания и журналы YARN.

В последнем выпуске инструментов, вы можете увидеть, что находится внутри ваших заданий Hive, собирая и всплывая журналы yarn. Журнал YARN может помочь исследовать проблемы производительности. Для получения дополнительной информации о том, как HDInsight собирает журналы Yarn, [см.](../hdinsight-hadoop-access-yarn-app-logs-linux.md)

Для просмотра заданий Hive сделайте следующее:

1. Нажмите правой кнопкой мыши кластера HDInsight и выберите **Просмотр вакансий.**

    ![Посмотреть вакансии, Apache Hive, КЛАСТЕР HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png)

    Появится список заданий Hive, выполняющихся в кластере.  

2. Выберите задание. В окне **«Резюме работы Hive»** выберите одну из следующих ссылок:
    - **Запрос задания**
    - **Выходные данные задания**
    - **Регистрация вакансий**  
    - **Yarn Log**

## <a name="run-apache-pig-scripts"></a>Запуск скриптов Apache Pig

1. Из меню бар, выберите **файл** > **новый** > **проект**.

2. В окне **старта** выберите поле поиска и введите **Pig.** Затем выберите **Приложение Свинья** и выберите **Следующий**.

3. В новом окне **проекта введите** **имя проекта**и выберите или создайте **место** для проекта. Щелкните **Создать**.

4. В панели IDE **Solution Explorer,** дважды щелкните **Script.pig,** чтобы открыть сценарий.

## <a name="feedback-and-known-issues"></a>Отзывы и известные проблемы

* Проблема, при которой результаты, начинающиеся со значений Null, не отображались, исправлена. Если эта проблема мешает вам работать, обратитесь в нашу службу поддержки.

* Сценарий H'L, который создает Visual Studio, кодируется в зависимости от локальной настройки локальной области пользователя. Скрипт выполняется неправильно, если вы загружаете его в кластер как двоичный файл.

## <a name="next-steps"></a>Следующие шаги

Из этой статьи вы узнали, как подключаться к кластерам HDInsight из Visual Studio, используя пакет средств Data Lake для Visual Studio, и выполнять запросы Hive. 

* [Run Apache Hive queries using the Data Lake tools for Visual Studio](apache-hadoop-use-hive-visual-studio.md) (Выполнение запросов Apache Hive с использованием средств Data Lake для Visual Studio)
* [Обзор Apache Hive и HiveQL в Azure HDInsight](hdinsight-use-hive.md)
* [Создание кластера Apache Hadoop — шаблон](apache-hadoop-linux-tutorial-get-started.md)
* [Отправить Apache Hadoop рабочих мест в HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Анализ данных Twitter с помощью Apache Hive и Apache Hadoop в HDInsight](../hdinsight-analyze-twitter-data-linux.md)
