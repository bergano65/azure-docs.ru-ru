---
title: Отладка заданий Apache Spark в Azure HDInsight
description: Использование пользовательского интерфейса YARN, пользовательского интерфейса Spark и сервера журнала Spark для отслеживания и отладки заданий, запущенных в кластере Spark в Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 110a8e86fc1916254ab914630ce10d2b7ae073b7
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74775339"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Отладка заданий Apache Spark в Azure HDInsight

Из этой статьи вы узнаете, как выполнять отслеживание и отладку заданий [Apache Spark](https://spark.apache.org/), запущенных в кластерах HDInsight, с помощью пользовательского интерфейса [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), пользовательского интерфейса Spark и сервера журнала Spark. Вы создадите задание Spark с помощью записной книжки, прилагающейся к кластеру Spark: **Машинное обучение. Прогнозный анализ на основе данных контроля качества пищевых продуктов**. С помощью описанных ниже действий вы сможете отслеживать приложение, отправленное любым другим методом, например с помощью **spark-submit**.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Технические условия

* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).

* Для начала вам следует запустить записную книжку **[Машинное обучение. Прогнозный анализ на основе данных контроля качества пищевых продуктов](apache-spark-machine-learning-mllib-ipython.md)** . Перейдите по ссылке, чтобы получить инструкции по запуску этой записной книжки.  

## <a name="track-an-application-in-the-yarn-ui"></a>Отслеживание приложения в пользовательском интерфейсе YARN

1. Запустите пользовательский интерфейс YARN. Выберите **Yarn** в разделе **панели мониторинга кластера**.

    ![портал Azure запуска пользовательского интерфейса YARN](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > Также пользовательский интерфейс YARN можно открыть из пользовательского интерфейса Ambari. Чтобы запустить пользовательский интерфейс Ambari, выберите **Ambari Home** в разделе **панели мониторинга кластера**. В пользовательском интерфейсе Ambari перейдите по адресу **YARN** > **быстрые ссылки** > Active диспетчер ресурсов > **Диспетчер ресурсов UI**.

2. Так как вы запустили задание Spark с помощью записных книжек Jupyter, приложение получило имя **remotesparkmagics** (это стандартное имя для всех приложений, запускаемых из записных книжек). Чтобы получить дополнительные сведения о задании, выберите идентификатор приложения в имени приложения. Откроется представление приложения.

    ![Сервер журнала Spark Поиск идентификатора приложения Spark](./media/apache-spark-job-debugging/find-application-id1.png)

    Для приложений, которые запущены из записных книжек Jupyter, состояние всегда будет иметь значение **Запущено** , пока вы не выйдете из записной книжки.

3. Из представления приложения вы можете ознакомиться с подробными сведениями о контейнерах, связанных с приложением, а также изучить журналы (stdout и stderr). Пользовательский интерфейс Spark можно запустить, щелкнув ссылку в графе **URL-адрес отслеживания**, как показано ниже.

    ![Журнал событий загрузки сервера журнала Spark](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Отслеживание приложения в пользовательском интерфейсе Spark

В пользовательском интерфейсе Spark вы можете подробно изучить задания Spark, которые создаются запущенным вами приложением.

1. Чтобы запустить пользовательский интерфейс Spark, в представлении приложения выберите ссылку на **URL-адрес отслеживания**, как показано на снимке экрана выше. Здесь вы увидите все задания Spark, созданные приложением, которое запущено в записной книжке Jupyter.

    ![Вкладка "задания сервера журнала Spark"](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. Перейдите на вкладку **исполнители** , чтобы просмотреть сведения об обработке и хранении для каждого исполнителя. Кроме того, стек вызовов можно получить, выбрав ссылку **дампа потока** .

    ![Вкладка "исполнители сервера журнала Spark"](./media/apache-spark-job-debugging/view-spark-executors.png)

3. Перейдите на вкладку **этапы** , чтобы просмотреть этапы, связанные с приложением.

    ![Вкладка "этапы сервера журнала Spark"](./media/apache-spark-job-debugging/view-apache-spark-stages.png "Просмотр этапов Spark")

    Каждый этап может включать несколько задач. Вы можете просмотреть для них статистику выполнения, как показано ниже.

    ![Сведения об вкладке "этапы сервера журнала Spark"](./media/apache-spark-job-debugging/view-spark-stages-details.png "Просмотр сведений о стадиях Spark")

4. На странице сведений об этапе вы можете открыть визуализацию DAG. Для этого разверните ссылку **Визуализация DAG** в верхней части страницы, как показано ниже.

    ![Просмотр этапов Spark в формате визуализации DAG](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    Направленный ациклический граф (DAG) представляет различные этапы в приложении. Каждый синий блок графа соответствует определенной операции Spark, вызываемой из приложения.

5. На странице сведений об этапах можно также запустить представление временной шкалы для приложения. Для этого разверните ссылку **Представление временной шкалы** в верхней части страницы, как показано ниже.

    ![Представление временной шкалы для этапов Spark](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    Здесь вы увидите события Spark в формате временной шкалы. Это представление доступно на трех уровнях: для заданий, в пределах одного задания и в пределах этапа. На рисунке выше представлен пример представления временной шкалы для одного этапа.

   > [!TIP]  
   > Если установить флажок **Изменять масштаб** , представление временной шкалы можно будет прокручивать влево и вправо.

6. Другие вкладки в пользовательском интерфейсе Spark содержат полезные сведения о самом экземпляре Spark.

   * Вкладка "хранилище". Если приложение создает RDD, вы можете найти сведения о них на вкладке "хранилище".
   * Вкладка "среда" — Эта вкладка содержит полезные сведения об экземпляре Spark, например:
     * версия Scala;
     * каталог журнала событий, связанный с кластером;
     * число ядер исполнителя для приложения;
     * и т. д.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Поиск сведений о выполненных заданиях с помощью сервера журнала Spark

Когда задание завершается, сведения о нем сохраняются на сервере журнала Spark.

1. Чтобы запустить сервер журнала Spark, на странице **Обзор** выберите **сервер журнала Spark** в разделе **панели мониторинга кластера**.

    ![портал Azure запустить сервер журнала Spark](./media/apache-spark-job-debugging/launch-spark-history-server.png "Запуск Сервер1 журнала Spark")

   > [!TIP]  
   > Также пользовательский интерфейс сервера журнала Spark можно открыть из пользовательского интерфейса Ambari. Чтобы запустить пользовательский интерфейс Ambari, в колонке Обзор выберите **Ambari домой** в разделе **панели мониторинга кластера**. В пользовательском интерфейсе Ambari перейдите к **Spark2** > **быстрые ссылки** > **Spark2 пользовательский интерфейс сервера журнала**.

2. Отобразится список всех завершенных приложений. Выберите идентификатор приложения для детализации приложения для получения дополнительных сведений.

    ![Завершенные приложения сервера журнала Spark](./media/apache-spark-job-debugging/view-completed-applications.png "Запуск журнала Spark Server2")

## <a name="see-also"></a>Дополнительные материалы

* [Управление ресурсами кластера Apache Spark в Azure HDInsight](apache-spark-resource-manager.md)
* [Отладка заданий Apache Spark с помощью расширенного сервера журнала Spark](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>Для специалистов по анализу данных

* [Apache Spark и Машинное обучение. Анализ температуры в здании на основе данных системы кондиционирования с помощью Spark в HDInsight](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark и Машинное обучение. Прогнозирование результатов проверки пищевых продуктов с помощью Spark в HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журнала веб-сайта с помощью Spark в HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Анализ журналов телеметрии Application Insights с помощью Apache Spark в HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Использование Caffe в кластере Azure HDInsight Spark для распределенного глубокого обучения](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Для разработчиков Spark

* [Создание автономного приложения с использованием Scala](apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Apache Livy в кластере Apache Spark](apache-spark-livy-rest-interface.md)
* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Apache Spark в HDInsight через VPN с помощью Azure Toolkit for IntelliJ](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование записных книжек Zeppelin с кластером Apache Spark в Azure HDInsight](apache-spark-zeppelin-notebook.md)
* [Ядра для записной книжки Jupyter в кластерах Apache Spark в Azure HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](apache-spark-jupyter-notebook-install-locally.md)
