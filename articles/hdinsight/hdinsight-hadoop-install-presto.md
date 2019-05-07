---
title: Установка Presto в кластерах Azure HDInsight на основе Linux
description: Узнайте, как устанавливать Presto и Airpal в кластерах HDInsight Hadoop на основе Linux с помощью действий сценария.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: hrasheed
ms.openlocfilehash: 7ce2cf40dafc09d86d0c424a3954a334ae83143a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145481"
---
# <a name="install-and-use-presto-on-hadoop-based-hdinsight-clusters"></a>Установка и использование Presto в кластерах HDInsight Hadoop

> [!IMPORTANT]  
> СМ. В СТАТЬЕ: Ниже инструкции являются устаревшими. См. последние рекомендации от [звезда данных](http://docs.starburstdata.com/latest/azure.html) для установки и запуска Presto на Azure HDInsight.  

В этой статье объясняется, как устанавливать Presto в кластерах HDInsight Hadoop под управлением Azure с помощью действий сценария. Вы также узнаете, как устанавливать Airpal в имеющемся кластере Presto HDInsight.

В HDInsight также предлагается приложение Starburst Presto для кластеров Apache Hadoop. Дополнительные сведения см. в статье [Установка сторонних приложений Apache Hadoop в Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-install-applications).

> [!IMPORTANT]  
> Для выполнения действий, описанных в этой статье, необходим кластер Hadoop для HDInsight 3.5 под управлением Linux. Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в статье [Что представляют собой различные компоненты и версии Hadoop, доступные в HDInsight?](hdinsight-component-versioning.md)

## <a name="what-is-presto"></a>Что такое Presto?
[Presto](https://prestosql.io) представляет собой быстрый распределенный модуль SQL-запросов для больших данных. Presto подходит для интерактивных запросов петабайт данных. Дополнительные сведения о компонентах Presto и их совместной работе см. в статье [Presto Concepts](https://prestosql.io/docs/current/overview/concepts.html) (Концепции Presto).

> [!WARNING]  
> Компоненты, поставляемые с кластером HDInsight, полностью поддерживаются. Служба поддержки Майкрософт помогает выявлять и устранять проблемы, связанные с этими компонентами.
> 
> Настраиваемые компоненты, такие как Presto, получают ограниченную коммерчески оправданную поддержку, способствующую дальнейшей диагностике проблемы. Эта поддержка может помочь решить проблему. Вас могут попросить воспользоваться доступными каналами по технологиям с открытым кодом, чтобы связаться с экспертами в данной области. Вы можете использовать сайты сообществ. Например, [форум MSDN по HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) и [Stack Overflow](https://stackoverflow.com). 
>
> Для проектов Apache также имеются соответствующие сайты, указанные на [веб-сайте Apache](https://apache.org). Например, [Hadoop](https://hadoop.apache.org/).


## <a name="install-presto-by-using-script-actions"></a>Установка Presto с помощью действия сценария

В этом разделе описывается использование примера сценария при создании нового кластера с помощью портала Azure. 

1. Начните подготовку кластера, выполнив действия, описанные в статье [Создание кластеров под управлением Linux в HDInsight с помощью портала Azure](hdinsight-hadoop-create-linux-clusters-portal.md). Убедитесь, что создаете кластер с помощью процесса создания **пользовательского** кластера. Кластер должен соответствовать следующим требованиям.

   * Это должен быть кластер Hadoop в HDInsight версии 3.6.

   * Он должен использовать хранилище Azure в качестве хранилища данных. Использование Presto в кластере с Azure Data Lake Storage в качестве хранилища пока невозможно.

     ![HDInsight, параметр "Настраиваемое (размер, параметры, приложения)"](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. В области **Дополнительные параметры** щелкните **Действия скрипта**. Введите следующие данные. Вы также можете выбрать вариант **Установить Presto** для типа сценария.
   
   * **Имя**. введите понятное имя для действия сценария.
   * **URI bash-скрипта**. `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`.
   * **Головной узел.** Выберите этот параметр.
   * **Рабочая роль.** Выберите этот параметр.
   * **ZooKeeper.** Не устанавливайте этот флажок.
   * **Параметры.** оставьте это поле пустым.


3. Внизу области **Действия скрипта** нажмите кнопку **Выбрать**, чтобы сохранить конфигурацию. Наконец, нажмите кнопку **Выбрать** внизу области **Дополнительные параметры**, чтобы сохранить конфигурацию.

4. Продолжите подготовку кластера, как описано в статье [Создание кластеров под управлением Linux в HDInsight с помощью портала Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

    > [!NOTE]  
    > Для выполнения действий этого сценария также можно использовать Azure PowerShell, классический интерфейс командной строки Azure, пакет SDK .NET для HDInsight или шаблоны Azure Resource Manager. Действия сценария также можно применять к уже работающим кластерам. Дополнительные сведения см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действий сценариев](hdinsight-hadoop-customize-cluster-linux.md).
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>Использование Presto с HDInsight

Для работы с Presto в кластере HDInsight выполните следующие действия.

1. Подключитесь к кластеру HDInsight с помощью протокола SSH:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Дополнительные сведения см. в руководстве по [подключению к HDInsight (Apache Hadoop) с помощью SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
     

2. Запустите оболочку Presto, выполнив следующую команду.
   
    `presto --schema default`

3. Выполните запрос в примере таблицы **hivesampletable**, которая доступна во всех кластерах HDInsight по умолчанию.
   
    `select count (*) from hivesampletable;`
   
    По умолчанию соединители [Apache Hive](https://prestosql.io/docs/current/connector/hive.html) и [TPCH](https://prestosql.io/docs/current/connector/tpch.html) для Presto уже настроены. Соединитель Hive настроен на использование установки Hive по умолчанию. Таким образом, все таблицы из Hive автоматически отображаются в Presto.

    Дополнительные сведения см. в [документации по Presto](https://prestosql.io/docs/current/index.html).

## <a name="use-airpal-with-presto"></a>Использование Airpal с Presto

[Airpal](https://github.com/airbnb/airpal#airpal) является веб-интерфейсом запросов с открытым кодом для Presto. Дополнительные сведения об Airpal см. в [документации по Airpal](https://github.com/airbnb/airpal#airpal).

Чтобы установить Airpal на граничном узле, сделайте следующее:

1. Подключитесь по протоколу SSH к головному узлу кластера HDInsight, в котором уже установлен Presto:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Дополнительные сведения см. в руководстве по [подключению к HDInsight (Apache Hadoop) с помощью SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

2. После подключения выполните следующую команду:

    `sudo slider registry  --name presto1 --getexp presto` 
   
    Должен появиться результат, аналогичный приведенному ниже коду JSON.

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. В выходных данных запомните значение свойства **value**. Это значение потребуется при установке Airpal на граничном узле кластера. Из предыдущих выходных данных вам необходимо значение **10.0.0.12:9090**.

4. Используйте [этот шаблон](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json), чтобы создать граничный узел кластера HDInsight. Укажите значения, показанные на следующем снимке экрана.

    ![Настраиваемое развертывание](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. Щелкните **Приобрести**.

6. После применения изменений к конфигурации кластера можно получить доступ к веб-интерфейсу Airpal, выполнив следующие действия на [портале Azure](https://portal.azure.com):

    1. В меню слева выберите **Все службы**.

    1. В разделе **АНАЛИТИКА** выберите **Кластеры HDInsight**.

    1. Выберите свой кластер в списке. Откроется представление по умолчанию.

    1. В представлении по умолчанию в разделе **Параметры** выберите **Приложения**.

        ![HDInsight, приложения](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    1. На странице **Установленные приложения** найдите запись таблицы **airpal**. Выберите **Портал**.

        ![Установленные приложения](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    1. При появлении запроса введите учетные данные администратора, указанные при создании кластера HDInsight на основе Hadoop.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>Настройка установки Presto в кластере HDInsight

Для настройки процесса установки выполните следующие действия:

1. Подключитесь по протоколу SSH к головному узлу кластера HDInsight, в котором уже установлен Presto:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Дополнительные сведения см. в руководстве по [подключению к HDInsight (Apache Hadoop) с помощью SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Внесите изменения конфигурации в файл `/var/lib/presto/presto-hdinsight-master/appConfig-default.json`. Дополнительные сведения о конфигурации Presto см. в статье [Presto Configuration Options for YARN-Based Clusters](https://prestosql.github.io/presto-yarn/installation-yarn-configuration-options.html) (Конфигурация параметров Presto для кластеров на базе YARN).

3. Остановите и отмените текущий выполняемый экземпляр Presto.

    `sudo slider stop presto1 --force` `sudo slider destroy presto1 --force`

4. Запустите новый экземпляр Presto с настройками:

    `sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json`

5. Дождитесь, пока новый экземпляр не станет готов. Обратите внимание на адрес координатора Presto.

    `sudo slider registry --name presto1 --getexp presto`

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Создание тестовых данных для кластеров HDInsight под управлением Presto

TPC-DS — это отраслевой стандарт для измерения производительности многих систем поддержки принятия решений, включая системы больших данных. Presto можно использовать, чтобы генерировать данные и сравнивать их с собственными данными тестирования HDInsight. Дополнительные сведения см. в статье [tpcds-hdinsight](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md).



## <a name="next-steps"></a>Дальнейшие действия
* [Установка и использование Hue на кластерах HDInsight Hadoop](hdinsight-hadoop-hue-linux.md). Hue является веб-интерфейсом, позволяющим легко создавать, выполнять и сохранять задания Apache Pig и Hive.

* [Установка Apache Giraph в кластерах HDInsight Hadoop и использование Giraph для обработки диаграмм больших объемов](hdinsight-hadoop-giraph-install-linux.md). Используйте настройки кластера для установки Giraph в кластерах HDInsight на основе Hadoop. В Giraph можно выполнять обработку графов с помощью Hadoop. Giraph также можно использовать с Azure HDInsight.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
