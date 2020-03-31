---
title: Управление ресурсами для кластера Apache Spark в Azure HDInsight
description: Узнайте, как управлять ресурсами для кластеров Spark в Azure HDInsight для повышения производительности.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 3aab89f86dcd48328771cd0fda03d1c9de4bc2c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932106"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Управление ресурсами для кластера Apache Spark в Azure HDInsight

Узнайте, как получить доступ к интерфейсам, таким как [UI Apache Ambari,](https://ambari.apache.org/) [UI Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) и [Spark History Server,](./apache-azure-spark-history-server.md) связанный с кластером [Apache Spark,](https://spark.apache.org/) и как настроить конфигурацию кластера для оптимальной производительности.

## <a name="open-the-spark-history-server"></a>Открытие сервера журнала Spark

Сервер журнала Spark — это пользовательский веб-интерфейс для готовых и запущенных приложений Spark. Это расширение веб-uI Spark. Для получения полной информации [см.](./apache-azure-spark-history-server.md)

## <a name="open-the-yarn-ui"></a>Открытие пользовательского интерфейса YARN

Вы можете использовать пользовательский интерфейс YARN для мониторинга приложений, которые выполняются в кластере Spark в настоящее время.

1. Откройте кластер Spark на [портале Azure](https://portal.azure.com/). Дополнительные сведения см. в разделе [Отображение кластеров](../hdinsight-administer-use-portal-linux.md#showClusters).

2. Из **панелей мониторинга кластера**выберите **yarn**. При появлении запроса введите учетные данные администратора для кластера Spark.

    ![Запуск пользовательского интерфейса YARN](./media/apache-spark-resource-manager/azure-portal-dashboard-yarn.png)

   > [!TIP]  
   > Также пользовательский интерфейс YARN можно открыть из пользовательского интерфейса Ambari. От ambari UI, перейдите на **YARN** > **Быстрые ссылки** > **Активный** > менеджер ресурсов**uI**.

## <a name="optimize-clusters-for-spark-applications"></a>Оптимизация кластеров для приложений Spark

В зависимости от требований приложения можно изменять три основных параметра Spark: `spark.executor.instances`, `spark.executor.cores` и `spark.executor.memory`. Исполнитель — это процесс, запущенный для приложения Spark. Он выполняется на рабочем узле и отвечает за выполнение задач этого приложения. Число исполнителей по умолчанию и размеры исполнителя для каждого кластера определяются с учетом числа рабочих узлов и размера каждого рабочего узла. Эти сведения хранятся в файле `spark-defaults.conf` на головных узлах кластера.

Эти три параметра конфигурации можно настроить на уровне кластера (для всех приложений, работающих в кластере) или для каждого отдельного приложения.

### <a name="change-the-parameters-using-ambari-ui"></a>Изменение параметров с помощью пользовательского интерфейса Ambari

1. От пользовательского иммунитета Ambari переходите к **Spark2** > **Configs** > **Custom spark2-defaults.**

    ![Набор параметров с помощью пользовательского амбари](./media/apache-spark-resource-manager/ambari-ui-spark2-configs.png "Набор параметров с помощью пользовательского амбари")

1. Значения по умолчанию позволяют запустить в кластере одновременно четыре приложения Spark. Вы можете изменять эти значения из пользовательского интерфейса, как показано на следующем снимке экрана:

    ![Изменение параметров с помощью Ambari](./media/apache-spark-resource-manager/ambari-ui-spark2-defaults.png "Изменение параметров с помощью Ambari")

1. Выберите **Сохранить,** чтобы сохранить изменения конфигурации. В верхней части страницы вам предлагается перезапустить все затронутые службы. Нажмите кнопку **Перезапустить**.

    ![Перезапуск служб](./media/apache-spark-resource-manager/apache-ambari-restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Изменение параметров для приложения, запущенного в записной книжке Jupyter

Чтобы изменить конфигурацию для приложений, запущенных в записной книжке Jupyter, можно использовать волшебную команду `%%configure` . Желательно вносить такие изменения в начале приложения, перед запуском первой ячейки кода. Это гарантирует, что конфигурация будет применена к сеансу Livy, когда он будет создан. Если вы хотите изменить конфигурацию на более позднем этапе выполнения приложения, следует использовать параметр `-f` . Но при этом будут потеряны все результаты, полученные в приложении.

В следующем фрагменте кода показано, как изменить конфигурацию для приложения, работающего в Jupyter.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Параметры конфигурации следует передавать в виде строки JSON, расположенной сразу после команды magic, как показано в столбце примера.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Изменение параметров для приложения, отправленного с помощью spark-submit

Следующая команда демонстрирует, как можно изменять параметры конфигурации для приложения пакетной службы, отправленного с помощью `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Изменение параметров для приложения, отправленного с помощью cURL

В следующей команде показано, как изменить параметры конфигурации для приложения пакетной службы, отправленного с помощью cURL.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Изменение этих параметров на сервере Thrift Spark

Сервер Thrift Spark предоставляет доступ JDBC и ODBC к кластеру Spark. Такие инструменты, как Power BI, Tableau и так далее, используют протокол ODBC для связи с Spark Thrift Server для выполнения запросов Spark S'L в качестве приложения Spark. Когда вы создаете кластер Spark, запускаются два экземпляра сервера Thrift Spark, по одному на каждый головной узел. Каждый сервер Thrift Spark отображается в пользовательском интерфейсе YARN как приложение Spark.

Spark Thrift Server использует распределение динамических исполнителей `spark.executor.instances` Spark и, следовательно, не используется. Вместо этого сервер Thrift Spark использует `spark.dynamicAllocation.maxExecutors` и `spark.dynamicAllocation.minExecutors`, чтобы указать число исполнителей. Параметры конфигурации `spark.executor.cores`и `spark.executor.memory` используются для изменения размера исполнителя. Эти параметры можно изменить, как описано ниже.

* Расширьте **категорию Advanced spark2-thrift-sparkconf** `spark.dynamicAllocation.maxExecutors`для `spark.dynamicAllocation.minExecutors`обновления параметров и .

    ![Настройка сервера Thrift Spark](./media/apache-spark-resource-manager/ambari-ui-advanced-thrift-sparkconf.png "Настройка сервера Thrift Spark")

* Расширьте категорию **Custom spark2-thrift-sparkconf** `spark.executor.cores`для `spark.executor.memory`обновления параметров и .

    ![Настройка параметра бережливого сервера Spark](./media/apache-spark-resource-manager/ambari-ui-custom-thrift-sparkconf.png "Настройка параметра бережливого сервера Spark")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Изменение памяти драйверов для сервера Thrift Spark

Память драйверов сервера Thrift Spark настроена так, что она использует 25 % от размера ОЗУ головного узла, при условии, что общий объем ОЗУ головного узла превышает 14 ГБ. Конфигурацию памяти драйверов можно изменить с помощью пользовательского интерфейса Ambari, как показано на снимке экрана ниже.

От ambari UI, перейдите к **Spark2** > **Configs** > **Расширенный spark2-env**. Затем предоставьте значение для **spark_thrift_cmd_opts**.

## <a name="reclaim-spark-cluster-resources"></a>Освобождение кластерных ресурсов Spark

Так как используется динамическое выделение Spark, сервер Thrift потребляет только ресурсы, предназначенные для двух главных серверов приложений. Чтобы освободить эти ресурсы, следует остановить службы сервера Thrift, запущенные в кластере.

1. Из uI Ambari, с левой панели, выберите **Spark2**.

2. На следующей странице выберите **серверы Spark2 Thrift.**

    ![Перезапуск бережливого сервера1](./media/apache-spark-resource-manager/ambari-ui-spark2-thrift-servers.png "Перезапуск бережливого сервера1")

3. Вы должны увидеть два headnodes, на которых Spark2 Thrift Server работает. Выберите один из головных узлов.

    ![Перезапуск бережливого сервера2](./media/apache-spark-resource-manager/restart-thrift-server-2.png "Перезапуск бережливого сервера2")

4. На следующей странице перечислены все службы, запущенные на выбранном головном узле. Из списка выберите кнопку выпадения вниз рядом с Spark2 Thrift Server, а затем выберите **Stop.**

    ![Перезапуск бережливого сервера3](./media/apache-spark-resource-manager/ambari-ui-spark2-thriftserver-restart.png "Перезапуск бережливого сервера3")
5. Повторите эти действия на другом головном узле.

## <a name="restart-the-jupyter-service"></a>Перезапуск службы Jupyter

Откройте пользовательский веб-интерфейс Ambari, как показано в начале статьи. Из левой навигационной панели выберите **Jupyter,** выберите **действия службы,** а затем выберите **Restart All.** Служба Jupyter запустится на всех головных узлах.

![Перезапуск Jupyter](./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png "Перезапуск Jupyter")

## <a name="monitor-resources"></a>Мониторинг ресурсов

Откройте пользовательский интерфейс Yarn, как показано в начале статьи. В таблице метрик кластера в верхней части экрана проверьте значения столбцов **Memory Used** (Используемая память) и **Memory Total** (Всего памяти). Если эти два значения близки, то для запуска следующего приложения может не хватить ресурсов. То же самое относится к столбцам **VCores Used** (Используемые ядра VCore) и **VCores Total** (Всего ядер VCore). Кроме того, в основном представлении, если приложение остается в состоянии **ACCEPTED** и не переходит в состояние **RUNNING** или **FAILED,** это также может быть признаком того, что оно не получает достаточно ресурсов для запуска.

![Ограничение ресурсов](./media/apache-spark-resource-manager/apache-ambari-resource-limit.png "Ограничение ресурсов")

## <a name="kill-running-applications"></a>Завершение работы запущенных приложений

1. В ui Yarn, с левой панели, выберите **Запуск**. Из списка запущенных приложений определите, что приложение будет убито, и выберите **идентификатор.**

    ![Убить App1](./media/apache-spark-resource-manager/apache-ambari-kill-app1.png "Убить App1")

2. Выберите **приложение Kill** в правом верхнем углу, а затем выберите **OK.**

    ![Убить App2](./media/apache-spark-resource-manager/apache-ambari-kill-app2.png "Убить App2")

## <a name="see-also"></a>См. также

* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Для специалистов по анализу данных

* [Apache Spark и Машинное обучение. Анализ температуры в здании на основе данных системы кондиционирования с помощью Spark в HDInsight](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark и Машинное обучение. Прогнозирование результатов проверки пищевых продуктов с помощью Spark в HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журналов веб-сайтов с помощью Apache Spark в HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Анализ журналов телеметрии Application Insights с помощью Apache Spark в HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="for-apache-spark-developers"></a>Для разработчиков Apache Spark

* [Создание автономного приложения с использованием Scala](apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Apache Livy в кластере Apache Spark](apache-spark-livy-rest-interface.md)
* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Apache Spark с помощью подключаемого модуля средств HDInsight для IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование записных книжек Zeppelin с кластером Apache Spark в Azure HDInsight](apache-spark-zeppelin-notebook.md)
* [Ядра для записной книжки Jupyter в кластерах Apache Spark в Azure HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](apache-spark-jupyter-notebook-install-locally.md)
