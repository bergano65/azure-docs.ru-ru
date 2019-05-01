---
title: Использование записных книжек Zeppelin с кластером Apache Spark в Azure HDInsight
description: Пошаговые инструкции по использованию записных книжек Zeppelin с кластерами Apache Spark в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/04/2019
ms.openlocfilehash: 9fc18b11b24791c1e154d89d757408da4ab20539
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64709561"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Использование записных книжек Apache Zeppelin с кластером Apache Spark в Azure HDInsight

Кластеры Spark HDInsight включают в себя записные книжки [Apache Zeppelin](https://zeppelin.apache.org/), которые можно использовать для выполнения заданий [Apache Spark](https://spark.apache.org/). Из этой статьи вы узнаете, как использовать записную книжку Zeppelin в кластере HDInsight.

**Предварительные требования:**

* Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).
* Схема URI для основного хранилища кластеров. Это было бы `wasb://` для хранилища BLOB-объектов Azure, `abfs://` для Gen2 хранилища Озера данных Azure или `adl://` для Gen1 хранилища Озера данных Azure. Если безопасной передачи включена для хранилища BLOB-объектов или Gen2 хранилища Data Lake, URI будет `wasbs://` или `abfss://`, соответственно.  Кроме того, см. в разделе [требование безопасной передачи в службе хранилища Azure](../../storage/common/storage-require-secure-transfer.md) Дополнительные сведения.

## <a name="launch-an-apache-zeppelin-notebook"></a>Запуск записной книжки Apache Zeppelin

1. Из кластера Spark **Обзор**выберите **записной книжки Zeppelin** из **панели мониторинга кластера**. Введите учетные данные администратора для кластера.  

   > [!NOTE]  
   > Также можно открыть Zeppelin Notebook для своего кластера, открыв следующий URL-адрес в браузере. Замените **CLUSTERNAME** именем кластера:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Создайте новую записную книжку. На панели заголовка, перейдите к **записной книжки** > **создать новую заметку**.

    ![Создание записной книжки Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Создание записной книжки Zeppelin")

    Введите имя для записной книжки, а затем выберите **создать заметку**.

3. Убедитесь, что в заголовке записной книжки покажет подключенное состояние. Оно обозначается зеленой точкой в правом верхнем углу.

    ![Состояния записной книжки Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Состояния записной книжки Zeppelin")

4. Загрузите демонстрационные данные во временную таблицу. При создании кластера Spark в HDInsight, образец файла данных, `hvac.csv`, копируется в связанную учетную запись хранения в разделе `\HdiSamples\SensorSampleData\hvac`.

    В пустой абзац, созданный по умолчанию в новой записной книжке, вставьте следующий фрагмент кода.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
   
    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0), 
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    Нажмите клавиши **SHIFT+ВВОД** или кнопку **воспроизведения** для абзаца, чтобы выполнить фрагмент кода. Состояние, которое отображается в правом верхнем углу абзаца, должно изменяться в следующей последовательности: READY (ГОТОВО), PENDING (ОЖИДАЕТ), RUNNING (ВЫПОЛНЯЕТСЯ) и FINISHED (ЗАВЕРШЕНО). Выходные данные отображаются в нижней части того же абзаца. Снимок экрана выглядит следующим образом:

    ![Создание временной таблицы из необработанных данных](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Создание временной таблицы из необработанных данных")

    Можно указать заголовок для каждого абзаца. В правом углу абзаца, выберите **параметры** значок (sprocket), а затем выберите **Показывать**.  

    > [!NOTE]  
    > Интерпретатор %spark2 не поддерживается в блокнотах Zeppelin во всех версиях HDInsight, а интерпретатор %sh не поддерживается в HDInsight версии 4.0 и выше.

5. Теперь вы можете запустить инструкции Spark SQL `hvac` таблицы. Вставьте следующий запрос в новый абзац. Запрос извлекает идентификатор здания и разницу между целевой и фактической температурами для каждого здания в указанный день. Нажмите **SHIFT + ВВОД**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
    ```  

    Инструкция **%sql** в начале сообщает записной книжке, что необходимо использовать интерпретатор Livy Scala.

6. Выберите **линейчатая диаграмма** значок, чтобы изменить способ отображения.  **Параметры**, которая появляется после выбора **линейчатая диаграмма**, можно выбрать **ключи**, и **значения**.  Выходные данные показаны на снимке экрана ниже.

    ![Выполнение инструкции Spark SQL с помощью записной книжки](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Выполнение инструкции Spark SQL с помощью записной книжки")

7. Можно также запустить инструкции Spark SQL с помощью переменных в запросе. В следующем фрагменте показано, как определить переменную, `Temp`, в запросе с возможными значениями, вам необходимо использовать в запросе. При первом выполнении запроса раскрывающийся список автоматически заполняется значениями, указанными для переменной.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Вставьте этот фрагмент кода в новый абзац и нажмите клавиши **SHIFT + ВВОД**. Затем выберите **65** из **Temp** раскрывающегося списка по индийскому стандартному времени. 

8. Выберите **линейчатая диаграмма** значок, чтобы изменить способ отображения.  Затем выберите **параметры** и внесите следующие изменения:

   * **Группы:**  Добавить **targettemp**.  
   * **Значения:** 1. Удалить **даты**.  2. Добавить **temp_diff**.  3.  Изменение средства инвентаризации программного обеспечения из **SUM** для **AVG**.  

     Выходные данные показаны на снимке экрана ниже.

     ![Выполнение инструкции Spark SQL с помощью записной книжки](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Выполнение инструкции Spark SQL с помощью записной книжки")

9. Перезапустите интерпретатор Livy, чтобы выйти из приложения. Чтобы сделать это, откройте параметры интерпретатора, выбрав вошедшего в имя пользователя в правом верхнем углу и выберите **интерпретатор**.  

    ![Запуск интерпретатора](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Выходные данные Hive")

10. Прокрутите страницу до **livy**, а затем выберите **перезапустите**.  Выберите **ОК** в командной строке.

    ![Перезапуск интерпретатора Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Перезапуск интерпретатора Zeppelin")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Использование внешних пакетов с записной книжкой
Можно настроить записную книжку Zeppelin в кластере Apache Spark в HDInsight для использования внешних, предоставленных сообществом пакетов, которые не включены out-of--box в кластере. Полный список доступных пакетов можно найти в [репозитории Maven](https://search.maven.org/) . Его также можно получить из других источников. Например, полный список предоставленных сообществом пакетов можно найти в разделе [Пакеты Spark](https://spark-packages.org/).

В этой статье показано, как использовать пакет [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) с записной книжкой Jupyter.

1. Откройте параметры интерпретатора. В правом верхнем углу, выберите имя пользователя вошедшего в систему, а затем выберите **интерпретатор**.

    ![Запуск интерпретатора](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Выходные данные Hive")

2. Прокрутите страницу до **livy**, а затем выберите **изменить**.

    ![Изменение параметров интерпретатора](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Изменение параметров интерпретатора")

3. Добавьте новый ключ с именем `livy.spark.jars.packages`и присвойте ему значение в формате `group:id:version`. Если вы хотите использовать пакет [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar), для ключа необходимо задать значение `com.databricks:spark-csv_2.10:1.4.0`.

    ![Изменение параметров интерпретатора](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Изменение параметров интерпретатора")

    Выберите **Сохранить** , а затем перезапустите интерпретатор Livy.

4. Вот как можно получить значение указанного выше ключа.
   
    a. Найдите пакет в репозитории Maven. В этом руководстве мы используем [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    2. В репозитории найдите значения для параметров **GroupId**, **ArtifactId** и **Version**.
   
    ![Использование внешних пакетов с записными книжками Jupyter](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Использование внешних пакетов с записными книжками Jupyter")
   
    c. Объедините три значения, разделив их двоеточием (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Место сохранения записных книжек Zeppelin
Записные книжки Zeppelin сохраняются на головных узлах кластера. Поэтому при удалении кластера записные книжки также будут удалены. Если вы хотите сохранить записные книжки для последующего использования в других кластерах, необходимо экспортировать их после выполнения заданий. Чтобы экспортировать записную книжку, выберите **Экспорт** значок, как показано на рисунке ниже.

![Скачивание записной книжки](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Скачивание записной книжки")

Это действие сохраняет записную книжку в формате JSON в расположение для скачивания.

## <a name="livy-session-management"></a>Управление сеансом Livy
При выполнении первого абзаца кода в записной книжке Zeppelin в кластере HDInsight Spark создается новый сеанс Livy. Этот сеанс будет общим в записных книжках Zeppelin, которые вы затем создадите. Если по какой-то причине сеанс Livy будет завершен (перезагрузка кластера и т. д.), вы не сможете запускать задания из записной книжки Zeppelin.

В этом случае перед началом выполнения заданий из записной книжки Zeppelin необходимо сделать следующее:  

1. Перезапустите интерпретатор Livy из записной книжки Zeppelin. Чтобы сделать это, откройте параметры интерпретатора, выбрав вошедшего в имя пользователя в правом верхнем углу, а затем выберите **интерпретатор**.

    ![Запуск интерпретатора](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Выходные данные Hive")

2. Прокрутите страницу до **livy**, а затем выберите **перезапустите**.

    ![Перезапуск интерпретатора Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Перезапуск интерпретатора Zeppelin")

3. Запустите ячейку кода из имеющейся записной книжки Zeppelin. При этом в кластере HDInsight будет создан сеанс Livy.

## <a name="seealso"></a>Дополнительные материалы
* [Apache Spark в Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Сценарии
* [Руководство. Анализ данных Apache Spark с использованием Power BI в HDInsight](apache-spark-use-bi-tools.md)
* [Использование Apache Spark MLlib для Создание приложения машинного обучения Apache Spark в HDInsight](apache-spark-ipython-notebook-machine-learning.md)
* [Использование Apache Spark MLlib для создания приложения машинного обучения и анализа набора данных](apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журналов веб-сайтов с помощью Apache Spark в HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Apache Livy в кластере Apache Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Apache Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Apache Spark с помощью подключаемого модуля средств HDInsight для IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Ядра для записной книжки Jupyter в кластерах Apache Spark в Azure HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md 
