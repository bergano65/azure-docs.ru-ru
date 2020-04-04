---
title: Установка записной книжки Jupyter в локальной среде и ее подключение к Spark в Azure HDInsight
description: Сведения о том, как установить записную книжку Jupyter на компьютере локально и как подключить ее к кластеру Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/02/2020
ms.openlocfilehash: 1d044ddaea0a2c7a1d489523cc9aa4515df0728a
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632659"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Установка записной книжки Jupyter на компьютере и ее подключение к Apache Spark в HDInsight

В этой статье вы узнаете, как установить ноутбук Jupyter с пользовательскими PySpark (для Python) и Apache Spark (для Scala) ядер с магией Spark. Затем ноутбук подключается к кластеру HDInsight.

Есть четыре ключевых шага, связанных с установкой Jupyter и подключение к Apache Spark на HDInsight.

* Настройка кластера Spark.
* Установите записную книжку Jupyter.
* Установите ядра PySpark и Spark с помощью волшебной команды Spark.
* Настройте волшебную команду Spark для доступа к кластеру Spark в HDInsight.

Для получения дополнительной информации о пользовательских ядер и Spark магии, [см. Ядра доступны для ноутбуков Jupyter с Apache Spark Linux кластеров на HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Предварительные требования

* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md). Локальный блокнот подключается к кластеру HDInsight.

* Опыт работы с записными книжками Jupyter с Spark в HDInsight.

## <a name="install-jupyter-notebook-on-your-computer"></a>Установка записной книжки Jupyter на компьютер

Установите Python перед установкой ноутбуков Jupyter. В [дистрибутиве Anaconda](https://www.anaconda.com/download/) будут установлены как Python, так и Jupyter Notebook.

Скачайте [установщик Anaconda](https://www.anaconda.com/download/) для своей платформы и запустите программу установки. В мастере установки укажите параметр для добавления Anaconda в переменную PATH.  Смотрите также, [Установка Jupyter с помощью Anaconda](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>Установка spark магии

1. Введите одну из команд ниже, чтобы установить spark магии. Смотрите также, [искривая документация](https://github.com/jupyter-incubator/sparkmagic#installation).

    |Cluster version | Установка команды |
    |---|---|
    |v3.6 и v3.5 |`pip install sparkmagic==0.13.1`|
    |v3.4|`pip install sparkmagic==0.2.3`|

1. Убедитесь, `ipywidgets` что он установлен должным образом, запустив следующую команду:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>Установка ядер PySpark и Spark

1. Определите, где `sparkmagic` устанавливается, введя следующую команду:

    ```cmd
    pip show sparkmagic
    ```

    Затем измените рабочий каталог на **место,** идентифицированное с приведенной выше командой.

1. Из нового рабочего каталога введите одну или несколько команд ниже, чтобы установить нужное ядро (ы):

    |Ядро | Get-Help |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. Необязательный параметр. Введите команду ниже, чтобы включить расширение сервера:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Настройка волшебной команды Spark для подключения к кластеру HDInsight Spark

В этом разделе вы настраиваете магию Spark, которую вы установили ранее для подключения к кластеру Apache Spark.

1. Запустите оболочку Python со следующей командой:

    ```cmd
    python
    ```

2. Сведения о конфигурации Jupyter обычно хранятся в домашнем каталоге пользователей. Введите следующую команду, чтобы определить домашний каталог, и создать папку под названием ** \.sparkmagic.**  Полный путь будет пробит.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. В папке `.sparkmagic`создайте файл под названием **config.json** и добавьте следующий фрагмент JSON внутри него.  

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

      "custom_headers" : {
        "X-Requested-By": "livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. Внести следующие вечения в файл:

    |Значение шаблона | Новое значение |
    |---|---|
    |«ИМЯ ПОЛЬЗОВАТЕЛЯ»|Кластер войти, по `admin`умолчанию .|
    |(КЛАСТЕРДННАМА)|Имя кластера|
    |(BASE64ENCODEDPASSWORD)|Базовый 64 закодированный пароль для вашего фактического пароля.  Вы можете создать пароль base64 на [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/).|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Храните `sparkmagic 0.12.7` при использовании (кластеры v3.5 и v3.6).  При `sparkmagic 0.2.3` использовании (кластеры v3.4) замените . `"should_heartbeat": true`|

    Вы можете увидеть полный пример файла на [примере config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > Сигналы пульса отправляются, чтобы предотвратить утечку сеансов. При переходе в спящий режим или завершении работы компьютера пульс не отправляется, что приводит к очистке сеанса. Если вы хотите отключить такое поведение для кластеров версии 3.4, то можете настроить для параметра Livy `livy.server.interactive.heartbeat.timeout` значение `0` с помощью пользовательского интерфейса Ambari. Если для кластеров версии 3.5 не настроить соответствующую конфигурацию, приведенную выше, то сеанс не будет удален.

5. Запустите Jupyter. Выполните следующую команду из командной строки.

    ```cmd
    jupyter notebook
    ```

6. Убедитесь, что вы можете использовать магию Spark, доступную с ядрами. Выполните следующие шаги.

    а. Создайте новую записную книжку. Из правого угла выберите **Новый**. Вы должны увидеть ядро по умолчанию **Python 2** или **Python 3** и установленные ядра. Фактические значения могут варьироваться в зависимости от выбора установки.  Выберите **PySpark**.

    ![Доступные ядра в блокноте Jupyter](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "Ядра в блокноте Jupyter")

    > [!IMPORTANT]  
    > После выбора **Новой** просмотрите свою оболочку на все ошибки.  Если вы видите ошибку, `TypeError: __init__() got an unexpected keyword argument 'io_loop'` вы можете испытывать известные проблемы с определенными версиями Торнадо.  Если это так, остановите ядро, а затем понизить установку Торнадо со следующей командой: `pip install tornado==4.5.3`.

    b. Запустите следующий фрагмент кода.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Если вы успешно получили выходные данные, подключение к кластеру HDInsight работает.

    Если требуется обновить конфигурацию ноутбука для подключения к другому кластеру, обновите конфигурацию config.json с новым набором значений, как показано в шаге 3, выше.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Зачем устанавливать Jupyter на моем компьютере?

Причины установки Jupyter на вашем компьютере, а затем подключить его к кластеру Apache Spark на HDInsight:

* Предоставляет возможность локально создавать блокноты, тестировать приложение на уровне запущенного кластера, а затем загружать блокноты в кластер. Чтобы загрузить блокноты в кластер, вы можете загрузить их с помощью работающего ноутбука `/HdiNotebooks` Jupyter или кластера, либо сохранить их в папку в учетной записи хранения, связанной с кластером. Дополнительные сведения о хранении записных книжек в кластере см. в разделе [Где хранятся записные книжки](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored).
* С помощью локально доступных записных книжек вы сможете подключиться к различным кластерам Spark в зависимости от потребностей вашего приложения.
* Можно использовать GitHub для реализации системы управления версиями, чтобы контролировать версии записных книжек. Вы также можете создать среду совместной работы, в которой несколько пользователей будут работать с одной записной книжкой.
* Вы можете работать с записными книжками локально даже без кластера. Кластер нужен только для тестирования записных книжек, но не обязателен для ручного управления записными книжками или средой разработки.
* Возможно, проще настроить собственную локальную среду разработки, чем настроить установку Jupyter на кластере.  Вы можете воспользоваться всем программным обеспечением, установленным локально, не настраивая один или несколько удаленных кластеров.

> [!WARNING]  
> Если Jupyter установлен на локальном компьютере, несколько пользователей могут одновременно запустить одну и ту же записную книжку в одном кластере Spark. В такой ситуации создаются несколько сеансов Livy. Если вы столкнетесь с проблемами и начнете их отладку, вам будет сложно определить, какой сеанс Livy какому пользователю принадлежит.  

## <a name="next-steps"></a>Дальнейшие действия

* [Обзор: Apache Spark в Azure HDInsight](apache-spark-overview.md)
* [Apache Spark с BI: Анализ данных Apache Spark с помощью Power BI в HDInsight](apache-spark-use-bi-tools.md)
* [Apache Spark и Машинное обучение. Анализ температуры в здании на основе данных системы кондиционирования с помощью Spark в HDInsight](apache-spark-ipython-notebook-machine-learning.md)
