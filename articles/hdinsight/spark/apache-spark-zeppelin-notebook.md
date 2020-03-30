---
title: Блокноты Цеппелина & кластера Apache Spark - Azure HDInsight
description: Пошаговые инструкции по использованию записных книжек Zeppelin с кластерами Apache Spark в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: e313048986beca1991e38ce2e65ea12f954170d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598278"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Использование записных книжек Apache Zeppelin с кластером Apache Spark в Azure HDInsight

Кластеры Spark HDInsight включают в себя записные книжки [Apache Zeppelin](https://zeppelin.apache.org/), которые можно использовать для выполнения заданий [Apache Spark](https://spark.apache.org/). Из этой статьи вы узнаете, как использовать записную книжку Zeppelin в кластере HDInsight.

## <a name="prerequisites"></a>Предварительные требования

* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).
* Схема универсального кода ресурса (URI) для основного хранилища кластеров. Это будет `wasb://` для хранилища Azure `abfs://` Blob, для хранения `adl://` данных Azure Data Lake Data Gen2 или для хранилища озер данных Azure Gen1. Если для Blob Storage включена безопасная `wasbs://`передача, URI будет .  Для получения дополнительной информации [см.](../../storage/common/storage-require-secure-transfer.md)

## <a name="launch-an-apache-zeppelin-notebook"></a>Запуск записной книжки Apache Zeppelin

1. Из **обзора**кластера Spark выберите **блокнот Цеппелин** из **панелей мониторинга кластера.** Введите учетные данные admin для кластера.  

   > [!NOTE]  
   > Также можно открыть Zeppelin Notebook для своего кластера, открыв следующий URL-адрес в браузере. Замените **CLUSTERNAME** именем кластера:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Создайте новую записную книжку. От панели заголовка перейдите на **notebook** > **Создать новую заметку.**

    ![Создание новой записной книжки Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Создание новой записной книжки Zeppelin")

    Введите имя для ноутбука, а затем выберите **Создать Примечание**.

3. Убедитесь, что заголовок ноутбука показывает подключенный статус. Это обозначается зеленой точкой в правом верхнем углу.

    ![Состояния записной книжки Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Состояния записной книжки Zeppelin")

4. Загрузите демонстрационные данные во временную таблицу. При создании кластера Spark в HDInsight файл `hvac.csv`данных образца — скопирован на связанную учетную запись хранения под `\HdiSamples\SensorSampleData\hvac`.

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

    Нажмите **SHIFT и ENTER** или выберите кнопку **воспроизведения** для абзаца для запуска фрагмента. Состояние, которое отображается в правом верхнем углу абзаца, должно изменяться в следующей последовательности: READY (ГОТОВО), PENDING (ОЖИДАЕТ), RUNNING (ВЫПОЛНЯЕТСЯ) и FINISHED (ЗАВЕРШЕНО). Выходные данные отображаются в нижней части того же абзаца. Снимок экрана выглядит следующим образом:

    ![Создание временной таблицы из необработанных данных](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Создание временной таблицы из необработанных данных")

    Можно указать заголовок для каждого абзаца. Из правого угла абзаца выберите значок **«Настройки»** (sprocket), а затем выберите **название Show.**  

    > [!NOTE]  
    > Интерпретатор %spark2 не поддерживается в блокнотах Zeppelin во всех версиях HDInsight, а интерпретатор %sh не поддерживается в HDInsight версии 4.0 и выше.

5. Теперь вы можете запускать операторы Spark S'L на `hvac` столе. Вставьте следующий запрос в новый абзац. Запрос извлекает идентификатор здания и разницу между целевой и фактической температурами для каждого здания в указанный день. **Пресс-пресс SHIFT - ENTER**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    Инструкция **%sql** в начале сообщает записной книжке, что необходимо использовать интерпретатор Livy Scala.

6. Выберите значок **диаграммы бара** для изменения дисплея.  **настройки,** которые появляются после того, как вы выбрали **диаграмму баров,** позволяют выбрать **keys**и **значения.**  Выходные данные показаны на снимке экрана ниже.

    ![Выполнить выписку Spark S'L с помощью ноутбука1](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Выполнить выписку Spark S'L с помощью ноутбука1")

7. Можно также запустить инструкции Spark SQL с помощью переменных в запросе. Следующий фрагмент показывает, как определить `Temp`переменную, в запросе с возможными значениями, которые вы хотите задать. При первом выполнении запроса раскрывающийся список автоматически заполняется значениями, указанными для переменной.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Вставьте этот фрагмент кода в новый абзац и нажмите клавиши **SHIFT + ВВОД**. Затем выберите **65** из списка выпадающих **температур.**

8. Выберите значок **диаграммы бара** для изменения дисплея.  Затем выберите **настройки** и внедь следующих изменений:

   * **Группы:**  Добавить **целевую температуру.**  
   * **Значения:** 1. Удалите **дату**.  2. Добавить **temp_diff**.  3.  Измените агрегатор с **SUM** на **AVG.**  

     Выходные данные показаны на снимке экрана ниже.

     ![Выполнить выписку Spark S'L с помощью ноутбука2](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Выполнить выписку Spark S'L с помощью ноутбука2")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Использование внешних пакетов с записной книжкой

Вы можете настроить блокнот Цеппелин в кластере Apache Spark на HDInsight, чтобы использовать внешние пакеты, внесенные сообществом, которые не включены в кластер. Полный список доступных пакетов можно найти в [репозитории Maven](https://search.maven.org/) . Его также можно получить из других источников. Например, полный список предоставленных сообществом пакетов можно найти в разделе [Пакеты Spark](https://spark-packages.org/).

В этой статье вы увидите, как использовать пакет [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) с ноутбуком Jupyter.

1. Откройте параметры интерпретатора. Из верхнего правого угла выберите зарегистрированное имя пользователя, а затем выберите **Interpreter.**

    ![Запуск интерпретатора](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Выходные данные Hive")

2. Прокрутите до **livy2,** затем выберите **edit.**

    ![Изменение настроек переводчика1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Изменение настроек переводчика1")

3. Перейдите `livy.spark.jars.packages`к ключу и установите его значение в формате. `group:id:version` Если вы хотите использовать пакет [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar), для ключа необходимо задать значение `com.databricks:spark-csv_2.10:1.4.0`.

    ![Изменение настроек переводчика2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Изменение настроек переводчика2")

    Выберите **Сохранить,** а затем **OK,** чтобы перезапустить переводчика Livy.

4. Вот как можно получить значение указанного выше ключа.

    а. Найдите пакет в репозитории Maven. Для этой статьи мы использовали [искру-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).

    b. В репозитории найдите значения для параметров **GroupId**, **ArtifactId** и **Version**.

    ![Использование внешних пакетов с записной книжкой Jupyter](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Использование внешних пакетов с записной книжкой Jupyter")

    c. Объедините три значения, разделив их двоеточием (**:**).

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Место сохранения записных книжек Zeppelin

Записные книжки Zeppelin сохраняются на головных узлах кластера. Поэтому при удалении кластера записные книжки также будут удалены. Если вы хотите сохранить записные книжки для последующего использования в других кластерах, необходимо экспортировать их после выполнения заданий. Для экспорта ноутбука выберите значок **Экспорта,** показанный на рисунке ниже.

![Скачивание записной книжки](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Скачать блокнот")

Это действие сохраняет записную книжку в формате JSON в расположение для скачивания.

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>Используйте Shiro для настройки доступа к переводчикам Цеппелина в кластерах корпоративной безопасности (ESP)
Как отмечалось `%sh` выше, переводчик не поддерживается HDInsight 4.0 и далее. Кроме того, поскольку `%sh` переводчик вводит потенциальные проблемы безопасности, такие как клавиатура доступа с использованием команд оболочки, он также был удален из кластеров HDInsight 3.6 ESP. Это `%sh` означает, что переводчик недоступен при нажатии **Создать новую заметку** или в uI-варианте переводчика по умолчанию. 

Привилегированные пользователи домена могут использовать `Shiro.ini` файл для управления доступом к пользовательму пользовательного доступа interpreter. Таким образом, только эти `%sh` пользователи могут создавать новых `%sh` переводчиков и устанавливать разрешения на каждого нового переводчика. Чтобы контролировать доступ `shiro.ini` с помощью файла, используйте следующие действия:

1. Определите новую роль, используя существующее имя доменных групп. В следующем примере `adminGroupName` группа привилегированных пользователей в AAD. Не используйте специальные символы или белые пробелы в названии группы. Персонажи после `=` предоставления разрешений на эту роль. `*`означает, что группа имеет полные разрешения.

    ```
    [roles]
    adminGroupName = *
    ```

2. Добавьте новую роль для доступа к переводчикам Цеппелина. В следующем примере все `adminGroupName` пользователи получают доступ к переводчикам Цеппелина и могут создавать новых устных переводчиков. Вы можете поместить несколько ролей `roles[]`между скобками в, разделенных запятыми. Затем пользователи, которые имеют необходимые разрешения, могут получить доступ к переводчикам Цеппелина.

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Управление сеансом Livy

При выполнении первого абзаца кода в записной книжке Zeppelin в кластере HDInsight Spark создается новый сеанс Livy. Этот сеанс будет общим в записных книжках Zeppelin, которые вы затем создадите. Если по какой-либо причине будет убит сеанс Livy (перезагрузка кластера и так далее), вы не сможете запускать задания из блокнота Цеппелин.

В этом случае перед началом выполнения заданий из записной книжки Zeppelin необходимо сделать следующее:  

1. Перезапустите интерпретатор Livy из записной книжки Zeppelin. Для этого откройте настройки интерпретатора, выбрав имя пользователя из верхнего правого угла, а затем выберите **Interpreter.**

    ![Запуск интерпретатора](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Выходные данные Hive")

2. Прокрутите до **livy2,** затем выберите **перезагрузку.**

    ![Перезапуск переводчика Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Перезапуск переводчика Цеппелина")

3. Запустите ячейку кода из имеющейся записной книжки Zeppelin. При этом в кластере HDInsight будет создан сеанс Livy.

## <a name="general-information"></a>Общие сведения

### <a name="validate-service"></a>Служба проверки

Чтобы проверить службу от Ambari, перейдите туда, `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` где clusterNAME — это название кластера.

Для проверки службы от командной строки, SSH к головному узлам. Переключите пользователя на цеппелин с помощью команды. `sudo su zeppelin` Команды статуса:

|Команда |Описание |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|Статус службы.|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|Сервисная версия.|
|`ps -aux | grep zeppelin`|Определить PID.|

### <a name="log-locations"></a>Места регистрации

|Служба |путь |
|---|---|
|цеппелин-сервер|/usr/hdp/current/zeppelin-server/|
|Журналы сервера|/var/log/zeppelin|
|Переводчик конфигурации, Широ, site.xml, log4j|/usr/hdp/current/zeppelin-server/conf/ /etc/zeppelin/conf|
|Каталог PID|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>Включение ведения журнала отладки

1. Перейдите `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` туда, где — —столько— название кластера.

1. Перейдите к **CONFIGS** > **Расширенный zeppelin-log4j-свойства** > **log4j_properties_content**.

1. Изменить `log4j.appender.dailyfile.Threshold = INFO` `log4j.appender.dailyfile.Threshold = DEBUG`до .

1. Добавьте `log4j.logger.org.apache.zeppelin.realm=DEBUG`.

1. Сохранить изменения и перезапустить службу.

## <a name="next-steps"></a>Дальнейшие действия

[Обзор: Apache Spark в Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Сценарии

* [Использование Apache Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](apache-spark-use-bi-tools.md)
* [Apache Spark и Машинное обучение. Анализ температуры в здании на основе данных системы кондиционирования с помощью Spark в HDInsight](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark и Машинное обучение. Прогнозирование результатов проверки пищевых продуктов с помощью Spark в HDInsight](apache-spark-machine-learning-mllib-ipython.md)
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
