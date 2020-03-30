---
title: Устранение неполадок Apache Spark в Azure HDInsight
description: Получите ответы на распространенные вопросы о работе с Apache Spark и Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: 80bca2dab1d07d9b99e75e283068bff99335fa18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271945"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Устранение неполадок в Apache Spark с помощью Azure HDInsight

Узнайте о главных проблемах и их разрешениях при работе с полезной нагрузкой Apache Spark в [Apache Ambari.](https://ambari.apache.org/)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Как настроить приложение Apache Spark с помощью Apache Ambari в кластерах?

Значения конфигурации spark могут быть настроены, что `OutofMemoryError` поможет избежать исключения приложения Apache Spark. Следующие шаги показывают значения конфигурации Spark по умолчанию в Azure HDInsight:

1. Войти в Ambari `https://CLUSTERNAME.azurehdidnsight.net` с вашими учетными данными кластера. Первоначальный экран отображает панель мониторинга обзора. Есть небольшие косметические различия между HDInsight 3.6 и 4.0.

1. Перейдите к **Spark2** > **Configs**.

    ![Выбор вкладки "Конфигурации"](./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png)

1. В списке конфигураций выберите и расширите **Custom-spark2-defaults.**

1. Найдите параметр значения, который необходимо настроить, например **spark.executor.memory**. В этом случае значение **9728м** слишком высоко.

    ![Выбор конфигурации custom-spark-defaults](./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png)

1. Задайте для этого параметра рекомендуемое значение. Рекомендуется использовать значение **2048m**.

1. Сохраните это значение, а затем сохраните конфигурацию. Нажмите кнопку **Сохранить**.

    ![Изменение значения на 2048m](./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png)

    Запишите примечание об изменениях конфигурации, а затем нажмите кнопку **Сохранить**.

    ![Примечание об изменениях](./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png)

    Если нужно будет пересмотреть какую-либо конфигурацию, вы получите оповещение. Проверьте элементы, а затем нажмите кнопку **Proceed Anyway** (Продолжить).

    ![Нажатие кнопки "Proceed Anyway" (Продолжить)](./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png)

1. При каждом сохранении конфигурации вам будет предложено перезапустить службу. Нажмите кнопку **Перезапустить**.

    ![Нажатие кнопки "Перезапустить"](./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png)

    Подтвердите перезапуск.

    ![Нажатие кнопки "Confirm Restart All" (Подтвердить перезапуск всех)](./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png)

    Вы можете просмотреть запущенные процессы.

    ![Просмотр запущенных процессов](./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png)

1. Вы можете добавить конфигурации. В списке конфигураций выберите **Custom-spark2-defaults**, а затем щелкните **Добавить свойство**.

    ![Выбор "Добавить свойство"](./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png)

1. Определите новое свойство. Вы можете определить отдельное свойство с помощью диалогового окна для определенных параметров, например тип данных. Или вы можете определить несколько свойств с одним определением на строку.

    В этом примере свойство **spark.driver.memory** определяется со значением **4 ГБ**.

    ![Определение нового свойства](./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png)

1. Сохраните конфигурацию и перезапустите службу, как описано на шагах 6 и 7.

Эти изменения применяются на уровне кластера, но их можно переопределить при отправке задания Spark.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Как настроить приложение Apache Spark с помощью Jupyter Notebook в кластерах?

Укажите конфигурации Spark в допустимом формате JSON в первой ячейке Jupyter Notebook после директивы **%%configure**. При необходимости измените фактические значения:

![Добавление конфигурации](./media/apache-troubleshoot-spark/add-configuration-cell.png)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Как настроить приложение Apache Spark с помощью Apache Livy в кластерах?

Отправьте приложение Spark в Livy с помощью клиента REST, например cURL. Используйте команду, аналогичную приведенной ниже. При необходимости измените фактические значения:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Как настроить приложение Apache Spark с помощью spark-submit в кластерах?

Запустите оболочку Spark с помощью команды, аналогичной приведенной ниже. При необходимости измените фактические значения конфигураций:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>Дополнительные материалы

[Отправка заданий Apache Spark в кластерах HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* [Обзор управления искрой памяти](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Отладка приложения Spark на кластерах HDInsight.](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с - официальная учетная запись Microsoft Azure для улучшения обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации просмотрите [Как создать запрос поддержки Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
