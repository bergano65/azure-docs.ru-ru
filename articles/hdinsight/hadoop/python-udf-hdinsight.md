---
title: Использование определяемых пользователем функций Python с Apache Hive и Apache Pig в Azure HDInsight
description: Узнайте, как использовать определяемые пользователем функции Python стека технологий Apache Hadoop на базе Azure — Apache Hive и Apache Pig в HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 201bb40e5024442587f5508886da7e844f35be40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74148402"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Использование определяемых пользователем функций Python с Apache Hive и Apache Pig в HDInsight

Узнайте, как использовать определяемые пользователем функции (UDF) Python с Apache Hive и Apache Pig в Apache Hadoop на кластерах Azure HDInsight.

## <a name="python-on-hdinsight"></a><a name="python"></a>Python в HDInsight

По умолчанию на кластерах HDInsight 3.0 и более поздних версиях установлен Python версии 2.7. Apache Hive можно использовать с этой версией Python для потоковой обработки. При этом для передачи данных между Hive и определяемой пользователем функцией используется STDOUT и STDIN.

В состав HDInsight также входят Jython, который представляет собой реализацию Python, написанную на Java. Jython работает непосредственно на Java Virtual Machine и не использует потоковую передачу. Jython является рекомендуемым интерпретатором Python при использовании Python с Pig.

## <a name="prerequisites"></a>Предварительные требования

* **Кластер Hadoop на HDInsight**. Ознакомьтесь со статьей [Краткое руководство. Использование Apache Hadoop и Apache Hive в Azure HDInsight с шаблоном Resource Manager](apache-hadoop-linux-tutorial-get-started.md).
* **Клиент SSH**. Дополнительные сведения см. в руководстве по [подключению к HDInsight (Apache Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
* [Схема универсального кода ресурса (URI)](../hdinsight-hadoop-linux-information.md#URI-and-scheme) для основного хранилища кластеров. Это будет `wasb://` для хранилища `abfs://` Azure, для хранения данных Azure Data Lake Data Gen2 или adl:// для хранения azure Data Lake Data Gen1. Если для хранилища Azure включена безопасная передача, URI будет wasbs://.  См. также сведения о [безопасной передаче](../../storage/common/storage-require-secure-transfer.md).
* **Возможно ещих изменений в конфигурации хранилища.**  Просмотрите [конфигурацию хранилища](#storage-configuration) `BlobStorage`при использовании вида учетной записи хранилища.
* Необязательный параметр.  При планировании использования PowerShell вам понадобится установленный [модуль.](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)

> [!NOTE]  
> Учетная запись хранилища, используемая в этой статье, `wasbs` была Azure Storage с [включенной безопасной передачей](../../storage/common/storage-require-secure-transfer.md) и, таким образом, используется на протяжении всей статьи.

## <a name="storage-configuration"></a>Конфигурация хранилища

Нет никаких действий, если используемая учетная запись хранилища является доброй `Storage (general purpose v1)` или `StorageV2 (general purpose v2)`.  Процесс в этой статье будет производить `/tezstaging`выход, по крайней мере .  Конфигурация hadoop по `/tezstaging` умолчанию будет содержаться в переменной `fs.azure.page.blob.dir` конфигурации `core-site.xml` для обслуживания. `HDFS`  Эта конфигурация приведет к тому, что выход в каталог будет капли `BlobStorage`страницы, которые не поддерживаются для хранения учетной записи рода.  Чтобы `BlobStorage` использовать эту статью, удалите `/tezstaging` из переменной конфигурации. `fs.azure.page.blob.dir`  Конфигурация доступна из [uI Ambari.](../hdinsight-hadoop-manage-ambari.md)  В противном случае вы получите сообщение об ошибке:`Page blob is not supported for this account type.`

> [!WARNING]  
> Шаги в этом документе основаны на следующих предположениях:  
>
> * Вы создаете скрипты Python в локальной среде разработки.
> * Вы загружаете скрипты `scp` в HDInsight, используя либо команду, либо предоставленный скрипт PowerShell.
>
> Если вы хотите использовать [azure Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) для работы с HDInsight, то необходимо:
>
> * Создать скрипты в среде Cloud Shell.
> * Использовать `scp` для отправки файлов из Cloud Shell в HDInsight.
> * Использовать `ssh` из Cloud Shell для подключения к HDInsight и выполнения примеров.

## <a name="apache-hive-udf"></a><a name="hivepython"></a>Определяемая пользователем функция Apache Hive

Скрипт Python можно использовать в качестве определяемой пользователем функции из Hive через HiveQL с помощью инструкции `TRANSFORM`. Например, следующий запрос HiveQL вызывает файл `hiveudf.py`, хранящийся в учетной записи хранения Azure по умолчанию для кластера.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

Вот что делает данный пример:

1. Инструкция `add file` в начале файла добавляет файл `hiveudf.py` в распределенный кэш, и он становится доступен всем узлам кластера.
2. Инструкция `SELECT TRANSFORM ... USING` выбирает данные из `hivesampletable`. Она также передает параметры clientid, devicemake и devicemodel в скрипт `hiveudf.py`.
3. Предложение `AS` описывает поля, возвращаемые из `hiveudf.py`.

<a name="streamingpy"></a>

### <a name="create-file"></a>Создать файл

В среде разработки создайте текстовый файл с именем `hiveudf.py`. Используйте следующий код в качестве содержимого файла:

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

Сценарий выполняет следующие действия:

1. Читает строку данных от STDIN.
2. Стоящий в конце знак новой строки удаляется с помощью `string.strip(line, "\n ")`.
3. При обработке потока в одной строке будут содержаться все значения, разделенные символом табуляции. Поэтому можно использовать `string.split(line, "\t")` для разделения входящих данных при каждой табуляции, возвращая лишь поля.
4. По завершении обработки результат должен быть записан в поток STDOUT в виде одной строки, с разделенными символами табуляции полями. Например, `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. Цикл `while` повторяется до тех пор, пока считывается `line`.

Выходные данные скрипта представляют собой объединенные входные значения для `devicemake` и `devicemodel`, а также хэш для объединенного значения.

### <a name="upload-file-shell"></a>Файл загрузки (оболочка)

В приведенных ниже `sshuser` командах замените фактическое имя пользователя, если они отличаются друг от друга.  Заменить `mycluster` фактическим названием кластера.  Убедитесь, что рабочий каталог находится там, где находится файл.

1. Используйте `scp` для копирования файлов в кластер HDInsight. Отойдите от и введите команду ниже:

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Используйте SSH, чтобы подключиться к кластеру.  Отойдите от и введите команду ниже:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Из сеанса SSH добавьте файлы питона, загруженные ранее, в хранилище для кластера.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Используйте Hive UDF (оболочка)

1. Чтобы подключиться к Hive, используйте следующую команду из открытой сессии SSH:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Эта команда запускает клиент Beeline.

2. Введите следующий запрос `0: jdbc:hive2://headnodehost:10001/>` в командной строке:

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. После ввода последней строки запустится задание. По завершении задания эта команда возвращает выходные данные следующего вида:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Чтобы выйти из Билайна, введите следующую команду:

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Файл загрузки (PowerShell)

PowerShell также можно использовать для удаленного запуска запросов на использование Hive. Убедитесь, что ваш `hiveudf.py` рабочий каталог находится там, где находится.  Используйте следующий скрипт PowerShell для запуска `hiveudf.py` запроса Hive, в который используется скрипт:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Revise file path as needed
$pathToStreamingFile = ".\hiveudf.py"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToStreamingFile `
    -Blob "hiveudf.py" `
    -Container $container `
    -Context $context
```

> [!NOTE]  
> Дополнительные сведения об отправке файлов см. в статье [Отправка данных для заданий Apache Hadoop в HDInsight](../hdinsight-upload-data.md).

#### <a name="use-hive-udf"></a>Используйте Hive UDF

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"

$HiveQuery = "add file wasbs:///hiveudf.py;" +
                "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                "USING 'python hiveudf.py' AS " +
                "(clientid string, phoneLabel string, phoneHash string) " +
                "FROM hivesampletable " +
                "ORDER BY clientid LIMIT 50;"

# Create Hive job object
$jobDefinition = New-AzHDInsightHiveJobDefinition `
    -Query $HiveQuery

# For status bar updates
$activity="Hive query"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting query..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting on query to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -JobId $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
   -Clustername $clusterName `
   -JobId $job.JobId `
   -HttpCredential $creds `
   -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

Результат выполнения задания **Hive** должен выглядеть аналогично следующему примеру:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

## <a name="apache-pig-udf"></a><a name="pigpython"></a>Определяемая пользователем функция Apache Pig

Скрипт Python можно использовать в виде определяемой пользователем функции из Pig с использованием инструкции `GENERATE`. Вы можете запустить скрипт с помощью Jython или CPython.

* Jython работает на виртуальной машине Java и изначально может вызываться из Pig.
* CPython является внешним процессом, поэтому данные из Pig на JVM отправляются в скрипт, выполняющийся в процессе Python. Выходные данные скрипта Python отправляются обратно в Pig.

Чтобы указать интерпретатор Python, используйте `register` при указании ссылки на скрипт Python. Следующие примеры регистрируют скрипты с Pig в качестве `myfuncs`:

* **Для использования Jython**:`register '/path/to/pigudf.py' using jython as myfuncs;`
* **Для использования C Python**:`register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> При использовании Jython путь к файлу pig_jython может быть либо локальным, либо WASBS:// пути. Но при использовании CPython необходимо указать ссылку на файл в локальной файловой системе узла, который используется для отправки задания Pig.

После регистрации язык Pig Latin будет одинаковым для обоих примеров:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Вот что делает данный пример:

1. Первая строка загружает образец файла данных `sample.log` в `LOGS`. Она также определяет каждую запись как массив символов `chararray`.
2. Следующая строка отфильтровывает все пустые значения, сохраняя результат работы в `LOG`.
3. Затем выполняется итерация по записям в `LOG` и используется инструкция `GENERATE` для вызова метода `create_structure`, содержащегося в скрипте Python или Jython, загруженном как `myfuncs`. `LINE` используется для передачи текущей записи в функцию.
4. Наконец, выходные данные сбрасываются в поток STDOUT командой `DUMP`. После завершения операции эта команда выведет результат.

### <a name="create-file"></a>Создать файл

В среде разработки создайте текстовый файл с именем `pigudf.py`. Используйте следующий код в качестве содержимого файла:

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema


@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

В примере `LINE` Pig Latin вход определяется как шарар, потому что нет последовательной схемы для ввода. Скрипт Python выполняет преобразование данных в согласованную схему на выходе.

1. Инструкция `@outputSchema` задает формат данных, в котором они возвращаются в Pig. В данном случае это **data bag**, являющийся типом данных Pig. Корзина содержит следующие поля, все они имеют тип "Массив строк" (строки):

   * date — дата создания записи журнала;
   * date — время создания записи журнала;
   * classname — имя класса, для которого создана запись;
   * level — уровень журналирования;
   * detail — подробная информация о записи журнала.

2. Затем `def create_structure(input)` определяет функцию, в которую Pig отправляет строковые элементы.

3. Данные для примера, `sample.log`, в основном соответствуют схеме даты, времени, имени класса, уровня и подробной информации. Однако он содержит несколько строк, начинающихся с `*java.lang.Exception*`. Эти строки должны быть изменены в соответствии со схемой. Инструкция `if` проверяет на наличие таких строк, затем обрабатывает входные данные, переставляя строку `*java.lang.Exception*` в конец, формируя данные в соответствии с ожидаемой схемой.

4. Затем команда `split` используется для разделения данных по первым четырем символам пробела. Выходным данным присваиваются значения `date`, `time`, `classname`, `level` и `detail`.

5. И результаты возвращаются в Pig.

Когда данные возвращаются в Pig, они имеют согласованную схему, определенную инструкцией `@outputSchema`.

### <a name="upload-file-shell"></a>Файл загрузки (оболочка)

В приведенных ниже `sshuser` командах замените фактическое имя пользователя, если они отличаются друг от друга.  Заменить `mycluster` фактическим названием кластера.  Убедитесь, что рабочий каталог находится там, где находится файл.

1. Используйте `scp` для копирования файлов в кластер HDInsight. Отойдите от и введите команду ниже:

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Используйте SSH, чтобы подключиться к кластеру.  Отойдите от и введите команду ниже:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Из сеанса SSH добавьте файлы питона, загруженные ранее, в хранилище для кластера.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```

### <a name="use-pig-udf-shell"></a>Используйте Свинья UDF (оболочка)

1. Чтобы подключиться к свинье, используйте следующую команду из открытой сессии SSH:

    ```bash
    pig
    ```

2. В окне запроса `grunt>` введите следующие операторы:

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. После ввода указанной строки должно запуститься задание. По завершении задания эта команда возвращает выходные данные следующего вида:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Используйте `quit` для выхода из оболочки Grunt, а затем следующую команду для изменения файла pigudf.py в локальной файловой системе:

    ```bash
    nano pigudf.py
    ```

5. Войдите в редактор и раскомментируйте следующую строку, удалив символ `#` в начале строки.

    ```bash
    #from pig_util import outputSchema
    ```

    Эта строка изменяет сценарий Python для работы с C Python вместо Jython. Закончив вносить изменения, нажмите клавиши **CTRL+X**, чтобы выйти из редактора. Выберите **Y,** а затем **введите,** чтобы сохранить изменения.

6. Используйте команду `pig` , чтобы снова запустить оболочку. При появлении запроса `grunt>` введите следующие инструкции, чтобы запустить сценарий Python с помощью интерпретатора CPython.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Когда это задание будет выполнено, вы увидите такой же результат, как при запуске сценария с помощью Jython.

### <a name="upload-file-powershell"></a>Файл загрузки (PowerShell)

PowerShell также можно использовать для удаленного запуска запросов на использование Hive. Убедитесь, что ваш `pigudf.py` рабочий каталог находится там, где находится.  Используйте следующий скрипт PowerShell для запуска `pigudf.py` запроса Hive, в который используется скрипт:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Revise file path as needed
$pathToJythonFile = ".\pigudf.py"


# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToJythonFile `
    -Blob "pigudf.py" `
    -Container $container `
    -Context $context
```

### <a name="use-pig-udf-powershell"></a>Используйте Свинья UDF (PowerShell)

> [!NOTE]  
> При удаленной отправке задания с помощью PowerShell нельзя использовать CPython в качестве интерпретатора.

PowerShell также можно использовать для запуска заданий Pig Latin. Чтобы выполнить задание Pig `pigudf.py` Latin, используюв сценарий, используйте следующий скрипт PowerShell:

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"


$PigQuery = "Register wasbs:///pigudf.py using jython as myfuncs;" +
            "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
            "LOG = FILTER LOGS by LINE is not null;" +
            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
            "DUMP DETAILS;"

# Create Pig job object
$jobDefinition = New-AzHDInsightPigJobDefinition -Query $PigQuery

# For status bar updates
$activity="Pig job"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting job..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting for the Pig job to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -Job $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds `
    -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

Результат выполнения задания **Pig** должен выглядеть аналогично следующим данным:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a><a name="troubleshooting"></a>Устранение неполадок

### <a name="errors-when-running-jobs"></a>Ошибки при выполнении заданий

При выполнении задания hive может возникнуть ошибка, аналогичная приведенной ниже:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Эта проблема может быть вызвана символами окончания строк в файле Python. Многие редакторы Windows по умолчанию используют символы CRLF, но в приложениях Linux обычно ожидается использование символа LF.

Вы можете использовать следующие команды PowerShell для удаления символов CR перед передачей файла в HDInsight:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>Сценарии PowerShell

Оба примера скриптов PowerShell, используемых для запуска примеров, содержат закомментированную строку, которая отображает вывод ошибок для задания. Если вы не видите ожидаемый выход для задания, не прокомментируйте следующую строку и посмотрите, указывает ли информация об ошибке на проблему.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

Сведения об ошибках (STDERR) и результат выполнения задания (STDOUT) также записываются в хранилище HDInsight.

| Для данного задания... | Смотрите эти файлы в контейнере |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig, |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next-steps"></a><a name="next"></a>Дальнейшие действия

Если вам нужно загрузить модули Python, которые не поставляются по умолчанию, см. статью [How to deploy a Python module to Windows Azure HDInsight](https://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx) (Как развернуть модуль Python в Windows Azure HDInsight).

Сведения о других способах использования Pig и Hive и дополнительную информацию об использовании MapReduce см. в следующих документах:

* [Использование Hive и HiveQL с Hadoop в HDInsight для анализа примера файла Apache log4j](hdinsight-use-hive.md)
* [Использование MapReduce в Hadoop в HDInsight](hdinsight-use-mapreduce.md)
