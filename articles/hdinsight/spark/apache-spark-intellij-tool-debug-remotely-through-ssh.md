---
title: 'Azure Toolkit for IntelliJ: Отладка приложений Spark с помощью SSH-HDInsight'
description: Пошаговые инструкции по использованию инструментов HDInsight из набора средств Azure для IntelliJ для удаленной отладки приложений на кластерах HDInsight через SSH.
keywords: удаленная отладка intellij, ssh, intellij, hdinsight, отладка intellij, отладка
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/25/2017
ms.openlocfilehash: 82aaead87fad0ed9fc7b715baf3dc5ebbd4941be
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494606"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Отладка Apache Spark приложений в кластере HDInsight с Azure Toolkit for IntelliJ через SSH

В этой статье описано, как использовать инструменты HDInsight из [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) для удаленной отладки приложений в кластере HDInsight. Для отладки проекта можно также просмотреть видео [Debug HDInsight Spark applications with Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Отладка приложений HDInsight Spark с помощью набора средств Azure для IntelliJ).

**Предварительные требования**
* **Средства HDInsight из набора средств Azure для IntelliJ**. Этот инструмент входит в состав набора средств Azure для IntelliJ. Дополнительные сведения см. в статье [Установка набора средств Azure для IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation). Также ознакомьтесь с **набором средств Azure для IntelliJ**. Этот набор средств используется для создания приложений Apache Spark для кластера HDInsight. См. дополнительные сведения о [создании приложений Apache Spark для кластера HDInsight с помощью Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).

* **Служба SSH HDInsight с функцией управления именем пользователя и паролем.** См. дополнительные сведения о [подключении к HDInsight (Apache Hadoop) с помощью SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) и [использовании туннелирования SSH для доступа к пользовательскому веб-интерфейсу Ambari, JobHistory, NameNode, Apache Oozie и другим пользовательским веб-интерфейсам](https://docs.microsoft.com/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Инструкции по выполнению локального запуска и отладки
### <a name="scenario-1-create-a-spark-scala-application"></a>Сценарий 1. Создание приложения Spark Scala 

1. Запустите IntelliJ IDEA и создайте проект. В диалоговом окне **Новый проект** сделайте следующее:

   а. Выберите **Azure Spark/HDInsight**. 

   b. Выберите шаблон Java или Scala (на свой выбор). Выберите один из следующих вариантов:

   - **Проект Spark (Java)**

   - **Проект Spark (Scala)**

   - **Проект Spark с примерами (Scala)**

   - **Проект Spark с примерами отладки задач сбоя (Предварительная версия) (Scala)**

     В этом примере используется шаблон **проекта Spark с примерами (Scala)** .

   c. В списке **средств сборки** выберите один из следующих вариантов:

   - **Maven**для поддержки мастера создания проектов Scala.

   - **SBT**для управления зависимостями и сборкой проекта Scala.

     ![IntelliJ создать новый проект Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   г) Щелкните **Далее**.

1. В следующем окне **New Project** (Новый проект) сделайте следующее.

   ![IntelliJ новый проект выберите версию Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   а. Введите имя и расположение проекта.

   b. Из раскрывающегося списка **Project SDK** (Пакет SDK проекта) выберите **Java 1.8** для кластера **Spark 2.x** или **Java 1.7** для кластера **Spark 1.x**.

   c. В раскрывающемся списке **Spark version** (Версия Spark) мастер создания проекта Scala интегрирует правильную версию пакета SDK для Spark и пакета SDK для Scala. Если версия кластера Spark ниже 2.0, выберите **Spark 1.x**. В противном случае выберите **Spark 2.x.** В этом примере используется **Spark 2.0.2 (Scala 2.11.8)** .

   г) Выберите **Готово**.

1. Выберите **src** > **main** > **scala**, чтобы открыть код в проекте. В этом примере используется сценарий **SparkCore_wasbloTest**.

### <a name="prerequisite-for-windows"></a>Предварительные требования для Windows
При запуске локального приложения Spark Scala на компьютере с Windows может возникнуть исключение, описанное в статье о [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Это исключение возникает, так как в Windows отсутствует файл WinUtils.exe.

Чтобы устранить эту ошибку, [скачайте этот исполняемый файл](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe), например в папку **C:\WinUtils\bin**. После этого добавьте переменную среды **HADOOP_HOME** и присвойте ей значение **C\WinUtils**.

### <a name="scenario-2-perform-local-run"></a>Сценарий 2. Выполнение локального запуска

1. Откройте скрипт **SparkCore_wasbloTest**, щелкните правой кнопкой мыши редактор скриптов и выберите параметр **Run '[Spark Job]XXX'** , чтобы выполнить локальный запуск.

1. По завершении локального выполнения выходной файл сохраняется в текущий обозреватель проектов: **данные** >  **__по умолчанию__** .

    ![Результат локального запуска проекта IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)

1. С помощью наших средств при локальном запуске и локальной отладке по умолчанию настраивается конфигурацию локального запуска. Откройте конфигурацию **[Spark в hdinsight] XXX** в правом верхнем углу, вы увидите **[Spark в hdinsight] XXX** , уже созданную в разделе **Apache Spark в HDInsight**. Откройте вкладку **Locally Run** (Локальный запуск).

    ![IntelliJ запуск конфигураций отладки локальный запуск](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)

    - [Переменные среды.](#prerequisite-for-windows) Если вы уже задали переменную среды **HADOOP_HOME** для **C:\WinUtils**, она определяется автоматически и добавлять ее вручную не нужно.
    - [Расположение WinUtils.exe.](#prerequisite-for-windows) Если вы не задали переменную среды, найдите расположение, нажав соответствующую кнопку.
    - Просто выберите один из вариантов (в MacOS и Linux в этом нет необходимости).

1. Кроме того, вы можете вручную задать конфигурацию перед локальным запуском и локальной отладкой. На предыдущем снимке экрана щелкните знак плюса ( **+** ). Затем выберите параметр **Apache Spark в HDInsight** . Укажите **имя** и **имя класса Main**, чтобы сохранить их, а затем нажмите кнопку локального запуска.

### <a name="scenario-3-perform-local-debugging"></a>Сценарий 3. Локальная отладка
1. Откройте скрипт **SparkCore_wasbloTest** и задайте точки останова.
1. Щелкните правой кнопкой мыши редактор скриптов и выберите пункт **Отладка "[Spark в HDInsight] XXX"** , чтобы выполнить локальную отладку.

## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Инструкции по удаленному запуску и отладке
### <a name="scenario-1-perform-remote-run"></a>Сценарий 1. Удаленный запуск

1. Чтобы перейти в меню **Edit Configurations** (Изменение конфигураций), щелкните значок в правом верхнем углу. В этом меню можно создавать и редактировать конфигурации для удаленной отладки.

   ![Изменение конфигураций HDI IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png)

1. В диалоговом окне **Run/Debug Configurations** (Конфигурации выполнения и отладки) щелкните знак "плюс" ( **+** ). Затем выберите параметр **Apache Spark в HDInsight** .

   ![IntelliJ добавить новую конфигурацию](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)

1. Перейдите в режим **удаленного запуска на вкладке кластер** . Введите данные для **имени**, **кластера Spark**и **имени класса Main**. Затем щелкните **Расширенная конфигурация (удаленная отладка)** . Наши средства поддерживают отладку с **исполнителями**. Для параметра **numExectors** по умолчанию устанавливается значение 5. Лучше не указывать значение больше 3.

   ![IntelliJ запуск конфигураций отладки](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. В части **Расширенная конфигурация (удаленная отладка)** выберите **включить удаленную отладку Spark**. Введите имя пользователя SSH, затем введите пароль либо воспользуйтесь файлом закрытого ключа. Для выполнения удаленной отладки необходимо настроить ее. Чтобы использовать удаленный запуск, не нужно настраивать отладку.

   ![Расширенная конфигурация IntelliJ включение удаленной отладки Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. Теперь конфигурация сохранена под именем, которое вы указали. Чтобы просмотреть сведения о конфигурации, выберите имя конфигурации. Чтобы внести изменения, выберите **Edit Configurations** (Изменить конфигурации).

1. Завершив настройку конфигурации, можно запустить проект на удаленном кластере или выполнить удаленную отладку.

   ![Кнопка удаленного запуска IntelliJ отладки удаленного задания Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. Нажмите кнопку **Отключить**, чтобы журналы отправки не отображались на левой панели. Тем не менее она все еще выполняется в серверной части.

   ![Результат удаленного запуска IntelliJ отладки удаленного задания Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

### <a name="scenario-2-perform-remote-debugging"></a>Сценарий 2. Удаленная отладка
1. Настройте точки останова, а затем щелкните значок **Remote debug** (Удаленная отладка). Отличие удаленной отладки заключается в том, что требуется настроить имя пользователя и пароль SSH.

   ![Значок отладки задания удаленной отладки Spark IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Когда выполнение программы достигнет точки останова, в области **Debugger** (Отладчик) вы увидите вкладку **Driver** (Драйвер) и две вкладки **Executor** (Исполнитель). Щелкните значок **Resume Program** (Возобновить выполнение программы), чтобы продолжить выполнение кода, который затем достигнет следующей точки останова. Откройте соответствующую вкладку **Исполнитель**, чтобы найти целевой исполнитель для отладки. Вы можете просмотреть журналы выполнения на соответствующей вкладке **Консоль**.

   ![Вкладка "Отладка IntelliJ отладки заданий удаленного задания Spark"](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Сценарий 3. Удаленная отладка и исправление ошибок

1. Настройте две точки останова и щелкните значок **Debug** (Отладка), чтобы приступить к удаленной отладке.

1. Выполнение кода будет приостановлено в первой точке останова, а в области **Variables** (Переменные) отобразятся сведения о параметре и переменной.

1. Щелкните значок **Resume Program** (Возобновить выполнение программы), чтобы продолжить. Выполнение кода будет приостановлено во второй точке останова. Как и ожидалось, возникнет исключение.

   ![Ошибка при вызове IntelliJ отладки для удаленного задания Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png)

1. Снова щелкните значок **Resume Program** (Возобновить выполнение программы). В окне **HDInsight Spark Submission** (Отправка HDInsight Spark) будет указано, что во время выполнения задания произошла ошибка.

   ![Отправка сообщения об ошибке IntelliJ отладки удаленного задания Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png)

1. Чтобы динамически обновить значение переменной с помощью функции отладки IntelliJ, еще раз щелкните значок **Debug** (Отладка). Снова появится область **Variables** (Переменные).

1. Щелкните правой кнопкой мыши целевой объект на вкладке **Debug** (Отладка), а затем выберите **Set Value** (Задать значение). Затем введите новое значение переменной. Нажмите клавишу **ВВОД**, чтобы сохранить значение.

   ![Значение набора заданий IntelliJ Debug Remote Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. Щелкните значок **Resume Program** (Возобновить выполнение программы), чтобы продолжить выполнение программы. На этот раз исключение не возникает. Проект выполняется успешно без исключений.

   ![IntelliJ Отладка удаленного задания Spark без исключения](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Дальнейшие действия

* [Обзор: Apache Spark в Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Демонстрация
* Создание проекта Scala (видео): [создание приложений Scala Apache Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Удаленная отладка (видео): [Use Azure Toolkit for IntelliJ to debug Spark applications remotely on an HDInsight cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Удаленная отладка приложений Apache Spark в кластере HDInsight с помощью Azure Toolkit for IntelliJ)

### <a name="scenarios"></a>Сценарии
* [Руководство. Анализ данных Apache Spark с использованием Power BI в HDInsight](apache-spark-use-bi-tools.md)
* [Apache Spark и Машинное обучение. Анализ температуры в здании на основе данных системы кондиционирования с помощью Spark в HDInsight](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark и Машинное обучение. Прогнозирование результатов проверки пищевых продуктов с помощью Spark в HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журнала веб-сайта с использованием Apache Spark в HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Apache Livy в кластере Apache Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Создание приложений Apache Spark для кластера HDInsight с помощью Azure Toolkit for IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Spark в HDInsight через VPN с помощью Azure Toolkit for IntelliJ](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование средств HDInsight в Azure Toolkit for Eclipse для создания приложений Apache Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Использование записных книжек Zeppelin с кластером Apache Spark в Azure HDInsight](apache-spark-zeppelin-notebook.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Apache Spark в HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](apache-spark-job-debugging.md)
