---
title: Использование Azure Data Box для переноса данных из локальной HDFS из магазина в службу хранилища Azure
description: Перенесите данные из локального хранилища HDFS в хранилище Azure
services: storage
author: normesta
ms.service: storage
ms.date: 06/05/2019
ms.author: normesta
ms.topic: article
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 9a42135df38cde91cc6626a3f7d0328334af0a5d
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729067"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Использовать Azure Data Box для переноса данных из локального хранилища HDFS в хранилище Azure

Можно перенести данные из локального хранилища HDFS кластера Hadoop в службу хранилища Azure (хранилище BLOB-объектов или Gen2 хранилища Озера данных) с помощью устройство Data Box. Можно выбрать поле данных, которое 80 ТБ или сильно нагружает поле 770 ТБ данных.

Эта статья поможет вам выполнить эти задачи:

:heavy_check_mark: Копирование данных на Data Box или устройство активно поле данных.

:heavy_check_mark: Отправьте устройство обратно в корпорацию Майкрософт.

:heavy_check_mark: Переместите данные на вашей учетной записи хранения Gen2 хранилища Озера данных.

## <a name="prerequisites"></a>Технические условия

Необходимо, чтобы эти действия для завершения миграции.

* Учетная запись хранения Azure, **не** включено иерархических пространств имен.

* Если вы хотите использовать учетную запись Gen2 хранилища Озера данных Azure (учетная запись хранения, **does** иерархических пространств имен, включено), а затем создать ее на этом этапе может потребоваться.

* Кластер Hadoop в локальной, исходными данными.

* [Устройство Azure Data Box](https://azure.microsoft.com/services/storage/databox/).

    - [Заказ Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) или [Data Box тяжелой](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). При заказе устройства, не забудьте выбрать учетную запись хранения, **не** включено иерархических пространств имен. Это обусловлено Data Box устройства еще не поддерживают приема прямой передачи в Gen2 хранилища Озера данных Azure. Необходимо будет скопировать в учетную запись хранения, а затем выполните вторую копию в учетную запись ADLS Gen2. Соответствующие инструкции приведены в указанные ниже действия.
    - Подключение кабелей и подключитесь к [Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) или [большой поле данных](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) к локальной сети.

Если вы готовы, начнем.

## <a name="copy-your-data-to-a-data-box-device"></a>Копирование данных на устройство Data Box

Чтобы скопировать данные из локального хранилища HDFS на устройство Data Box, вы настроить несколько вещей, а затем с помощью [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) средство.

Если объем данных, который выполняется копирование превышает емкость одного поля данных, либо с одним узлом на большой поле данных, разбейте набор данных в размерах, которые помещаются в устройствах.

Выполните следующие действия для копирования данных с помощью REST API -интерфейсы из Blob-объект хранилища в устройство Data Box. Интерфейс REST API сделает устройство отобразится в качестве хранилища HDFS в кластере. 


1. Перед копированием данных через интерфейс REST, определите примитивы безопасности и соединения для подключения к интерфейсу REST на Data Box или большой поле данных. Войдите в локальный пользовательский Интерфейс поле данных веб- и перейдите к **Connect и копирования** страницы. В службе хранилища Azure учетной записи для вашего устройства в разделе **доступ к параметрам**, найдите и выберите **REST**.

    ![Страница «Подключение и копирование»](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. В учетной записи доступа и передачи данных диалоговое окно, скопируйте **конечной точки службы BLOB-объектов** и **ключ учетной записи хранения**. Из конечной точки службы BLOB-объектов, пропустить `https://` и завершающей косой черты.

    В данном случае является конечная точка: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. Узел часть URI, который будет использоваться: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Например, см. в разделе Практическое [соединиться с REST по протоколу http](/azure/databox/data-box-deploy-copy-data-via-rest). 

     ![Диалоговое окно «Доступ к учетной записи хранения и передачи данных»](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Добавление конечной точки и поля данных или высокой поле данных узла IP-адреса для `/etc/hosts` на каждом узле.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```
    Если вы используете другой механизм для DNS, необходимо убедиться, что поле может быть разрешено конечной точки.
    
4. Задайте переменной оболочки `azjars` указывал на `hadoop-azure` и `microsoft-windowsazure-storage-sdk` JAR-файлы. Эти файлы находятся в каталоге установки Hadoop (можно проверить, если эти файлы существуют с помощью этой команды `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure` где `<hadoop_install_dir>` — это каталог, где установлен Hadoop) использовать полные пути. 
    
    ```
    # azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar
    # azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar
    ```

5. Создание контейнера хранилища, который вы хотите использовать для копирования данных. Следует также указать конечную папку в этой команде. Это может быть фиктивный конечную папку на этом этапе.

    ```
    # hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -mkdir -p  wasb://[container_name]@[blob_service_endpoint]/[destination_folder]
    ```

6. Выполните команду list, чтобы убедиться, что контейнер и папку были созданы.

    ```
    # hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -ls -R  wasb://[container_name]@[blob_service_endpoint]/
    ```

7. Копирование данных из Hadoop HDFS в хранилище BLOB-объектов поле данных, в контейнер, который был создан ранее. Если не найден, которые копируются в папку, команда автоматически создаст его.

    ```
    # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -strategy dynamic -m 4 -update \
    [source_directory] \
           wasb://[container_name]@[blob_service_endpoint]/[destination_folder]       
    ```
   `-libjars` Параметр используется для выполнения `hadoop-azure*.jar` и зависимой `azure-storage*.jar` доступ к файлам `distcp`. Это может уже существовать для некоторых кластеров.
   
   В следующем примере показан способ `distcp` команда используется для копирования данных.
   
   ```
   # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -strategy dynamic -m 4 -update \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/testfiles
   ```
  
Для повышения скорости копирования:
- Попробуйте изменить количество модулей сопоставления. (В примере выше используется `m` = 4 модулей сопоставления.)
- Попробуйте запустить несколько `distcp` в параллельном режиме.
- Помните, что большие файлы работают лучше, чем небольших файлов.
    
## <a name="ship-the-data-box-to-microsoft"></a>Поставка поле данных в корпорацию Майкрософт

Выполните следующие действия для подготовки и отправьте устройство Data Box в корпорацию Майкрософт.

1. После завершения копирования данных выполните:
    
    - [Подготовка к отправке на Data Box или поле данных в большой](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).
    - После завершения подготовки устройства, загрузите файлы Спецификации. Необходимо использовать эти Спецификации или манифест файлы позже, чтобы проверить, передать данные в Azure. 
    - Завершите работу устройства и кабели.
2.  Вызовите курьера для отправки посылки службой доставки UPS. Следуйте инструкциям по выполнению следующих процедур:

    - [Поставляться на Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up) 
    - [Поставлять вашей доступно поле данных](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).
3.  После того как корпорация Майкрософт получает устройства, он подключен к сети центра обработки данных и данные передаются в учетную запись хранения, указанным пользователем (в иерархических пространств имен отключена) Если вы разместил заказ устройства. Спецификации файлов убедитесь, что все ваши данные отправляется в Azure. Теперь можно переместить эти данные в учетную запись хранилища Gen2 хранилища Озера данных.


## <a name="move-the-data-onto-your-data-lake-storage-gen2-storage-account"></a>Перемещение данных в вашей учетной записи хранения Gen2 хранилища Озера данных

Этот шаг требуется, если вы используете Gen2 хранилища Озера данных Azure как хранилище данных. При использовании только BLOB-объектов учетной записи хранения без иерархического пространства имен, как хранилище данных, этот шаг не нужно.

Это можно сделать двумя способами.

- Используйте [фабрики данных Azure для перемещения данных в ADLS Gen2](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). Необходимо будет указать **хранилище BLOB-объектов** как источник.

- Используйте кластер Hadoop под управлением Azure. Эту команду DistCp, можно выполнить:

    ```bash
    hadoop distcp -Dfs.azure.account.key.{source_account}.dfs.windows.net={source_account_key} abfs://{source_container} @{source_account}.dfs.windows.net/[path] abfs://{dest_container}@{dest_account}.dfs.windows.net/[path]
    ```

Эта команда копирует данные и метаданные из вашей учетной записи хранения в вашей учетной записи хранения Gen2 хранилища Озера данных.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как работает Gen2 хранилища Озера данных с кластерами HDInsight. См. раздел [Use Azure Data Lake Storage Gen2 with Azure HDInsight clusters](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) (Использование хранилища Azure Data Lake поколения 2 с кластерами Azure HDInsight).
