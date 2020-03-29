---
title: Выполнение заданий Apache Sqoop с помощью Azure HDInsight в Apache Hadoop
description: Вы узнаете, как использовать Azure PowerShell с рабочей станции для запуска Sqoop, импорта и экспорта между кластером HDInsight и базой данных SQL Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 8353c0fba034022a79570d09b320b7b5c4c3e60a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74951859"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Использование Apache Sqoop с Hadoop в HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Узнайте, как использовать Apache Sqoop в HDInsight для импорта и экспорта данных между кластером HDInsight и базой данных Azure S'L.

Хотя Apache Hadoop является естественным выбором для обработки неструктурированных и полуструктурированных данных, таких как журналы и файлы, может также возникнуть необходимость в обработке структурированных данных, которые хранятся в реляционных базах данных.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) — это средство, предназначенное для передачи данных между кластерами Hadoop и реляционными базами данных. С его помощью можно импортировать данные из системы управления реляционной базой данных (реляционной СУБД), например SQL Server, MySQL или Oracle, в распределенную файловую систему Hadoop (HDFS), преобразовать данные в системе Hadoop с использованием MapReduce или Apache Hive, а затем экспортировать данные обратно в реляционную СУБД. В этой статье вы используете базу данных сервера S'L для своей реляционной базы данных.

> [!IMPORTANT]  
> Эта статья устанавливает тестовую среду для выполнения передачи данных. Затем вы выбираете метод передачи данных для этой среды из одного из методов в разделе [Run Sqoop задания](#run-sqoop-jobs), далее ниже.

Для версий Sqoop, которые поддерживаются [What's new in the cluster versions provided by HDInsight?](../hdinsight-component-versioning.md) в кластерах HDInsight, см.

## <a name="understand-the-scenario"></a>Ознакомление со сценарием

Кластер HDInsight имеет несколько примеров данных. Вы будете использовать два следующих образца:

* Файл журнала Apache Log4j, который `/example/data/sample.log`находится по адресу . Из файла извлекаются следующие журналы:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Таблица Hive `hivesampletable`под названием , которая `/hive/warehouse/hivesampletable`ссылается на файл данных, расположенный на . Эта таблица содержит некоторые данные о мобильных устройствах.
  
  | Поле | Тип данных |
  | --- | --- |
  | clientid |строка |
  | querytime |строка |
  | market |строка |
  | deviceplatform |строка |
  | devicemake |строка |
  | devicemodel |строка |
  | state |строка |
  | country |строка |
  | querydwelltime |double |
  | sessionid |BIGINT |
  | sessionpagevieworder |BIGINT |

В этой статье вы используете эти два набора данных для проверки импорта и экспорта Sqoop.

## <a name="set-up-test-environment"></a><a name="create-cluster-and-sql-database"></a>Настройка тестовой среды

Кластер, база данных S'L и другие объекты создаются через портал Azure с помощью шаблона Azure Resource Manager. Шаблон можно найти в [шаблонах быстрого запуска Azure.](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/) Шаблон менеджера ресурсов вызывает пакет bacpac для развертывания схем таблицы в базу данных S'L.  Пакет BACPAC хранится в общедоступном контейнере больших двоичных объектов (https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac). Чтобы использовать закрытый контейнер для BACPAC-файлов, используйте следующие значения в шаблоне:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> С помощью шаблона или портала Azure можно импортировать только BACPAC-файл из хранилища BLOB-объектов Azure.

1. Выберите следующее изображение, чтобы открыть шаблон менеджера ресурсов на портале Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Введите следующие свойства.

    |Поле |Значение |
    |---|---|
    |Подписка |Выберите подписку Azure из списка выпадающих.|
    |Группа ресурсов |Выберите группу ресурсов из списка выпадающих или создайте новую|
    |Расположение |Выберите регион из раскрывающегося списка.|
    |Имя кластера, |Введите имя кластера Hadoop. Используйте только строповую букву.|
    |Имя пользователя для входа в кластер |Сохраняйте предварительно заселенное значение. `admin`|
    |Пароль для входа в кластер |Введите пароль.|
    |Имя пользователя Ssh |Сохраняйте предварительно заселенное значение. `sshuser`|
    |Ssh Пароль |Введите пароль.|
    |Sql Админ Войти |Сохраняйте предварительно заселенное значение. `sqluser`|
    |Пароль Sql Admin |Введите пароль.|
    |_artifacts Местоположение | Используйте значение по умолчанию, если вы не хотите использовать свой собственный файл bacpac в другом месте.|
    |_artifacts Расположение Сас Токен |Не указывайте.|
    |Название файла Bacpac |Используйте значение по умолчанию, если вы не хотите использовать свой собственный файл bacpac.|
    |Расположение |Используйте значение по умолчанию.|

    Имя сервера Azure S'L Server будет `<ClusterName>dbserver`. Имя базы данных будет `<ClusterName>db`. Имя учетной записи `e6qhezrh2pdqu`хранилища по умолчанию будет .

3. Выберите **Я принимаю указанные выше условия**.

4. Нажмите кнопку **Купить**. Появится новый элемент под названием "Выполняется отправка развертывания для развертывания шаблона". Процесс создания кластера и базы данных SQL занимает около 20 минут.

## <a name="run-sqoop-jobs"></a>Выполнение заданий Sqoop

HDInsight может выполнять задания Sqoop с помощью различных методов. Используйте следующую таблицу, чтобы решить, какой метод подходит вам, а затем перейдите по ссылке к пошаговому руководству.

| **Используйте** , если требуется... | ... **интерактивная** оболочка | ... **обработка пакетов** | ...из этого **кластера операционной системы** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X или Windows |
| [.NET SDK для Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (сейчас) |
| [Лазурная силаШелл](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Ограничения

* Массовый экспорт - С linux-на основе HDInsight, разъем Sqoop, используемый для экспорта данных в Microsoft S'L Server или базу данных Azure S'L в настоящее время не поддерживает объемные вставки.
* Пакетная обработка: при использовании HDInsight на основе Linux, когда для вставок применяется параметр `-batch`, Sqoop выполняет несколько вставок вместо пакетной обработки операций вставки.

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы узнали, как использовать Sqoop. Дополнительные сведения см. на следующих ресурсах:

* [Использование Hive и HiveQL с Hadoop в HDInsight для анализа примера файла Apache log4j](../hdinsight-use-hive.md)
* [Передача данных в HDInsight](../hdinsight-upload-data.md): узнайте о других способах отправки данных в HDInsight и хранилище больших двоичных объектов Azure.
* [Использование Apache Sqoop для импорта и экспорта данных между Apache Hadoop в HDInsight и базой данных SQL](./apache-hadoop-use-sqoop-mac-linux.md)