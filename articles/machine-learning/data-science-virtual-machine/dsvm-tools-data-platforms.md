---
title: Поддерживаемые платформы данных
titleSuffix: Azure Data Science Virtual Machine
description: Узнайте о поддерживаемых платформах и инструментах для виртуальной машины Azure Data Science.
keywords: средства анализа и обработки данных, виртуальная машина для анализа и обработки данных, средства для анализа и обработки данных, анализ и обработка данных Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: cd787881957d78f179107e46b2650de4618c7724
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282330"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Языки, поддерживаемые Виртуальной машиной для обработки и анализа данных

С помощью виртуальной машины Data Science (DSVM) вы можете создавать аналитику на широком диапазоне платформ данных. Кроме интерфейсов для платформ удаленных данных DSVM предоставляет локальный экземпляр для быстрой разработки и создания прототипов.

Следующие инструменты платформы данных поддерживаются на DSVM.

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| | |
| ------------- | ------------- |
| Что это такое?   | Экземпляр локальной реляционной базы данных      |
| Поддерживаемые издания DSVM      | Windows 2016: Сервер S'L 2017, Windows 2019: Сервер S'L 2019      |
| Распространенные способы применения      | Быстрая локальная разработка приложений с меньшим набором данных <br/> Запуск R внутри базы данных   |
| Ссылки на примеры      |    Небольшая выборка набора данных в Нью-йорке загружается в базу данных S'L:<br/>  `nyctaxi` <br/> Образец Jupyter, показывающий Microsoft Machine Learning Server и аналитику в базе данных, можно найти по адресу:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Похожие инструменты на DSVM       | SQL Server Management Studio <br/> Драйверы ODBC и JDBC<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> Издание разработчика серверов S'L может использоваться только для целей разработки и тестирования. Для запуска в рабочей среде вам потребуется лицензия или одна из виртуальных машин SQL Server.


### <a name="setup"></a>Настройка

Сервер базы данных уже настроен на настройку, и `SQL Server (MSSQLSERVER)`службы Windows, связанные с сервером S'L (например), настроены на автоматическое запуск. Единственный ручной шаг включает в себя включение аналитики в базе данных с помощью Microsoft Machine Learning Server. Вы можете включить аналитику, запустив следующую команду в качестве одноразового действия в студии управления серверами S'L (SSMS). Запустите эту команду после входа в систему в качестве администратора машины, откройте новый запрос в SSMS и убедитесь, что выбранная `master`база данных:

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
Для запуска студии управления серверами S'L можно найти «Студию управления серверами» в списке программ или использовать поиск Windows для его поиска и запуска. При запросе для учетных данных выберите ```localhost``` **аутентификацию Windows** и используйте имя машины или в поле имени **сервера S'L.**

### <a name="how-to-use-and-run-it"></a>Как использовать и запустить его

По умолчанию сервер базы данных с экземпляром базы данных по умолчанию работает автоматически. Чтобы получить доступ к базе данных SQL Server локально, на виртуальной машине можно использовать такие средства, как SQL Server Management Studio. Учетные записи локальных администраторов имеют доступ к администратору в базе данных.

Кроме того, DSVM поставляется с драйверами ODBC и JDBC для разговоров с базами данных S'L Server, базами данных Azure S'L и хранилищем данных Azure S'L из приложений, написанных на нескольких языках, включая Python и Machine Learning Server.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Как он настроен и устанавливается на DSVM? 

 Сервер S'L устанавливается стандартным способом. Его можно найти в `C:\Program Files\Microsoft SQL Server`. Экземпляр Машинного сервера в базе `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`данных находится по адресу . DSVM также имеет отдельный автономный экземпляр Machine Learning `C:\Program Files\Microsoft\R Server\R_SERVER`Server, который устанавливается на . Эти два экземпляра Machine Learning Server не являются обменом библиотеками.


## <a name="apache-spark-2x-standalone"></a>Экземпляр Apache Spark 2.x (изолированный)

| | |
| ------------- | ------------- |
| Что это такое?   | Отдельный (один узла в процессе) экземпляр популярной платформы Apache Spark; система быстрой, крупномасштабной обработки данных и машинного обучения     |
| Поддерживаемые издания DSVM      | Linux     |
| Распространенные способы применения      | Быстрая разработка приложений Spark/PySpark локально с меньшим набором данных и более поздним развертыванием на больших кластерах Spark, таких как Azure HDInsight<br/> Тест Microsoft машинного обучения сервера Spark контекст <br />Используйте SparkML или библиотеку [MMLSpark](https://github.com/Azure/mmlspark) корпорации Майкрософт с открытым исходным кодом для создания приложений ML |
| Ссылки на примеры      |    Пример Jupyter: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Сервер машинного обучения Microsoft (контекст spark): /dsvm/образцы/MRS/MRSSparkContextSample.R |
| Похожие инструменты на DSVM       | PySpark, Scala<br/>Jupyter (ядра Spark/PySpark)<br/>Сервер машинного обучения Microsoft, SparkR, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Использование
Задания Spark можно отправить на командную строку, запустив `spark-submit` или `pyspark` команду. Вы можете также создать записную книжку Jupyter с ядром Spark.

Вы можете использовать Spark от R, используя такие библиотеки, как SparkR, Sparklyr и Microsoft Machine Learning Server, которые доступны на DSVM. См. указатели на примеры в предыдущей таблице.

### <a name="setup"></a>Настройка
Перед запуском в контексте Spark в Microsoft Machine Learning Server на Ubuntu Linux DSVM издание, вы должны завершить одноразовый шаг установки, чтобы локальный один узла Hadoop HDFS и Yarn экземпляр. По умолчанию на виртуальной машине для обработки и анализа данных службы Hadoop установлены, но отключены. Чтобы включить их, запустите следующие команды в качестве корня в первый раз:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Вы можете остановить услуги, связанные с Hadoop, когда они вам больше не нужны, запустив ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```.

Образец, демонстрирующие, как разрабатывать и тестировать MRS в удаленном контексте Spark (который является автономным экземпляром Spark на DSVM), предоставляется и доступен в каталоге. `/dsvm/samples/MRS`


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Как он настроен и устанавливается на DSVM? 
|Платформа|Размещение установки ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Библиотеки для доступа к данным из хранилища Azure Blob или Хранилища озер данных Azure, использующие библиотеки машинного обучения Microsoft MMLSpark, предустановлены в $SPARK/HOME/jars. Эти JAR-файлы автоматически загружаются при запуске Spark. По умолчанию Spark использует данные на локальном диске. 

Для экземпляра Spark в DSVM для доступа к данным, хранящимся в хранилище `core-site.xml` Blob или Хранилище озер Лазурных данных, необходимо создать и настроить файл на основе шаблона, найденного в $SPARK»HOME/conf/core-site.xml.template. Вы также должны иметь соответствующие учетные данные для доступа к хранилищу Blob и хранению озер данных Azure. (Обратите внимание, что файлы шаблонов используют заполнители для конфигураций хранения памяти Blob и Azure Data Lake.)

Для получения более подробной информации о создании [Authentication with Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)учетных данных службы хранения озер Лазурных данных см. После того, как учетные данные для хранения Blob или Хранилища озер Лазурных данных будут введены в файл core-site.xml, можно ссылаться на данные, хранящиеся в этих источниках, через префикс URI wasb:// или adl://.

