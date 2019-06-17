---
title: Выполнение заданий Apache Sqoop с помощью Azure HDInsight в Apache Hadoop
description: Вы узнаете, как использовать Azure PowerShell с рабочей станции для запуска Sqoop, импорта и экспорта между кластером HDInsight и базой данных SQL Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 6764d8d812789c9f54fa59e10b2a3e416e583a9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "62129405"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Использование Apache Sqoop с Hadoop в HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Узнайте, как использовать Apache Sqoop в HDInsight для импорта и экспорта данных между кластером HDInsight и базу данных Azure SQL.

Несмотря на то, что Apache Hadoop — идеальный вариант для обработки неструктурированных и частично структурированных данных, таких как журналы и файлы, может существовать необходимость обработки структурированных данных, которые хранятся в реляционных базах данных.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) — это средство, предназначенное для передачи данных между кластерами Hadoop и реляционными базами данных. С его помощью можно импортировать данные из системы управления реляционной базой данных (реляционной СУБД), например SQL Server, MySQL или Oracle, в распределенную файловую систему Hadoop (HDFS), преобразовать данные в системе Hadoop с использованием MapReduce или Apache Hive, а затем экспортировать данные обратно в реляционную СУБД. В этой статье вы используете базу данных SQL Server для реляционной базы данных.

> [!IMPORTANT]  
> В этой статье настраивает тестовую среду для выполнения передачи данных. Затем выберите метод передачи данных для этой среды из одного из методов в разделе [заданий Sqoop, запустите](#run-sqoop-jobs), еще ниже.

Версиях Sqoop, поддерживаемых в кластерах HDInsight, см. в разделе [новые возможности, доступные в HDInsight?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>Ознакомление со сценарием

Кластер HDInsight имеет несколько примеров данных. Вы будете использовать два следующих образца:

* Файл журнала Apache Log4j, расположенный в `/example/data/sample.log`. Из файла извлекаются следующие журналы:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Таблицу Hive с именем `hivesampletable`, которая ссылается на файл данных, расположенный в папке `/hive/warehouse/hivesampletable`. Эта таблица содержит некоторые данные о мобильных устройствах.
  
  | Поле | Тип данных |
  | --- | --- |
  | clientid |string |
  | querytime |string |
  | market |string |
  | deviceplatform |string |
  | devicemake |string |
  | devicemodel |string |
  | состояние |string |
  | country |string |
  | querydwelltime |double |
  | sessionid |bigint |
  | sessionpagevieworder |bigint |

В этой статье используйте эти два набора данных для тестирования Sqoop, импорта и экспорта.

## <a name="create-cluster-and-sql-database"></a>Настройка тестовой среды
Кластер, базы данных SQL и другие объекты создаются с помощью портала Azure, с помощью шаблона Azure Resource Manager. Шаблон можно найти в [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). Шаблон Resource Manager вызывает пакет bacpac для развертывания схем таблиц в базе данных SQL.  Пакет BACPAC хранится в общедоступном контейнере больших двоичных объектов (https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac ). Чтобы использовать закрытый контейнер для BACPAC-файлов, используйте следующие значения в шаблоне:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> С помощью шаблона или портала Azure можно импортировать только BACPAC-файл из хранилища BLOB-объектов Azure.

1. Выберите следующее изображение, чтобы открыть шаблон Resource Manager на портале Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Введите следующие свойства.

    |Поле |Значение |
    |---|---|
    |Подписка |Выберите подписку Azure из раскрывающегося списка.|
    |Группа ресурсов |Выберите свою группу ресурсов из раскрывающегося списка или создайте новую|
    |Расположение |Выберите регион из раскрывающегося списка.|
    |Имя кластера, |Введите имя кластера Hadoop. Используйте только буквы нижнего регистра.|
    |Имя пользователя для входа в кластер |Сохранить предварительно заполненного значения `admin`.|
    |Пароль для входа в кластер |Введите пароль.|
    |Имя пользователя SSH |Сохранить предварительно заполненного значения `sshuser`.|
    |SSH пароль |Введите пароль.|
    |Учетные данные администратора SQL |Сохранить предварительно заполненного значения `sqluser`.|
    |Пароль администратора SQL |Введите пароль.|
    |расположение артефактов | Используйте значение по умолчанию, если вы не хотите использовать собственный bacpac-файл в другом месте.|
    |Маркер Sas расположения артефактов |Не указывайте.|
    |Имя bacpac-файла |Используйте значение по умолчанию, если вы не хотите использовать собственный bacpac-файл.|
    |Расположение |Используйте значение по умолчанию.|

    Имя сервера SQL Azure будет `<ClusterName>dbserver`. Имя базы данных будет `<ClusterName>db`. Имя учетной записи хранения по умолчанию будет `e6qhezrh2pdqu`.

3. Выберите **Я принимаю указанные выше условия**.

4. Щелкните **Приобрести**. Появится новый элемент под названием "Выполняется отправка развертывания для развертывания шаблона". Процесс создания кластера и базы данных SQL занимает около 20 минут.

## <a name="run-sqoop-jobs"></a>Выполнение заданий Sqoop

HDInsight может выполнять задания Sqoop с помощью различных методов. Используйте следующую таблицу, чтобы решить, какой метод подходит вам, а затем перейдите по ссылке к пошаговому руководству.

| **Используйте** , если требуется... | ... **интерактивная** оболочка | ...**пакетная** обработка | ...из этого **кластера операционной системы** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X или Windows |
| [.NET SDK для Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (сейчас) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Ограничения

* Массовый экспорт: при использовании HDInsight на основе Linux соединитель Sqoop, применяемый для экспорта данных в Microsoft SQL Server или базу данных SQL Azure, пока не поддерживает операции массовой вставки.
* Пакетная обработка: при использовании HDInsight на основе Linux, когда для вставок применяется параметр `-batch`, Sqoop выполняет несколько вставок вместо пакетной обработки операций вставки.

## <a name="next-steps"></a>Дальнейшие действия
Теперь вы узнали, как использовать Sqoop. Дополнительные сведения см. на следующих ресурсах:

* [Использование Hive и HiveQL с Hadoop в HDInsight для анализа примера файла Apache log4j](../hdinsight-use-hive.md)
* [Использование Pig с Hadoop в HDInsight](../hdinsight-use-pig.md)
* [Отправка данных в HDInsight](../hdinsight-upload-data.md). Узнайте о других способах отправки данных в HDInsight или хранилище BLOB-объектов Azure.
