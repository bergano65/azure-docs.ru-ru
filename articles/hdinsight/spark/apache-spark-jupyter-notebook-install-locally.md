---
title: Установка записной книжки Jupyter в локальной среде и ее подключение к Spark в Azure HDInsight
description: Сведения о том, как установить записную книжку Jupyter на компьютере локально и как подключить ее к кластеру Apache Spark.
ms.service: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: hrasheed
ms.openlocfilehash: 489685485af4e3c8868f7e0281d2f81464a166f6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066174"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Установка записной книжки Jupyter на компьютере и ее подключение к Apache Spark в HDInsight

Из этой статьи вы узнаете, как установить записную книжку Jupyter с пользовательскими ядрами PySpark (для Python) и Apache Spark (для Scala) с помощью magic-команд Spark, а затем подключить эту записную книжку к кластеру HDInsight. Существуют разные причины для установки Jupyter на локальном компьютере, но есть и некоторые трудности. Дополнительные сведения см. в разделе [Зачем устанавливать Jupyter на компьютер?](#why-should-i-install-jupyter-on-my-computer) в конце этой статьи.

Существует четыре основные шаги, участвующих в установке Jupyter и подключению к Apache Spark в HDInsight.

* Настройка кластера Spark.
* Установите записную книжку Jupyter.
* Установите ядра PySpark и Spark с помощью волшебной команды Spark.
* Настройте волшебную команду Spark для доступа к кластеру Spark в HDInsight.

Дополнительные сведения о пользовательских ядрах и волшебных командах Spark, доступных для записных книжек Jupyter в кластере HDInsight, см. в статье [Ядра, доступные для использования записными книжками Jupyter с кластерами Apache Spark в HDInsight на платформе Linux](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Технические условия

Указанные здесь предварительные требования относятся не к установке Jupyter. Они относятся к подключению записной книжки Jupyter к кластеру HDInsight после установки записной книжки.

* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Установка записной книжки Jupyter на компьютер

Перед установкой записных книжек Jupyter необходимо установить Python. [Дистрибутив Anaconda](https://www.anaconda.com/download/) установит, Python и записной книжки Jupyter.

Скачайте [установщик Anaconda](https://www.anaconda.com/download/) для своей платформы и запустите программу установки. В мастере установки укажите параметр для добавления Anaconda в переменную PATH.  Кроме того, см. в разделе [здесь](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>Установка волшебной команды Spark

1. Введите одно из приведенных ниже команд, чтобы установить волшебной команды Spark. Кроме того, см. в разделе [документации по sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation).

    |Cluster version | Команда установки |
    |---|---|
    |V3.6 и версии 3.5 |`pip install sparkmagic==0.12.7`|
    |версии 3.4|`pip install sparkmagic==0.2.3`|

1. Убедитесь, `ipywidgets` установлена правильно, выполнив следующую команду:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>Установите ядра PySpark и Spark

1. Указать расположение `sparkmagic` устанавливается с помощью следующей команды:

    ```cmd
    pip show sparkmagic
    ```

    В расположении с приведенной выше команде измените рабочий каталог.

1. Из нового рабочего каталога введите один или несколько из приведенных ниже команд, чтобы установить нужные kernel(s):

    |Kernel (Ядро) | Команда |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. Необязательный элемент. Введите следующую команду, чтобы включить расширение сервера:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Настройка волшебной команды Spark для подключения к кластеру HDInsight Spark

В этом разделе вы настроите волшебной команды Spark, установленную ранее для подключения к кластеру Apache Spark.

1. Запустите оболочку Python с помощью следующей команды:

    ```cmd
    python
    ```

2. Сведения о конфигурации Jupyter обычно хранятся в домашнем каталоге пользователей. Введите следующую команду, чтобы определить домашний каталог и создайте папку с именем **.sparkmagic**.  Выводятся полный путь.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. В папке `.sparkmagic`, создайте файл с именем **config.json** и добавьте следующий фрагмент JSON внутри него.  

    ```json
    {
      "kernel_python_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "kernel_scala_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. Внесите следующие изменения в файл:

    |Значение шаблона | Новое значение |
    |---|---|
    |{USERNAME}|Входа в кластер, значение по умолчанию — `admin`.|
    |{CLUSTERDNSNAME}|Имя кластера|
    |{BASE64ENCODEDPASSWORD}|Пароль для фактический пароль в кодировке base64.  Вы можете создать пароль base64 в [ https://www.url-encode-decode.com/base64-encode-decode/ ](https://www.url-encode-decode.com/base64-encode-decode/).|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Если с помощью `sparkmagic 0.12.7` (кластеры версии 3.5 и 3.6).  При использовании `sparkmagic 0.2.3` (кластеры версии 3.4), замените `"should_heartbeat": true`.|

    Вы увидите в полном примере файл [примере файла config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > Сигналы пульса отправляются, чтобы предотвратить утечку сеансов. При переходе в спящий режим или завершении работы компьютера пульс не отправляется, что приводит к очистке сеанса. Если вы хотите отключить такое поведение для кластеров версии 3.4, то можете настроить для параметра Livy `livy.server.interactive.heartbeat.timeout` значение `0` с помощью пользовательского интерфейса Ambari. Если для кластеров версии 3.5 не настроить соответствующую конфигурацию, приведенную выше, то сеанс не будет удален.

5. Запустите Jupyter. Выполните следующую команду из командной строки.

    ```cmd
    jupyter notebook
    ```

6. Убедитесь, что можно использовать волшебную команду Spark с ядрами. Выполните следующие действия.

    a. Создайте новую записную книжку. В правом углу выберите **New**. Вы должны увидеть ядро по умолчанию **Python 2** или **Python 3** и ядра, вы установили. Фактические значения могут меняться в зависимости от вашей установки.  Выберите **PySpark**.

    ![Ядра в записной книжке Jupyter](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Ядра в записной книжке Jupyter")

    > [!IMPORTANT]  
    > После выбора **New** просмотрите оболочке наличие ошибок.  Если появится сообщение об ошибке `TypeError: __init__() got an unexpected keyword argument 'io_loop'` может возникнуть известная проблема с определенными версиями торнадо.  Если это так, остановите ядра и понизьте торнадо установку с помощью следующей команды: `pip install tornado==4.5.3`.

    2\. Запустите следующий фрагмент кода.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Если вы успешно получили выходные данные, подключение к кластеру HDInsight работает.

    Если вы хотите обновить конфигурацию записной книжки для подключения к другому кластеру, измените файл config.json новым набором значений, как показано на шаге 3 выше.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Зачем устанавливать Jupyter на моем компьютере?

Может быть несколько причин, по которым вам потребуется установить на компьютер Jupyter и подключить к кластеру Apache Spark в HDInsight.

* Хотя записные книжки Jupyter уже доступны в кластере Spark в Azure HDInsight, после их установки на компьютер вы сможете создавать записные книжки локально, тестировать приложения на работающем кластере и отправлять записные книжки в кластер. Для отправки записных книжек в кластер можно отправить их с помощью записной книжки Jupyter, которая запущена на кластере, или сохранить их в папке /HdiNotebooks в учетной записи хранения, связанной с кластером. Дополнительные сведения о хранении записных книжек в кластере см. в разделе [Где хранятся записные книжки](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored).
* С помощью локально доступных записных книжек вы сможете подключиться к различным кластерам Spark в зависимости от потребностей вашего приложения.
* Можно использовать GitHub для реализации системы управления версиями, чтобы контролировать версии записных книжек. Вы также можете создать среду совместной работы, в которой несколько пользователей будут работать с одной записной книжкой.
* Вы можете работать с записными книжками локально даже без кластера. Кластер нужен только для тестирования записных книжек, но не обязателен для ручного управления записными книжками или средой разработки.
* Возможно, вам будет проще настроить локальную среду разработки, чем настраивать установку Jupyter в кластере.  Вы можете спокойно пользоваться любым программным обеспечением, установленным локально, не настраивая удаленные кластеры.

> [!WARNING]  
> Если Jupyter установлен на локальном компьютере, несколько пользователей могут одновременно запустить одну и ту же записную книжку в одном кластере Spark. В такой ситуации создаются несколько сеансов Livy. Если вы столкнетесь с проблемами и начнете их отладку, вам будет сложно определить, какой сеанс Livy какому пользователю принадлежит.  

## <a name="next-steps"></a>Дальнейшие действия

* [Apache Spark в Azure HDInsight](apache-spark-overview.md)
* [Руководство. Анализ данных Apache Spark с использованием Power BI в HDInsight](apache-spark-use-bi-tools.md)
* [Использование Apache Spark MLlib для Создание приложения машинного обучения Apache Spark в HDInsight](apache-spark-ipython-notebook-machine-learning.md)