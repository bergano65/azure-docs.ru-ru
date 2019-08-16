---
title: Устранение неполадок со Spark в Azure HDInsight
description: Получите ответы на распространенные вопросы о работе с Apache Spark и Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: c88136fee7a75b8f3b8e504b1ff1e6673a31bcf7
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543174"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Устранение неполадок в Apache Spark с помощью Azure HDInsight

Ознакомьтесь с основными проблемами, которые возникают при работе с полезными данными [Apache Spark](https://spark.apache.org/) в [Apache Ambari](https://ambari.apache.org/), и способами их решения.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Как настроить приложение Apache Spark с помощью Apache Ambari в кластерах?

### <a name="resolution-steps"></a>Способы устранения

Значения конфигурации Spark можно настроить, чтобы избежать Apache Spark приложения, OutofMemoryError исключение. Следующие шаги демонстрируют значения конфигурации Spark по умолчанию в Azure HDInsight. 

1. В списке кластеров выберите **Spark2**.

    ![Выбор кластера из списка](./media/apache-troubleshoot-spark/update-config-1.png)

2. Выберите вкладку **Конфигурации** .

    ![Выбор вкладки "Конфигурации"](./media/apache-troubleshoot-spark/update-config-2.png)

3. В списке конфигураций выберите **Custom-spark2-defaults**.

    ![Выбор конфигурации custom-spark-defaults](./media/apache-troubleshoot-spark/update-config-3.png)

4. Найдите параметр значения, который необходимо настроить, например **spark.executor.memory**. В этом случае значение **4608m** слишком высокое.

    ![Выбор поля spark.executor.memory](./media/apache-troubleshoot-spark/update-config-4.png)

5. Задайте для этого параметра рекомендуемое значение. Рекомендуется использовать значение **2048m**.

    ![Изменение значения на 2048m](./media/apache-troubleshoot-spark/update-config-5.png)

6. Сохраните это значение, а затем сохраните конфигурацию. На панели инструментов нажмите кнопку **Сохранить**.

    ![Сохранение параметра и конфигурации](./media/apache-troubleshoot-spark/update-config-6a.png)

    Если нужно будет пересмотреть какую-либо конфигурацию, вы получите оповещение. Проверьте элементы, а затем нажмите кнопку **Proceed Anyway** (Продолжить). 

    ![Нажатие кнопки "Proceed Anyway" (Продолжить)](./media/apache-troubleshoot-spark/update-config-6b.png)

    Запишите примечание об изменениях конфигурации, а затем нажмите кнопку **Сохранить**.

    ![Примечание об изменениях](./media/apache-troubleshoot-spark/update-config-6c.png)

7. При каждом сохранении конфигурации вам будет предложено перезапустить службу. Нажмите кнопку **Перезапустить**.

    ![Нажатие кнопки "Перезапустить"](./media/apache-troubleshoot-spark/update-config-7a.png)

    Подтвердите перезапуск.

    ![Нажатие кнопки "Confirm Restart All" (Подтвердить перезапуск всех)](./media/apache-troubleshoot-spark/update-config-7b.png)

    Вы можете просмотреть запущенные процессы.

    ![Просмотр запущенных процессов](./media/apache-troubleshoot-spark/update-config-7c.png)

8. Вы можете добавить конфигурации. В списке конфигураций выберите **Custom-spark2-defaults**, а затем щелкните **Добавить свойство**.

    ![Выбор "Добавить свойство"](./media/apache-troubleshoot-spark/update-config-8.png)

9. Определите новое свойство. Вы можете определить отдельное свойство с помощью диалогового окна для определенных параметров, например тип данных. Или вы можете определить несколько свойств с одним определением на строку. 

    В этом примере свойство **spark.driver.memory** определяется со значением **4 ГБ**.

    ![Определение нового свойства](./media/apache-troubleshoot-spark/update-config-9.png)

10. Сохраните конфигурацию и перезапустите службу, как описано на шагах 6 и 7.

Эти изменения применяются на уровне кластера, но их можно переопределить при отправке задания Spark.

### <a name="additional-reading"></a>Дополнительные материалы для чтения

[Отправка заданий Apache Spark в кластерах HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Как настроить приложение Apache Spark с помощью Jupyter Notebook в кластерах?

### <a name="resolution-steps"></a>Способы устранения

1. Чтобы определить, какие конфигурации Spark нужно настроить и с какими значениями, см. раздел "Что вызывает в приложении Apache Spark исключение OutOfMemoryError?"

2. Укажите конфигурации Spark в допустимом формате JSON в первой ячейке Jupyter Notebook после директивы **%%configure**. При необходимости измените фактические значения:

    ![Добавление конфигурации](./media/apache-troubleshoot-spark/add-configuration-cell.png)

### <a name="additional-reading"></a>Дополнительные материалы для чтения

[Отправка заданий Apache Spark в кластерах HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Как настроить приложение Apache Spark с помощью Apache Livy в кластерах?

### <a name="resolution-steps"></a>Способы устранения

1. Чтобы определить, какие конфигурации Spark нужно настроить и с какими значениями, см. раздел "Что вызывает в приложении Apache Spark исключение OutOfMemoryError?" 

2. Отправьте приложение Spark в Livy с помощью клиента REST, например cURL. Используйте команду, аналогичную приведенной ниже. При необходимости измените фактические значения:

    ```apache
    curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
    ```

### <a name="additional-reading"></a>Дополнительные материалы для чтения

[Отправка заданий Apache Spark в кластерах HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Как настроить приложение Apache Spark с помощью spark-submit в кластерах?

### <a name="resolution-steps"></a>Способы устранения

1. Чтобы определить, какие конфигурации Spark нужно настроить и с какими значениями, см. раздел "Что вызывает в приложении Apache Spark исключение OutOfMemoryError?"

2. Запустите оболочку Spark с помощью команды, аналогичной приведенной ниже. При необходимости измените фактические значения конфигураций: 

    ```apache
    spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
    ```

### <a name="additional-reading"></a>Дополнительные материалы для чтения

[Отправка заданий Apache Spark в кластерах HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* [Общие сведения об управлении памятью Spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Отладка приложения Spark в кластерах HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключение с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Для получения более подробных сведений см. статью [о создании запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
