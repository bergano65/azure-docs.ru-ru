---
title: Действие скрипта для пакетов Python с Jupyter в Azure HDInsight
description: Пошаговые инструкции по использованию действия скрипта для настройки записных книжек Jupyter с кластерами Spark HDInsight для использования внешних пакетов Python.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: f80486758152c002762bbddd6ae97a2ce9468ccf
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241517"
---
# <a name="script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-on-hdinsight"></a>Действие скрипта для установки внешних пакетов Python для записных книжек Jupyter в Apache Spark в HDInsight

> [!div class="op_single_selector"]
> * [Использование волшебных команд](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Использование действия сценария](apache-spark-python-package-installation.md)

Узнайте, как использовать действия сценария для настройки кластера [Apache Spark](https://spark.apache.org/) в HDInsight для использования внешних, участвующих в сообществе пакетов **Python** , которые не включены в готовый список в кластере.

> [!NOTE]  
> Можно также настроить записную книжку Jupyter с помощью волшебной команды `%%configure`, чтобы использовать внешние пакеты. Дополнительные сведения см. в статье [Использование внешних пакетов с записными книжками Jupyter в кластерах Apache Spark в HDInsight](apache-spark-jupyter-notebook-use-external-packages.md).

Полный список доступных пакетов можно найти в [указателе пакетов](https://pypi.python.org/pypi). Его также можно получить из других источников. Например, можно установить пакеты, предоставляемые посредством [conda-forge](https://conda-forge.org/feedstocks/).

В этой статье описано, как установить в кластере пакет [TensorFlow](https://www.tensorflow.org/) с помощью действия скрипта и использовать, например, с помощью записной книжки Jupyter.

## <a name="prerequisites"></a>Технические условия
Необходимо следующее:

* Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > Если у вас еще нет кластера Spark в HDInsight на платформе Linux, можно выполнить действия сценария во время его создания. Обратитесь к документации по [использованию настраиваемых действий сценария](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   
## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Поддержка программного обеспечения с открытым исходным кодом, используемого в кластере HDInsight

Служба Microsoft Azure HDInsight использует сформированную вокруг Apache Hadoop экосистему технологий с открытым кодом. Microsoft Azure предоставляет общий уровень поддержки для технологий с открытым исходным кодом. Дополнительные сведения см. в разделе, посвященном **области действия поддержки**, на [веб-сайте с часто задаваемыми вопросами о поддержке Azure](https://azure.microsoft.com/support/faq/). Служба HDInsight предоставляет дополнительный уровень поддержки для встроенных компонентов.

В службе HDInsight доступно два типа компонентов с открытым исходным кодом.

* **Встроенные компоненты.** Эти компоненты предварительно установлены в кластерах HDInsight и предоставляют его базовые функциональные возможности. Например, к этой категории относится диспетчер ресурсов Apache Hadoop YARN, язык запросов Apache Hive (HiveQL) и библиотека Mahout. Полный список компонентов кластера доступен в статье [Что представляют собой компоненты и версии Apache Hadoop, доступные в HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)
* **Настраиваемые компоненты.** Как пользователь кластера вы можете установить или использовать в рабочей нагрузке любой компонент, полученный из сообщества или созданный самостоятельно.

> [!IMPORTANT]   
> Компоненты, поставляемые с кластером HDInsight, полностью поддерживаются. Служба поддержки Майкрософт помогает выявлять и устранять проблемы, связанные с этими компонентами.
>
> Настраиваемые компоненты получают ограниченную коммерчески оправданную поддержку, способствующую дальнейшей диагностике проблемы. Служба поддержки Майкрософт может устранить проблему ИЛИ вас могут попросить обратиться к специалистам по технологиям с открытым исходным кодом, используя доступные каналы связи. Можно использовать ряд сайтов сообществ, например [форум MSDN по HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) или [https://stackoverflow.com](https://stackoverflow.com). Кроме того, в проектах Apache есть сайты проектов на [https://apache.org](https://apache.org), например [Hadoop](https://hadoop.apache.org/).


## <a name="use-external-packages-with-jupyter-notebooks"></a>Использование внешних пакетов с записными книжками Jupyter

1. В [портал Azure](https://portal.azure.com/)перейдите к кластеру.  

2. Выбрав кластер, в левой области в разделе **Параметры**выберите **действия скрипта**.

3. Выберите **+ отправить новый**.

4. Введите следующие значения в окне **Отправка действия скрипта** :  


    |Параметр | Value |
    |---|---|
    |Тип скрипта | Выберите **— Настраиваемый** из раскрывающегося списка.|
    |Name |Введите `tensorflow` в текстовое поле.|
    |URI bash-скрипта |Введите `https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh` в текстовое поле. |
    |Типы узлов | Установите флажки **головной**и **Рабочий** . |

    `tensorflowinstall.sh` содержит следующие команды:

    ```bash
    #!/usr/bin/env bash
    /usr/bin/anaconda/bin/conda install --yes tensorflow
    ```

5. Нажмите кнопку **Создать**.  Обратитесь к документации по [использованию настраиваемых действий сценария](../hdinsight-hadoop-customize-cluster-linux.md).

6. Дождитесь завершения сценария.  Панель **действия скрипта** будет указывать, что **новые действия скрипта можно отправить после завершения текущей операции кластера** во время выполнения скрипта.  Индикатор выполнения можно просмотреть в окне **фоновые операции** пользовательского интерфейса Ambari.

7. Откройте записную книжку PySpark Jupyter.  Пошаговые инструкции см. в статье [Создание записной книжки Jupyter в Spark HDInsight](./apache-spark-jupyter-notebook-kernels.md#create-a-jupyter-notebook-on-spark-hdinsight) .

    ![Создание записной книжки Jupyter](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Создание новой записной книжки Jupyter")

8. Теперь будет выполнена команда `import tensorflow` и запущен пример hello world. Введите приведенный ниже код.

    ```
    import tensorflow as tf
    hello = tf.constant('Hello, TensorFlow!')
    sess = tf.Session()
    print(sess.run(hello))
    ```

    Результат должен выглядеть следующим образом:
    
    ![Выполнение кода TensorFlow](./media/apache-spark-python-package-installation/tensorflow-execution.png "Выполнение кода TensorFlow")

> [!NOTE]  
> В кластере имеются два установленных компонента Python. Spark будет использовать компонент Python Anaconda, расположенный в `/usr/bin/anaconda/bin`, и среду Python 2.7 по умолчанию. Чтобы использовать Python 3.x и установить пакеты в ядре PySpark3, укажите путь к исполняемому файлу `conda` для этой среды и параметр `-n`, чтобы задать среду. Например, команда `/usr/bin/anaconda/envs/py35/bin/conda install -c conda-forge ggplot -n py35` устанавливает пакет `ggplot` в среде Python 3.5 с использованием канала `conda-forge`.

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
* [Использование внешних пакетов с записными книжками Jupyter в кластерах Apache Spark в HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Apache Spark в HDInsight через VPN с помощью Azure Toolkit for IntelliJ](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование записных книжек Zeppelin с кластером Apache Spark в Azure HDInsight](apache-spark-zeppelin-notebook.md)
* [Ядра для записной книжки Jupyter в кластерах Apache Spark в Azure HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](apache-spark-job-debugging.md)
