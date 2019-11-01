---
title: Записные книжки Zeppelin & Apache Spark кластере Azure HDInsight
description: Пошаговые инструкции по использованию записных книжек Zeppelin с кластерами Apache Spark в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/04/2019
ms.openlocfilehash: b50baa41c4758ba3c0a405df3f54b4ea2f3f2d13
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241285"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Использование записных книжек Apache Zeppelin с кластером Apache Spark в Azure HDInsight

Кластеры Spark HDInsight включают в себя записные книжки [Apache Zeppelin](https://zeppelin.apache.org/), которые можно использовать для выполнения заданий [Apache Spark](https://spark.apache.org/). Из этой статьи вы узнаете, как использовать записную книжку Zeppelin в кластере HDInsight.

**Предварительные требования:**

* Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).
* Схема URI для основного хранилища кластеров. Это будет `wasb://` для хранилища BLOB-объектов Azure, `abfs://` для Azure Data Lake Storage 2-го поколения или `adl://` для Azure Data Lake Storage 1-го поколения. Если для хранилища BLOB-объектов включено безопасное перемещение, URI будет `wasbs://`.  Дополнительные сведения см. также [в статье требование безопасной пересылки в службе хранилища Azure](../../storage/common/storage-require-secure-transfer.md) .

## <a name="launch-an-apache-zeppelin-notebook"></a>Запуск записной книжки Apache Zeppelin

1. В разделе **Общие сведения о**кластере Spark в разделе **панели мониторинга кластера**выберите **Записная книжка Zeppelin** . Введите учетные данные администратора для кластера.  

   > [!NOTE]  
   > Также можно открыть Zeppelin Notebook для своего кластера, открыв следующий URL-адрес в браузере. Замените **CLUSTERNAME** именем кластера:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Создайте новую записную книжку. На панели заголовка перейдите к **записной книжке**  > **создать новую заметку**.

    ![Создание записной книжки Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Создание новой записной книжки Zeppelin")

    Введите имя записной книжки, а затем выберите **создать заметку**.

3. Убедитесь, что в заголовке записной книжки отображается состояние подключено. Оно обозначается зеленой точкой в правом верхнем углу.

    ![Состояние записной книжки Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Состояния записной книжки Zeppelin")

4. Загрузите демонстрационные данные во временную таблицу. При создании кластера Spark в HDInsight файл образца данных, `hvac.csv`, копируется в связанную учетную запись хранения в разделе `\HdiSamples\SensorSampleData\hvac`.

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

    ![Создание временной таблицы на основе необработанных данных](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Создание временной таблицы из необработанных данных")

    Можно указать заголовок для каждого абзаца. В правом углу абзаца выберите значок **параметров** (спроккет), а затем щелкните **отобразить заголовок**.  

    > [!NOTE]  
    > Интерпретатор %spark2 не поддерживается в блокнотах Zeppelin во всех версиях HDInsight, а интерпретатор %sh не поддерживается в HDInsight версии 4.0 и выше.

5. Теперь можно выполнять инструкции Spark SQL в таблице `hvac`. Вставьте следующий запрос в новый абзац. Запрос извлекает идентификатор здания и разницу между целевой и фактической температурами для каждого здания в указанный день. Нажмите **SHIFT + ВВОД**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
    ```  

    Инструкция **%sql** в начале сообщает записной книжке, что необходимо использовать интерпретатор Livy Scala.

6. Щелкните значок **линейчатой диаграммы** , чтобы изменить отображение.  **Параметры**, которые отображаются после выбора **линейчатой диаграммы**, позволяют выбрать **ключи**и **значения**.  Выходные данные показаны на снимке экрана ниже.

    ![Выполнение инструкции Spark SQL с помощью notebook1](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Выполнение инструкции Spark SQL с помощью notebook1")

7. Можно также запустить инструкции Spark SQL с помощью переменных в запросе. В следующем фрагменте кода показано, как определить переменную `Temp` в запросе с возможными значениями, с которыми необходимо выполнить запрос. При первом выполнении запроса раскрывающийся список автоматически заполняется значениями, указанными для переменной.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Вставьте этот фрагмент кода в новый абзац и нажмите клавиши **SHIFT + ВВОД**. Затем выберите **65** в раскрывающемся списке **TEMP** . 

8. Щелкните значок **линейчатой диаграммы** , чтобы изменить отображение.  Затем выберите **Параметры** и внесите следующие изменения:

   * **Группы:**  Добавьте **таржеттемп**.  
   * **Значения:** одного. Удалить **дату**.  2. Добавьте **temp_diff**.  3.  Измените агрегатор с **Sum** на **AVG**.  

     Выходные данные показаны на снимке экрана ниже.

     ![Выполнение инструкции Spark SQL с помощью notebook2](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Выполнение инструкции Spark SQL с помощью notebook2")

9. Перезапустите интерпретатор Livy, чтобы выйти из приложения. Для этого откройте параметры интерпретатора, выбрав имя вошедшего в систему пользователя в правом верхнем углу, а затем выберите **интерпретатор**.  

    ![Запустить интерпретатор](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Выходные данные Hive")

10. Прокрутите до **Livy**и выберите **перезапустить**.  В командной строке нажмите кнопку **ОК** .

    ![Перезапуск интерпретатора Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Перезапуск интерпретатора Zeppelin")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Использование внешних пакетов с записной книжкой
Вы можете настроить записную книжку Zeppelin в кластере Apache Spark в HDInsight для использования внешних, предоставленных сообществом пакетов, которые не включены в готовый список в кластере. Полный список доступных пакетов можно найти в [репозитории Maven](https://search.maven.org/) . Его также можно получить из других источников. Например, полный список предоставленных сообществом пакетов можно найти в разделе [Пакеты Spark](https://spark-packages.org/).

В этой статье показано, как использовать пакет [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) с записной книжкой Jupyter.

1. Откройте параметры интерпретатора. В правом верхнем углу выберите имя пользователя, выполнившего вход, а затем выберите **интерпретатор**.

    ![Запустить интерпретатор](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Выходные данные Hive")

2. Прокрутите до **Livy**, а затем выберите **изменить**.

    ![Изменение интерпретатора запуска1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Изменение интерпретатора запуска1")

3. Добавьте новый раздел с именем `livy.spark.jars.packages` и задайте его значение в формате `group:id:version`. Если вы хотите использовать пакет [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar), для ключа необходимо задать значение `com.databricks:spark-csv_2.10:1.4.0`.

    ![Изменение интерпретатора Settings2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Изменение интерпретатора Settings2")

    Выберите **сохранить** , а затем перезапустите интерпретатор Livy.

4. Вот как можно получить значение указанного выше ключа.
   
    а) Найдите пакет в репозитории Maven. В этой статье мы использовали [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    б) В репозитории найдите значения для параметров **GroupId**, **ArtifactId** и **Version**.
   
    ![Использование внешних пакетов с записной книжкой Jupyter](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Использование внешних пакетов с записной книжкой Jupyter")
   
    в) Объедините три значения, разделив их двоеточием ( **:** ).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Место сохранения записных книжек Zeppelin
Записные книжки Zeppelin сохраняются на головных узлах кластера. Поэтому при удалении кластера записные книжки также будут удалены. Если вы хотите сохранить записные книжки для последующего использования в других кластерах, необходимо экспортировать их после выполнения заданий. Чтобы экспортировать записную книжку, выберите значок **экспорта** , как показано на рисунке ниже.

![Скачать записную книжку](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Скачать записную книжку")

Это действие сохраняет записную книжку в формате JSON в расположение для скачивания.

## <a name="livy-session-management"></a>Управление сеансом Livy
При выполнении первого абзаца кода в записной книжке Zeppelin в кластере HDInsight Spark создается новый сеанс Livy. Этот сеанс будет общим в записных книжках Zeppelin, которые вы затем создадите. Если по какой-то причине сеанс Livy будет завершен (перезагрузка кластера и т. д.), вы не сможете запускать задания из записной книжки Zeppelin.

В этом случае перед началом выполнения заданий из записной книжки Zeppelin необходимо сделать следующее:  

1. Перезапустите интерпретатор Livy из записной книжки Zeppelin. Для этого откройте параметры интерпретатора, выбрав имя вошедшего в систему пользователя в правом верхнем углу, а затем выберите **интерпретатор**.

    ![Запустить интерпретатор](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Выходные данные Hive")

2. Прокрутите до **Livy**, а затем выберите **перезапустить**.

    ![Перезапуск интерпретатора Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Перезапуск интерпретатора Zeppelin")

3. Запустите ячейку кода из имеющейся записной книжки Zeppelin. При этом в кластере HDInsight будет создан сеанс Livy.

## <a name="seealso"></a>Дополнительные материалы
* [Обзор: Apache Spark в Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Сценарии
* [Использование Apache Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](apache-spark-use-bi-tools.md)
* [Apache Spark и Машинное обучение. Анализ температуры в здании на основе данных системы кондиционирования с помощью Spark в HDInsight](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark и Машинное обучение. Прогнозирование результатов проверки пищевых продуктов с помощью Spark в HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журнала веб-сайта с помощью Spark в HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Apache Livy в кластере Apache Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Apache Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Apache Spark в HDInsight через VPN с помощью Azure Toolkit for IntelliJ](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
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
