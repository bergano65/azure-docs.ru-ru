---
title: Действие сценария для пакетов Python с Jupyter на Azure HDInsight
description: Пошаговые инструкции по использованию действия скрипта для настройки записных книжек Jupyter с кластерами Spark HDInsight для использования внешних пакетов Python.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 659af8b85cb3736d663e79676b04af8041aeabfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129576"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Безопасное управление средой Python в Azure HDInsight с помощью действия скрипта

> [!div class="op_single_selector"]
> * [Использование волшебных команд](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Использование действия сценария](apache-spark-python-package-installation.md)

HDInsight имеет две встроенные установки Python в кластере Spark, Anaconda Python 2.7 и Python 3.5. В некоторых случаях клиентам необходимо настроить среду Python, например, установить внешние пакеты Python или другую версию Python. В этой статье мы показываем наилучшую практику безопасного управления средами Python для кластера [Apache Spark](./apache-spark-overview.md) на HDInsight.

## <a name="prerequisites"></a>Предварительные требования

Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md). Если на HDInsight еще нет кластера Spark, можно запустить действия скрипта во время создания кластера. . Обратитесь к документации по [использованию настраиваемых действий сценария](../hdinsight-hadoop-customize-cluster-linux.md).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Поддержка программного обеспечения с открытым исходным кодом, используемого в кластере HDInsight

Служба Microsoft Azure HDInsight использует сформированную вокруг Apache Hadoop экосистему технологий с открытым кодом. Microsoft Azure предоставляет общий уровень поддержки для технологий с открытым исходным кодом. Для получения дополнительной информации на сайте [Azure Support часто задаваемые вопросы](https://azure.microsoft.com/support/faq/). Служба HDInsight предоставляет дополнительный уровень поддержки для встроенных компонентов.

В службе HDInsight доступно два типа компонентов с открытым исходным кодом.

|Компонент |Описание |
|---|---|
|Встроено|Эти компоненты предварительно установлены на кластерах HDInsight и обеспечивают основную функциональность кластера. Например, к этой категории относятся менеджер ресурсов Apache Hadoop YARN, язык запросов Apache Hive (Hive'L) и библиотека Mahout. Полный список компонентов кластера доступен в статье [Что представляют собой компоненты и версии Apache Hadoop, доступные в HDInsight?](../hdinsight-component-versioning.md)|
|Другой|Вы, как пользователь кластера, можете установить или использовать в рабочей нагрузке любой компонент, доступный в сообществе или созданный вами.|

> [!IMPORTANT]
> Компоненты, поставляемые с кластером HDInsight, полностью поддерживаются. Служба поддержки Майкрософт помогает выявлять и устранять проблемы, связанные с этими компонентами.
>
> Настраиваемые компоненты получают ограниченную коммерчески оправданную поддержку, способствующую дальнейшей диагностике проблемы. Служба поддержки Майкрософт может устранить проблему ИЛИ вас могут попросить обратиться к специалистам по технологиям с открытым исходным кодом, используя доступные каналы связи. Например, Есть много сайтов сообщества, которые могут быть использованы, [https://stackoverflow.com](https://stackoverflow.com)как: [MSDN форум для HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), . Также проекты Apache [https://apache.org](https://apache.org)имеют места проекта на, например: [Hadoop](https://hadoop.apache.org/).

## <a name="understand-default-python-installation"></a>Понимание установки Python по умолчанию

Кластер HDInsight Spark создан с установкой Anaconda. В кластере есть две установки Python: Anaconda Python 2.7 и Python 3.5. В таблице ниже показаны настройки Python по умолчанию для Spark, Livy и Jupyter.

| |Python 2,7|Python 3.5|
|----|----|----|
|путь|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark|По умолчанию установлен до 2.7|Недоступно|
|Livy|По умолчанию установлен до 2.7|Недоступно|
|Jupyter|Ядро PySpark|Ядро PySpark3|

## <a name="safely-install-external-python-packages"></a>Безопасная установка внешних пакетов Python

Кластер HDInsight зависит от встроенной среды Python, как Python 2.7, так и Python 3.5. Непосредственное установка пользовательских пакетов в встроенных средах по умолчанию может привести к неожиданным изменениям версии библиотеки и дальнейшему разрыву кластера. Для безопасной установки пользовательских внешних пакетов Python для ваших приложений Spark выполните ниже шаги.

1. Создавайте виртуальную среду Python с помощью conda. Виртуальная среда обеспечивает изолированное пространство для ваших проектов, не нарушая других. При создании виртуальной среды Python можно указать версию Python, которую вы хотите использовать. Обратите внимание, что вам все еще нужно создать виртуальную среду, даже если вы хотите использовать Python 2.7 и 3.5. Это необходимо для того, чтобы среда по умолчанию не сломалась. Запуск действий скрипта в кластере для всех узлов с нижескриптом для создания виртуальной среды Python.

    -   `--prefix`определяет путь, где живет конда виртуальной среды. Есть несколько конфигураций, которые должны быть изменены далее в зависимости от пути, указанного здесь. В этом примере мы используем py35new, так как кластер уже имеет существующую виртуальную среду под названием py35.
    -   `python=`определяет версию Python для виртуальной среды. В этом примере мы используем версию 3.5, ту же версию, что и кластер, встроенный в одну. Вы также можете использовать другие версии Python для создания виртуальной среды.
    -   `anaconda`указывает package_spec как анаконду для установки пакетов Anaconda в виртуальной среде.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. При необходимости установите внешние пакеты Python в созданную виртуальную среду. Запуск действий скрипта на кластере для всех узлов с нижескриптом для установки внешних пакетов Python. Вы должны иметь sudo привилегии здесь для того, чтобы написать файлы в папку виртуальной среды.

    Полный список доступных пакетов можно найти в [указателе пакетов](https://pypi.python.org/pypi). Его также можно получить из других источников. Например, можно установить пакеты, предоставляемые посредством [conda-forge](https://conda-forge.org/feedstocks/).

    Используйте ниже команды, если вы хотите установить библиотеку с ее последней версией:

    - Используйте канал conda:

        -   `seaborn`это имя пакета, которое вы хотели бы установить.
        -   `-n py35new`указать только созданное имя виртуальной среды. Убедитесь в том, чтобы изменить имя соответственно на основе создания виртуальной среды.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - Или используйте Пипи `seaborn` репо, изменить и `py35new` соответственно:
        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install seaborn
        ```

    Используйте ниже команды, если вы хотите установить библиотеку с конкретной версией:

    - Используйте канал conda:

        -   `numpy=1.16.1`это название пакета и версия, которую вы хотели бы установить.
        -   `-n py35new`указать только созданное имя виртуальной среды. Убедитесь в том, чтобы изменить имя соответственно на основе создания виртуальной среды.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - Или используйте Пипи `numpy==1.16.1` репо, изменить и `py35new` соответственно:

        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install numpy==1.16.1
        ```

    если вы не знаете имя виртуальной среды, вы можете SSH в `/usr/bin/anaconda/bin/conda info -e` головной узлы кластера и запустить, чтобы показать все виртуальные среды.

3. Измените Spark и Livy конфигурации и укажите на созданную виртуальную среду.

    1. Откройте ui Ambari, перейдите на страницу Spark2, вкладка Configs.

        ![Изменение Spark и Livy конфигурации через Амбари](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)

    2. Расширьте Advanced livy2-env, добавьте ниже инструкции внизу. Если вы установили виртуальную среду с другой приставкой, измените путь соответственно.

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Изменение Ливи конфигурации через Амбари](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Расширьте Advanced spark2-env, замените существующую экспортную PYSPARK_PYTHON заявление внизу. Если вы установили виртуальную среду с другой приставкой, измените путь соответственно.

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Изменение Spark конфигурации через Амбари](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Сохранить изменения и перезапустить затронутые службы. Эти изменения требуют перезагрузки службы Spark2. Ambari UI подскажет требуемое напоминание о перезагрузке, нажмите Restart, чтобы перезапустить все затронутые службы.

        ![Изменение Spark конфигурации через Амбари](./media/apache-spark-python-package-installation/ambari-restart-services.png)

4. Если вы хотите использовать новую виртуальную среду на Jupyter. Вам нужно изменить Jupyter конфигураций и перезапустить Jupyter. Выполнить действия скрипта на всех узлах заголовка с ниже оператора, чтобы указать Jupyter на новую созданную виртуальную среду. Убедитесь в том, чтобы изменить путь к префиксу, указанной для вашей виртуальной среды. После запуска этого действия скрипта перезапустите службу Jupyter через uI Ambari, чтобы сделать это изменение доступным.

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    Вы можете удвоить подтверждение среды Python в ноутбуке Jupyter, запустив под кодом:

    ![Проверить версию Python в ноутбуке Jupyter](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Известная проблема

Существует известная ошибка для версии Anaconda 4.7.11, 4.7.12 и 4.8.0. Если вы видите ваши `"Collecting package metadata (repodata.json): ...working..."` действия `"Python script has been killed due to timeout after waiting 3600 secs"`сценария висит на и не с . Вы можете загрузить [этот скрипт](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) и запустить его как действия скрипта на всех узлах, чтобы исправить проблему.

Чтобы проверить версию Anaconda, вы можете SSH к `/usr/bin/anaconda/bin/conda --v`узлю заголовка кластера и запустить.

## <a name="next-steps"></a>Дальнейшие действия

* [Обзор: Apache Spark в Azure HDInsight](apache-spark-overview.md)
* [Использование Apache Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](apache-spark-use-bi-tools.md)
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](apache-spark-job-debugging.md)
