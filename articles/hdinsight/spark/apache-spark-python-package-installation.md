---
title: Действие скрипта для пакетов Python с Jupyter в Azure HDInsight
description: Пошаговые инструкции по использованию действия скрипта для настройки записных книжек Jupyter с кластерами Spark HDInsight для использования внешних пакетов Python.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 98326d23f5aca1264bc47168cc25b427c3db331d
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/12/2020
ms.locfileid: "79135961"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Безопасное управление средой Python в Azure HDInsight с помощью действия скрипта

> [!div class="op_single_selector"]
> * [Использование волшебных команд](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Использование действия сценария](apache-spark-python-package-installation.md)

В HDInsight имеется две встроенные установки Python в кластере Spark, Anaconda Python 2,7 и Python 3,5. В некоторых случаях клиентам необходимо настроить среду Python, например установить внешние пакеты Python или другую версию Python. В этой статье мы покажем лучшую методику безопасного управления окружениями Python в кластере [Apache Spark](https://spark.apache.org/) в HDInsight.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > Если у вас еще нет кластера Spark в HDInsight на платформе Linux, можно выполнить действия сценария во время его создания. Обратитесь к документации по [использованию настраиваемых действий сценария](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Поддержка программного обеспечения с открытым исходным кодом, используемого в кластере HDInsight

Служба Microsoft Azure HDInsight использует сформированную вокруг Apache Hadoop экосистему технологий с открытым кодом. Microsoft Azure предоставляет общий уровень поддержки для технологий с открытым исходным кодом. Дополнительные сведения см. на [веб-сайте вопросы и ответы службы поддержки Azure](https://azure.microsoft.com/support/faq/). Служба HDInsight предоставляет дополнительный уровень поддержки для встроенных компонентов.

В службе HDInsight доступно два типа компонентов с открытым исходным кодом.

* **Встроенные компоненты.** Эти компоненты предварительно установлены в кластерах HDInsight и предоставляют его базовые функциональные возможности. Например, Apache Hadoop YARN диспетчер ресурсов, Apache Hive язык запросов (HiveQL) и библиотека Mahout относятся к этой категории. Полный список компонентов кластера доступен в статье [Что представляют собой компоненты и версии Apache Hadoop, доступные в HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)
* **Настраиваемые компоненты.** Как пользователь кластера вы можете установить или использовать в рабочей нагрузке любой компонент, полученный из сообщества или созданный самостоятельно.

> [!IMPORTANT]
> Компоненты, поставляемые с кластером HDInsight, полностью поддерживаются. Служба поддержки Майкрософт помогает выявлять и устранять проблемы, связанные с этими компонентами.
>
> Настраиваемые компоненты получают ограниченную коммерчески оправданную поддержку, способствующую дальнейшей диагностике проблемы. Служба поддержки Майкрософт может устранить проблему ИЛИ вас могут попросить обратиться к специалистам по технологиям с открытым исходным кодом, используя доступные каналы связи. Вы можете использовать сайты сообществ, например: [форум MSDN по HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Кроме того, для проектов Apache есть соответствующие сайты ([https://apache.org](https://apache.org)), например [Hadoop](https://hadoop.apache.org/).

## <a name="understand-default-python-installation"></a>Общие сведения об установке Python по умолчанию

Кластер HDInsight Spark создается с установкой Anaconda. В кластере есть две установки Python, Anaconda Python 2,7 и Python 3,5. В таблице ниже показаны параметры Python по умолчанию для Spark, Livy и Jupyter.

| |Python 2,7|Python 3.5|
|----|----|----|
|Path|/уср/бин/анаконда/бин|/usr/bin/anaconda/envs/py35/bin|
|Spark|Значение по умолчанию — 2,7|Н/Д|
|Livy|Значение по умолчанию — 2,7|Н/Д|
|Jupyter|Ядро PySpark|Ядро PySpark3|

## <a name="safely-install-external-python-packages"></a>Безопасная установка внешних пакетов Python

Кластер HDInsight зависит от встроенной среды Python, как Python 2,7, так и Python 3,5. Непосредственная установка пользовательских пакетов в этих встроенных средах по умолчанию может привести к непредвиденным изменениям в версии библиотеки и дальнейшему прерыванию кластера. Чтобы безопасно установить пользовательские внешние пакеты Python для приложений Spark, выполните следующие действия.

1. Создайте виртуальную среду Python с помощью conda. Виртуальная среда предоставляет изолированное пространство для проектов, не нарушая работу других. При создании виртуальной среды Python можно указать версию Python, которую вы хотите использовать. Обратите внимание, что вам все равно нужно создать виртуальную среду, даже если вы хотите использовать Python 2,7 и 3,5. Это позволяет убедиться, что среда кластера по умолчанию не нарушена. Выполните действия сценария в кластере для всех узлов со следующим скриптом, чтобы создать виртуальную среду Python. 

    -   `--prefix` указывает путь, по которому находится виртуальная среда conda. Существует несколько конфигураций, которые необходимо изменить в зависимости от пути, указанного здесь. В этом примере мы используем py35new, так как в кластере уже существует виртуальная среда с именем PY35.
    -   `python=` указывает версию Python для виртуальной среды. В этом примере используется версия 3,5, та же версия, что и у кластера, встроенного в один. Для создания виртуальной среды можно также использовать другие версии Python.
    -   `anaconda` указывает package_spec как Anaconda для установки пакетов Anaconda в виртуальной среде.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes 
    ```

2. При необходимости установите внешние пакеты Python в созданную виртуальную среду. Выполните действия сценария в кластере для всех узлов со следующим скриптом, чтобы установить внешние пакеты Python. Для записи файлов в папку виртуальной среды необходимо иметь права sudo.

    Полный список доступных пакетов можно найти в [указателе пакетов](https://pypi.python.org/pypi). Его также можно получить из других источников. Например, можно установить пакеты, предоставляемые посредством [conda-forge](https://conda-forge.org/feedstocks/).

    Используйте приведенную ниже команду, если вы хотите установить библиотеку с последней версией:
    
    - Использовать канал conda:

        -   `seaborn` — имя пакета, который вы хотите установить.
        -   `-n py35new` указать имя виртуальной среды, которое было только что создано. Не забудьте изменить имя в соответствии с созданной виртуальной средой.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - Или используйте репозиторий PyPi, измените `seaborn` и `py35new` соответствующим образом:
        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install seaborn
        ```        

    Используйте приведенную ниже команду, если вы хотите установить библиотеку с определенной версией:

    - Использовать канал conda:

        -   `numpy=1.16.1` — это имя и версия пакета, которые вы хотите установить.
        -   `-n py35new` указать имя виртуальной среды, которое было только что создано. Не забудьте изменить имя в соответствии с созданной виртуальной средой.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - Или используйте репозиторий PyPi, измените `numpy==1.16.1` и `py35new` соответствующим образом:

        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install numpy==1.16.1
        ```

    Если вы не знакомы с именем виртуальной среды, вы можете SSH-подключение к головному узлу кластера и запустить `/usr/bin/anaconda/bin/conda info -e`, чтобы отобразить все виртуальные среды.

3. Измените настройки Spark и Livy и укажите на созданную виртуальную среду.

    1. Откройте пользовательский интерфейс Ambari, перейдите на страницу Spark2, вкладку configs.
    
        ![Изменение конфигурации Spark и Livy с помощью Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)
 
    2. Разверните Advanced livy2-env, добавьте приведенные ниже инструкции в нижней части. Если вы установили виртуальную среду с другим префиксом, измените путь соответствующим образом.

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Изменение конфигурации Livy через Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Разверните Advanced spark2-env, замените существующую инструкцию экспорта PYSPARK_PYTHON в нижней части. Если вы установили виртуальную среду с другим префиксом, измените путь соответствующим образом.

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Изменение конфигурации Spark с помощью Ambari](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Сохраните изменения и перезапустите затронутые службы. Эти изменения требуют перезапуска службы Spark2. Пользовательский интерфейс Ambari запросит обязательное напоминание о перезагрузке, нажмите кнопку Перезапустить, чтобы перезапустить все затронутые службы.

        ![Изменение конфигурации Spark с помощью Ambari](./media/apache-spark-python-package-installation/ambari-restart-services.png)
 
4.  Если вы хотите использовать новую созданную виртуальную среду на Jupyter. Необходимо изменить конфигурации Jupyter и перезапустить Jupyter. Выполните действия сценария для всех узлов заголовков с помощью приведенной ниже инструкции, чтобы указать Jupyter на новую созданную виртуальную среду. Не забудьте изменить путь на префикс, указанный для виртуальной среды. После выполнения этого действия скрипта перезапустите службу Jupyter через пользовательский интерфейс Ambari, чтобы сделать это изменение доступным.

    ```
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    Можно дважды подтвердить окружение Python в Jupyter Notebook, выполнив приведенный ниже код.

    ![Проверка версии Python в Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Известная проблема

Существует известная ошибка для Anaconda версии 4.7.11, 4.7.12 и 4.8.0. Если вы видите, что действия сценария зависает на `"Collecting package metadata (repodata.json): ...working..."` и при сбое `"Python script has been killed due to timeout after waiting 3600 secs"`. Вы можете скачать [этот скрипт](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) и запустить его как действия сценария на всех узлах, чтобы устранить проблему.

Чтобы проверить версию Anaconda, можно установить SSH-подключение к узлу заголовка кластера и запустить `/usr/bin/anaconda/bin/conda --v`.

## <a name="seealso"></a> См. также

* [Обзор: Spark в Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Сценарии

* [Руководство. Анализ данных Apache Spark с использованием Power BI в HDInsight](apache-spark-use-bi-tools.md)
* [Использование Apache Spark MLlib для Создание приложения машинного обучения Apache Spark в HDInsight](apache-spark-ipython-notebook-machine-learning.md)
* [Использование Apache Spark MLlib для создания приложения машинного обучения и анализа набора данных](apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журналов веб-сайтов с помощью Apache Spark в HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Создание и запуск приложений

* [Создание автономного приложения с использованием Scala](apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Apache Livy в кластере Apache Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения

* [Использование внешних пакетов с записными книжками Jupyter в кластерах Apache Spark в HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Apache Spark с помощью подключаемого модуля средств HDInsight для IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование записных книжек Zeppelin с кластером Apache Spark в Azure HDInsight](apache-spark-zeppelin-notebook.md)
* [Ядра для записной книжки Jupyter в кластерах Apache Spark в Azure HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Управление ресурсами

* [Управление ресурсами кластера Apache Spark в Azure HDInsight](apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](apache-spark-job-debugging.md)
