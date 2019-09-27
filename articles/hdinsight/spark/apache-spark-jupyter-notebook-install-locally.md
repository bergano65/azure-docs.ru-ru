---
title: Установка записной книжки Jupyter в локальной среде и ее подключение к Spark в Azure HDInsight
description: Сведения о том, как установить записную книжку Jupyter на компьютере локально и как подключить ее к кластеру Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/06/2019
ms.openlocfilehash: 46164cfc0c2baff919808a831a67180b65a23ff7
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71337654"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Установка записной книжки Jupyter на компьютере и ее подключение к Apache Spark в HDInsight

Из этой статьи вы узнаете, как установить записную книжку Jupyter с пользовательскими ядрами PySpark (для Python) и Apache Spark (для Scala) с помощью magic-команд Spark, а затем подключить эту записную книжку к кластеру HDInsight. Существуют разные причины для установки Jupyter на локальном компьютере, но есть и некоторые трудности. Дополнительные сведения см. в разделе [Зачем устанавливать Jupyter на компьютер?](#why-should-i-install-jupyter-on-my-computer) в конце этой статьи.

При установке Jupyter и подключении к Apache Spark в HDInsight необходимо выполнить четыре основных шага.

* Настройка кластера Spark.
* Установите записную книжку Jupyter.
* Установите ядра PySpark и Spark с помощью волшебной команды Spark.
* Настройте волшебную команду Spark для доступа к кластеру Spark в HDInsight.

Дополнительные сведения о пользовательских ядрах и волшебных командах Spark, доступных для записных книжек Jupyter в кластере HDInsight, см. в статье [Ядра, доступные для использования записными книжками Jupyter с кластерами Apache Spark в HDInsight на платформе Linux](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Предварительные требования

Указанные здесь предварительные требования относятся не к установке Jupyter. Они относятся к подключению записной книжки Jupyter к кластеру HDInsight после установки записной книжки.

* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Установка записной книжки Jupyter на компьютер

Перед установкой записных книжек Jupyter необходимо установить Python. В [дистрибутиве Anaconda](https://www.anaconda.com/download/) будут установлены оба, Python и Jupyter Notebook.

Скачайте [установщик Anaconda](https://www.anaconda.com/download/) для своей платформы и запустите программу установки. В мастере установки укажите параметр для добавления Anaconda в переменную PATH.  См. также [Установка Jupyter с помощью Anaconda](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>Установка программы Spark Magic

1. Введите одну из приведенных ниже команд, чтобы установить магическую версию Spark. См. также [документацию по sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation).

    |Версия кластера | Команда установки |
    |---|---|
    |v 3.6 и v 3.5 |`pip install sparkmagic==0.12.7`|
    |v 3.4|`pip install sparkmagic==0.2.3`|

1. Убедитесь `ipywidgets` , что установлен правильный параметр, выполнив следующую команду:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>Установка ядер PySpark и Spark

1. Найдите место `sparkmagic` установки, введя следующую команду:

    ```cmd
    pip show sparkmagic
    ```

    Затем измените рабочий каталог на расположение, указанное в приведенной выше команде.

1. В новом рабочем каталоге введите одну или несколько приведенных ниже команд, чтобы установить нужные ядра.

    |Kernel (Ядро) | Command |
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

В этом разделе вы настроите магическое значение Spark, установленное ранее, для подключения к кластеру Apache Spark.

1. Запустите оболочку Python с помощью следующей команды:

    ```cmd
    python
    ```

2. Сведения о конфигурации Jupyter обычно хранятся в домашнем каталоге пользователей. Введите следующую команду, чтобы указать домашний каталог, и создайте папку с именем **. sparkmagic**.  Будет выведен полный путь.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. В папке `.sparkmagic`создайте файл с именем **config. JSON** и добавьте в него следующий фрагмент JSON.  

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
    |ИМЕН|Имя входа кластера, по `admin`умолчанию —.|
    |CLUSTERDNSNAME|Имя кластера|
    |{BASE64ENCODEDPASSWORD}|Пароль в кодировке Base64 для фактического пароля.  Пароль для base64 можно создать по адресу [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/).|
    |`"livy_server_heartbeat_timeout_seconds": 60`|При использовании `sparkmagic 0.12.7` (кластеры версии 3.5 и 3.6) не заключайте.  При использовании `sparkmagic 0.2.3` (Clusters v 3.4) Замените на `"should_heartbeat": true`.|

    Полный пример файла см. в файле [Sample config. JSON](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > Сигналы пульса отправляются, чтобы предотвратить утечку сеансов. При переходе в спящий режим или завершении работы компьютера пульс не отправляется, что приводит к очистке сеанса. Если вы хотите отключить такое поведение для кластеров версии 3.4, то можете настроить для параметра Livy `livy.server.interactive.heartbeat.timeout` значение `0` с помощью пользовательского интерфейса Ambari. Если для кластеров версии 3.5 не настроить соответствующую конфигурацию, приведенную выше, то сеанс не будет удален.

5. Запустите Jupyter. Выполните следующую команду из командной строки.

    ```cmd
    jupyter notebook
    ```

6. Убедитесь, что вы можете использовать магическую платформу Spark, доступную в ядрах. Выполните следующие действия.

    1\. Создайте новую записную книжку. В правом углу выберите **создать**. Вы должны увидеть ядро по умолчанию **Python 2** или **Python 3** и установленные ядра. Фактические значения могут различаться в зависимости от выбранных вариантов установки.  Выберите **PySpark**.

    ![Доступные ядра в ядре записной книжки Jupyter](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "в записной книжке Jupyter")

    > [!IMPORTANT]  
    > После выбора **нового** проверьте оболочку на наличие ошибок.  Если отображается сообщение об ошибке `TypeError: __init__() got an unexpected keyword argument 'io_loop'` , возможно, возникла известная проблема с определенными версиями Торнадо.  Если это так, завершите работу ядра, а затем понизить установку торнадо, выполнив `pip install tornado==4.5.3`следующую команду:.

    2\. Запустите следующий фрагмент кода.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Если вы успешно получили выходные данные, подключение к кластеру HDInsight работает.

    Если вы хотите обновить конфигурацию записной книжки для подключения к другому кластеру, обновите файл config. JSON, используя новый набор значений, как показано на шаге 3 выше.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Зачем устанавливать Jupyter на моем компьютере?

Может быть несколько причин, по которым вам потребуется установить на компьютер Jupyter и подключить к кластеру Apache Spark в HDInsight.

* Хотя записные книжки Jupyter уже доступны в кластере Spark в Azure HDInsight, после их установки на компьютер вы сможете создавать записные книжки локально, тестировать приложения на работающем кластере и отправлять записные книжки в кластер. Для отправки записных книжек в кластер можно отправить их с помощью записной книжки Jupyter, которая запущена на кластере, или сохранить их в папке /HdiNotebooks в учетной записи хранения, связанной с кластером. Дополнительные сведения о хранении записных книжек в кластере см. в разделе [Где хранятся записные книжки](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored).
* С помощью локально доступных записных книжек вы сможете подключиться к различным кластерам Spark в зависимости от потребностей вашего приложения.
* Можно использовать GitHub для реализации системы управления версиями, чтобы контролировать версии записных книжек. Вы также можете создать среду совместной работы, в которой несколько пользователей будут работать с одной записной книжкой.
* Вы можете работать с записными книжками локально даже без кластера. Кластер нужен только для тестирования записных книжек, но не обязателен для ручного управления записными книжками или средой разработки.
* Возможно, вам будет проще настроить локальную среду разработки, чем настраивать установку Jupyter в кластере.  Вы можете спокойно пользоваться любым программным обеспечением, установленным локально, не настраивая удаленные кластеры.

> [!WARNING]  
> Если Jupyter установлен на локальном компьютере, несколько пользователей могут одновременно запустить одну и ту же записную книжку в одном кластере Spark. В такой ситуации создаются несколько сеансов Livy. Если вы столкнетесь с проблемами и начнете их отладку, вам будет сложно определить, какой сеанс Livy какому пользователю принадлежит.  

## <a name="next-steps"></a>Следующие шаги

* [Apache Spark в Azure HDInsight](apache-spark-overview.md)
* [Руководство. Анализ данных Apache Spark с использованием Power BI в HDInsight](apache-spark-use-bi-tools.md)
* [Использование Apache Spark MLlib для Создание приложения машинного обучения Apache Spark в HDInsight](apache-spark-ipython-notebook-machine-learning.md)
