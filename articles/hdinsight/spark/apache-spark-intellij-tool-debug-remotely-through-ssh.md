---
title: 'Azure Toolkit для IntelliJ: Приложения Debug Spark с SSH - HDInsight'
description: Пошаговые инструкции по использованию инструментов HDInsight из набора средств Azure для IntelliJ для удаленной отладки приложений на кластерах HDInsight через SSH.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/23/2019
ms.openlocfilehash: 67660e3e98f5a12236798d74cc61f71616e6751d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934750"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Приложения Debug Apache Spark в кластере HDInsight с Azure Toolkit для IntelliJ через SSH

В этой статье содержатся пошаговые рекомендации по использованию инструментов HDInsight в [Azure Toolkit для удаленного](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) отладки приложений в кластере HDInsight. Для отладки проекта можно также просмотреть видео [Debug HDInsight Spark applications with Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Отладка приложений HDInsight Spark с помощью набора средств Azure для IntelliJ).

## <a name="prerequisites"></a>Предварительные требования

* Кластер Apache Spark в HDInsight. Ознакомьтесь со статьей [Краткое руководство. Создание кластера Apache Spark в HDInsight с помощью шаблона](../spark/apache-spark-jupyter-spark-sql-use-portal.md).

* Для пользователей Windows: Во время работы локального приложения Spark Scala на компьютере Windows, вы можете получить исключение, как поясняется в [SPARK-2356.](https://issues.apache.org/jira/browse/SPARK-2356) Это исключение возникает, так как в Windows отсутствует файл WinUtils.exe.

    Чтобы устранить эту ошибку, загрузите [Winutils.exe](https://github.com/steveloughran/winutils) в такое место, как **C: «WinUtils»bin**. Затем добавьте переменную среды **HADOOP_HOME,** и установите значение переменной на **C: .WinUtils**.

* [IntelliJ IDEA](https://www.jetbrains.com/idea/download/#section=windows) (Издание Сообщества бесплатно.).

* [Набор средств Azure для IntelliJ](https://docs.microsoft.com/azure/java/intellij/azure-toolkit-for-intellij-installation).

* [Плагин Scala для IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).

* Клиент SSH. Дополнительные сведения см. в руководстве по [подключению к HDInsight (Apache Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-spark-scala-application"></a>Создание приложения Spark Scala

1. Запустите IntelliJ IDEA и выберите **Create New Project** (Создать проект), чтобы открыть окно **New Project** (Новый проект).

1. Выберите **Apache Spark/HDInsight** из левой панели.

1. Выберите **проект Spark с образцами (Scala)** из главного окна.

1. Из раскрывающегося списка **Build tool** (Инструмент сборки) выберите один из следующих вариантов:

    * **Maven** для поддержки мастера проекта Scala.
    * **SBT** для управления зависимостями и строительства для проекта Scala.

     ![Intellij Создать новый проект Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

1. Нажмите кнопку **Далее**.

1. В следующем окне **нового проекта** предоставьте следующую информацию:

    |Свойство |Описание |
    |---|---|
    |Имя проекта|Введите имя. Эта прогулка `myApp`через использует .|
    |Расположение проекта|Введите необходимое расположение для сохранения проекта.|
    |Project SDK (Пакет SDK проекта)|Если пустой, выберите **Новый ...**|
    |Версия Spark|Мастер создания интегрирует правильную версию пакетов SDK для Spark и Scala. Если кластерная версия Spark раньше 2.0, выберите **Spark 1.x**. В противном случае, выберите **Spark 2.x.**. В этом примере используется **Spark 2.3.0 (Scala 2.11.8)**.|

   ![Intellij Новый проект выбрать версию Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

1. Нажмите кнопку **Готово**. Может пройти несколько минут, прежде чем проект станет доступным. Следите за ходом работ в правом нижнем углу.

1. Расширьте свой проект и перейдите на **src** > **основной** > **образец****scala.** >  Двойной клик **SparkCore_WasbIOTest**.

## <a name="perform-local-run"></a>Выполнение локального запуска

1. Из **SparkCore_WasbIOTest** скрипта, нажмите вправо редактор скрипта, а затем выберите опцию **Run 'SparkCore_WasbIOTest'** для выполнения локального запуска.

1. После завершения локального запуска можно увидеть, как выводной файл сохраняется до текущего**__по умолчанию__** **данных** > explorer project.

    ![Локальный результат запуска проекта Intellij](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)

1. С помощью наших средств при локальном запуске и локальной отладке по умолчанию настраивается конфигурацию локального запуска. Откройте конфигурацию **«Spark on HDInsight» XXX** в правом верхнем углу, вы можете увидеть **«Spark» на HDInsight-XXX,** уже созданный под **Apache Spark на HDInsight.** Откройте вкладку **Locally Run** (Локальный запуск).

    ![Конфигурации локального запуска Intellij Run](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)

    - [Переменные среды.](#prerequisites) Если вы уже задали переменную среды **HADOOP_HOME** для **C:\WinUtils**, она определяется автоматически и добавлять ее вручную не нужно.
    - [Расположение WinUtils.exe.](#prerequisites) Если вы не задали переменную среды, найдите расположение, нажав соответствующую кнопку.
    - Просто выберите один из вариантов (в MacOS и Linux в этом нет необходимости).

1. Кроме того, вы можете вручную задать конфигурацию перед локальным запуском и локальной отладкой. На предыдущем скриншоте выберите знак**+** плюс (). Затем выберите **Apache Spark на hdInsight** вариант. Укажите **имя** и **имя класса Main**, чтобы сохранить их, а затем нажмите кнопку локального запуска.

## <a name="perform-local-debugging"></a>Локальная отладка

1. Откройте скрипт **SparkCore_wasbloTest** и задайте точки останова.

1. Нажмите справа на редактор скрипта, а затем выберите опцию **Debug 'Spark на HDInsight-XXX'** для выполнения локальной отладки.

## <a name="perform-remote-run"></a>Удаленный запуск

1. Перейдите к **запуску** > **конфигураций edit...**. Из этого меню можно создавать или отсвазать конфигурации для удаленной отладки.

1. В диалоговом окне **Run/Debug Configurations** (Конфигурации выполнения и отладки) щелкните знак "плюс" (**+**). Затем выберите **Apache Spark на hdInsight** вариант.

   ![Intellij Добавить новую конфигурацию](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)

1. Переключиться на **удаленное запуск в** вкладке кластера. Введите информацию для **имени,** **кластера Spark**и **имени основного класса.** Затем нажмите **Расширенная конфигурация (удаленная дебупировка)**. Наши средства поддерживают отладку с **исполнителями**. Для параметра **numExectors** по умолчанию устанавливается значение 5. Лучше не указывать значение больше 3.

   ![Конфигурации отладки Intellij Run](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. В **расширенной конфигурации (Remote Debugging)** разделе выберите **удаленную отладку Enable Spark.** Введите имя пользователя SSH, затем введите пароль либо воспользуйтесь файлом закрытого ключа. Для выполнения удаленной отладки необходимо настроить ее. Чтобы использовать удаленный запуск, не нужно настраивать отладку.

   ![Intellij Расширенная конфигурация позволяет искры удаленного отладки](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. Теперь конфигурация сохранена под именем, которое вы указали. Чтобы просмотреть сведения о конфигурации, выберите имя конфигурации. Чтобы внести изменения, выберите **Edit Configurations** (Изменить конфигурации).

1. Завершив настройку конфигурации, можно запустить проект на удаленном кластере или выполнить удаленную отладку.

   ![Кнопка удаленного запуска Intellij Debug](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. Нажмите кнопку **Отключить**, чтобы журналы отправки не отображались на левой панели. Тем не менее она все еще выполняется в серверной части.

   ![Результат удаленного запуска Intellij Debug Remote Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

## <a name="perform-remote-debugging"></a>Удаленная отладка

1. Настройте точки останова, а затем щелкните значок **Remote debug** (Удаленная отладка). Отличие удаленной отладки заключается в том, что требуется настроить имя пользователя и пароль SSH.

   ![Intellij отладка удаленной отладки работы](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Когда выполнение программы достигнет точки останова, в области **Debugger** (Отладчик) вы увидите вкладку **Driver** (Драйвер) и две вкладки **Executor** (Исполнитель). Щелкните значок **Resume Program** (Возобновить выполнение программы), чтобы продолжить выполнение кода, который затем достигнет следующей точки останова. Откройте соответствующую вкладку **Исполнитель**, чтобы найти целевой исполнитель для отладки. Вы можете просмотреть журналы выполнения на соответствующей вкладке **Консоль**.

   ![Вкладка Intellij Debug Remote Spark Job Debugging](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="perform-remote-debugging-and-bug-fixing"></a>Удаленная отладка и исправление ошибок

1. Настройте две точки останова и щелкните значок **Debug** (Отладка), чтобы приступить к удаленной отладке.

1. Выполнение кода будет приостановлено в первой точке останова, а в области **Variables** (Переменные) отобразятся сведения о параметре и переменной.

1. Щелкните значок **Resume Program** (Возобновить выполнение программы), чтобы продолжить. Выполнение кода будет приостановлено во второй точке останова. Как и ожидалось, возникнет исключение.

   ![Intellij Debug Удаленная искра Ошибка броска работы](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png)

1. Снова щелкните значок **Resume Program** (Возобновить выполнение программы). В окне **HDInsight Spark Submission** (Отправка HDInsight Spark) будет указано, что во время выполнения задания произошла ошибка.

   ![Intellij Debug Удаленная искра Ошибка работы представления](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png)

1. Чтобы динамически обновить значение переменной с помощью функции отладки IntelliJ, еще раз щелкните значок **Debug** (Отладка). Снова появится область **Variables** (Переменные).

1. Щелкните правой кнопкой мыши целевой объект на вкладке **Debug** (Отладка), а затем выберите **Set Value** (Задать значение). Затем введите новое значение переменной. Нажмите клавишу **ВВОД**, чтобы сохранить значение.

   ![Значение набора удаленной искры Intellij Debug](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. Щелкните значок **Resume Program** (Возобновить выполнение программы), чтобы продолжить выполнение программы. На этот раз исключение не возникает. Проект выполняется успешно без исключений.

   ![Intellij Debug Удаленная искряная работа без исключения](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Обзор: Apache Spark в Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Демонстрация

* Создание проекта Scala (видео): [создание приложений Scala Apache Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Удаленная отладка (видео): [Use Azure Toolkit for IntelliJ to debug Spark applications remotely on an HDInsight cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Удаленная отладка приложений Apache Spark в кластере HDInsight с помощью Azure Toolkit for IntelliJ)

### <a name="scenarios"></a>Сценарии

* [Руководство. Анализ данных Apache Spark с использованием Power BI в HDInsight](apache-spark-use-bi-tools.md)
* [Apache Spark и Машинное обучение. Анализ температуры в здании на основе данных системы кондиционирования с помощью Spark в HDInsight](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark и Машинное обучение. Прогнозирование результатов проверки пищевых продуктов с помощью Spark в HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журналов веб-сайтов с помощью Apache Spark в HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

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
