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
ms.openlocfilehash: bcf2f97e855126c86dbb1d74cd430704e2af3af1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932134"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Отладка заданий Apache Spark в Azure HDInsight

Из этой статьи вы узнаете, как выполнять отслеживание и отладку заданий [Apache Spark](https://spark.apache.org/), запущенных в кластерах HDInsight, с помощью пользовательского интерфейса [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), пользовательского интерфейса Spark и сервера журнала Spark. Вы создадите задание Spark с помощью записной книжки, прилагающейся к кластеру Spark: **Машинное обучение. Прогнозный анализ на основе данных контроля качества пищевых продуктов**. С помощью описанных ниже действий вы сможете отслеживать приложение, отправленное любым другим методом, например с помощью **spark-submit**.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).

* Для начала вам следует запустить записную книжку **[Машинное обучение. Прогнозный анализ на основе данных контроля качества пищевых продуктов](apache-spark-machine-learning-mllib-ipython.md)**. Перейдите по ссылке, чтобы получить инструкции по запуску этой записной книжки.  

## <a name="track-an-application-in-the-yarn-ui"></a>Отслеживание приложения в пользовательском интерфейсе YARN

1. Запустите пользовательский интерфейс YARN. Выберите **пряжу** под **панелей мониторинга кластера.**

    ![Портал Azure запускает uI YARN](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > Также пользовательский интерфейс YARN можно открыть из пользовательского интерфейса Ambari. Для запуска uI Ambari выберите **ambari дом** под **панелью мониторинга кластера.** От uI Ambari перейдите на **YARN** > **Быстрые ссылки** > активным менеджером ресурсов > **uI менеджера ресурсов.**

2. Так как вы запустили задание Spark с помощью записных книжек Jupyter, приложение получило имя **remotesparkmagics** (это стандартное имя для всех приложений, запускаемых из записных книжек). Выберите идентификатор приложения против имени приложения, чтобы получить больше информации о заданиях. Откроется представление приложения.

    ![Идентификатор идентификатор аидной идентификатора идентификатора иденти](./media/apache-spark-job-debugging/find-application-id1.png)

    Для приложений, которые запущены из записных книжек Jupyter, состояние всегда будет иметь значение **Запущено** , пока вы не выйдете из записной книжки.

3. Из представления приложения вы можете ознакомиться с подробными сведениями о контейнерах, связанных с приложением, а также изучить журналы (stdout и stderr). Пользовательский интерфейс Spark можно запустить, щелкнув ссылку в графе **URL-адрес отслеживания**, как показано ниже.

    ![Искры истории сервера скачать контейнер журналы](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Отслеживание приложения в пользовательском интерфейсе Spark

В пользовательском интерфейсе Spark вы можете подробно изучить задания Spark, которые создаются запущенным вами приложением.

1. Для запуска uI Spark из представления приложения выберите ссылку на **URL-адрес отслеживания,** как показано на снимке экрана выше. Здесь вы увидите все задания Spark, созданные приложением, которое запущено в записной книжке Jupyter.

    ![Вкладка задания сервера истории spark](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. Выберите вкладку **Исполнители,** чтобы увидеть информацию об обработке и хранении для каждого исполнителя. Вы также можете получить стек вызова, выбрав ссылку **Thread Dump.**

    ![Вкладка исполнителей сервера истории spark](./media/apache-spark-job-debugging/view-spark-executors.png)

3. Выберите вкладку **Этапы,** чтобы увидеть этапы, связанные с приложением.

    ![Вкладка сервера истории spark](./media/apache-spark-job-debugging/view-apache-spark-stages.png "Просмотр этапов Spark")

    Каждый этап может включать несколько задач. Вы можете просмотреть для них статистику выполнения, как показано ниже.

    ![Сведения о вкладках сервера истории spark](./media/apache-spark-job-debugging/view-spark-stages-details.png "Посмотреть детали этапов Spark")

4. На странице сведений об этапе вы можете открыть визуализацию DAG. Для этого разверните ссылку **Визуализация DAG** в верхней части страницы, как показано ниже.

    ![Просмотр этапов Spark в формате визуализации DAG](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    Направленный ациклический граф (DAG) представляет различные этапы в приложении. Каждый синий блок графа соответствует определенной операции Spark, вызываемой из приложения.

5. На странице сведений об этапах можно также запустить представление временной шкалы для приложения. Для этого разверните ссылку **Представление временной шкалы** в верхней части страницы, как показано ниже.

    ![Представление временной шкалы для этапов Spark](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    Здесь вы увидите события Spark в формате временной шкалы. Это представление доступно на трех уровнях: для заданий, в пределах одного задания и в пределах этапа. На рисунке выше представлен пример представления временной шкалы для одного этапа.

   > [!TIP]  
   > Если установить флажок **Изменять масштаб** , представление временной шкалы можно будет прокручивать влево и вправо.

6. Другие вкладки в пользовательском интерфейсе Spark содержат полезные сведения о самом экземпляре Spark.

   * Вкладка хранения - Если приложение создает RDD, вы можете найти информацию о них во вкладке "Хранение".
   * Вкладка среды - Эта вкладка предоставляет полезную информацию о экземпляре Spark, например:
     * версия Scala;
     * каталог журнала событий, связанный с кластером;
     * число ядер исполнителя для приложения;
     * Прочее

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Поиск сведений о выполненных заданиях с помощью сервера журнала Spark

Когда задание завершается, сведения о нем сохраняются на сервере журнала Spark.

1. Для запуска сервера Spark History Server со страницы **«Обзор»** выберите **сервер истории Spark** под **панелями мониторинга кластера.**

    ![Портал Azure запускает сервер истории Spark](./media/apache-spark-job-debugging/launch-spark-history-server.png "Запуск Spark History Server1")

   > [!TIP]  
   > Также пользовательский интерфейс сервера журнала Spark можно открыть из пользовательского интерфейса Ambari. Для запуска uI Ambari с лезвия Обзор, выберите **Ambari дома** под **кластерными панелями мониторинга.** От ambari UI, перейдите к **Spark2** > **Быстрые ссылки** > **Spark2 История Сервер uI**.

2. Отобразится список всех завершенных приложений. Выберите идентификатор приложения для сверления в приложении для получения дополнительной информации.

    ![Сервер истории Spark завершены приложения](./media/apache-spark-job-debugging/view-completed-applications.png "Запуск Spark History Server2")

## <a name="see-also"></a>См. также

* [Управление ресурсами кластера Apache Spark в Azure HDInsight](apache-spark-resource-manager.md)
* [Отладка заданий Apache Spark с помощью расширенного сервера журнала Spark](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>Для специалистов по анализу данных

* [Apache Spark и Машинное обучение. Анализ температуры в здании на основе данных системы кондиционирования с помощью Spark в HDInsight](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark и Машинное обучение. Прогнозирование результатов проверки пищевых продуктов с помощью Spark в HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журналов веб-сайтов с помощью Apache Spark в HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Анализ журналов телеметрии Application Insights с помощью Apache Spark в HDInsight](apache-spark-analyze-application-insight-logs.md)


### <a name="for-spark-developers"></a>Для разработчиков Spark

* [Создание автономного приложения с использованием Scala](apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Apache Livy в кластере Apache Spark](apache-spark-livy-rest-interface.md)
* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Apache Spark с помощью подключаемого модуля средств HDInsight для IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование записных книжек Zeppelin с кластером Apache Spark в Azure HDInsight](apache-spark-zeppelin-notebook.md)
* [Ядра для записной книжки Jupyter в кластерах Apache Spark в Azure HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](apache-spark-jupyter-notebook-install-locally.md)
