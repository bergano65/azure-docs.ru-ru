---
title: Использование Azure Data Box для переноса данных из локальной HDFS из магазина в службу хранилища Azure
description: Перенесите данные из локального хранилища HDFS в хранилище Azure
services: storage
author: normesta
ms.service: storage
ms.date: 06/11/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 4445a8566c04d30cfb8743cbd33623f2e23f0dde
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595398"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Использовать Azure Data Box для переноса данных из локального хранилища HDFS в хранилище Azure

Можно перенести данные из локального хранилища HDFS кластера Hadoop в службу хранилища Azure (хранилище BLOB-объектов или Gen2 хранилища Озера данных) с помощью устройство Data Box. Можно выбрать поле данных, которое 80 ТБ или сильно нагружает поле 770 ТБ данных.

Эта статья поможет вам выполнить эти задачи:

> [!div class="checklist"]
> * Подготовка к миграции данных.
> * Копирование данных на Data Box или устройство активно поле данных.
> * Отправьте устройство обратно в корпорацию Майкрософт.
> * Переместите данные на уровень Gen2 хранилища Озера данных.

## <a name="prerequisites"></a>предварительные требования

Необходимо, чтобы эти действия для завершения миграции.

* Две учетные записи хранения; для которой включено иерархическое пространство имен, а тот, который не.

* Кластер Hadoop в локальной, исходными данными.

* [Устройство Azure Data Box](https://azure.microsoft.com/services/storage/databox/).

  * [Заказ Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) или [Data Box тяжелой](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). При заказе устройства, не забудьте выбрать учетную запись хранения, **не** включено иерархических пространств имен. Это обусловлено Data Box устройства еще не поддерживают приема прямой передачи в Gen2 хранилища Озера данных Azure. Необходимо будет скопировать в учетную запись хранения, а затем выполните вторую копию в учетную запись ADLS Gen2. Соответствующие инструкции приведены в указанные ниже действия.

  * Подключение кабелей и подключитесь к [Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) или [большой поле данных](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) к локальной сети.

Если вы готовы, начнем.

## <a name="copy-your-data-to-a-data-box-device"></a>Копирование данных на устройство Data Box

Если данные соответствуют в одном устройстве Data Box, будет копировать данные на устройство Data Box. 

Если объем данных превышает объем устройство Data Box, используйте [необязательную процедуру для разделения данных на нескольких устройствах Data Box](#appendix-split-data-across-multiple-data-box-devices) и затем выполнить этот шаг. 

Чтобы скопировать данные из локального хранилища HDFS на устройство Data Box, вы настроить несколько вещей, а затем с помощью [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) средство.

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

4. Задайте переменную оболочки `azjars` к расположению `hadoop-azure` и `azure-storage` JAR-файлы. Можно найти эти файлы в каталоге установки Hadoop.

    Чтобы определить, информацию о том, имеются ли эти файлы, используйте следующую команду: `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`. Замените `<hadoop_install_dir>` заполнитель путь к каталогу, где вы установили Hadoop. Не забудьте использовать полные пути.

    Примеры:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Создание контейнера хранилища, который вы хотите использовать для копирования данных. В рамках этой команды следует также указать каталог назначения. Это может быть на этом этапе фиктивного целевого каталога.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Замените `<blob_service_endpoint>` заполнитель с именем конечной точки службы BLOB-объектов.

    * Замените `<account_key>` — ключом доступа своей учетной записи.

    * Замените `<container-name>` заполнитель с именем контейнера.

    * Замените `<destination_directory>` заполнитель с именем каталога, который требуется скопировать данные.

6. Выполните команду списка, чтобы убедиться, что контейнер и каталога были созданы.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Замените `<blob_service_endpoint>` заполнитель с именем конечной точки службы BLOB-объектов.

   * Замените `<account_key>` — ключом доступа своей учетной записи.

   * Замените `<container-name>` заполнитель с именем контейнера.

7. Копирование данных из Hadoop HDFS в хранилище BLOB-объектов поле данных, в контейнер, который был создан ранее. Если не найден, которые копируются в каталог, команда автоматически создаст его.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Замените `<blob_service_endpoint>` заполнитель с именем конечной точки службы BLOB-объектов.

    * Замените `<account_key>` — ключом доступа своей учетной записи.

    * Замените `<container-name>` заполнитель с именем контейнера.

    * Замените `<exlusion_filelist_file>` заполнитель с именем файла, содержащего список исключения файлов.

    * Замените `<source_directory>` заполнитель с именем каталога, который содержит данные, которые требуется скопировать.

    * Замените `<destination_directory>` заполнитель с именем каталога, который требуется скопировать данные.

    `-libjars` Параметр используется для выполнения `hadoop-azure*.jar` и зависимой `azure-storage*.jar` доступ к файлам `distcp`. Это может уже существовать для некоторых кластеров.

    В следующем примере показан способ `distcp` команда используется для копирования данных.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    Для повышения скорости копирования:

    * Попробуйте изменить количество модулей сопоставления. (В примере выше используется `m` = 4 модулей сопоставления.)

    * Попробуйте запустить несколько `distcp` в параллельном режиме.

    * Помните, что большие файлы работают лучше, чем небольших файлов.

## <a name="ship-the-data-box-to-microsoft"></a>Поставка поле данных в корпорацию Майкрософт

Выполните следующие действия для подготовки и отправьте устройство Data Box в корпорацию Майкрософт.

1. Во-первых, [Подготовка к отправке на Data Box или поле данных в большой](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).

2. После завершения подготовки устройства, загрузите файлы Спецификации. Необходимо использовать эти Спецификации или манифест файлы позже, чтобы проверить, передать данные в Azure.

3. Завершите работу устройства и кабели.

4. Вызовите курьера для отправки посылки службой доставки UPS.

    * Data Box устройств, см. в разделе [поставляются на Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

    * Для устройств, активно поле данных, см. в разделе [поставляются в большой поле данных](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).

5. После того как корпорация Майкрософт получает устройства, он подключен к сети центра данных и данные передаются в учетную запись хранения, указанным пользователем (в иерархических пространств имен отключена) Если вы разместил заказ устройства. Спецификации файлов убедитесь, что все ваши данные отправляется в Azure. Теперь можно переместить эти данные в учетную запись хранилища Gen2 хранилища Озера данных.

## <a name="move-the-data-into-azure-data-lake-storage-gen2"></a>Переместите данные в Gen2 хранилища Озера данных Azure

Уже есть данные в учетную запись хранилища Azure. Теперь будет скопировать данные в учетной записи хранения Озера данных Azure и применить разрешения на доступ к файлам и каталогам.

> [!NOTE]
> Этот шаг требуется, если вы используете Gen2 хранилища Озера данных Azure как хранилище данных. Если вы используете только BLOB-объектов учетной записи хранения без иерархического пространства имен как хранилище данных, этот раздел можно пропустить.

### <a name="copy-data-to-the-azure-data-lake-storage-gen-2-account"></a>Копирование данных в учетную запись Azure Data Lake хранилища Gen 2

Данные можно скопировать с помощью фабрики данных Azure или с помощью кластера Hadoop под управлением Azure.

* Чтобы использовать фабрику данных Azure, см. в разделе [фабрики данных Azure для перемещения данных в ADLS Gen2](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). Не забудьте указать **хранилище BLOB-объектов** как источник.

* Чтобы использовать кластер Hadoop под управлением Azure, выполните следующую команду DistCp:

    ```bash
    hadoop distcp -Dfs.azure.account.key.<source_account>.dfs.windows.net=<source_account_key> abfs://<source_container> @<source_account>.dfs.windows.net/<source_path> abfs://<dest_container>@<dest_account>.dfs.windows.net/<dest_path>
    ```

    * Замените `<source_account>` и `<dest_account>` заполнители с именами учетных записей хранения источника и назначения.

    * Замените `<source_container>` и `<dest_container>` заполнители имена исходного и целевого контейнеров.

    * Замените `<source_path>` и `<dest_path>` заполнители источника и назначения путей к каталогам.

    * Замените `<source_account_key>` — ключом доступа учетной записи хранения, которая содержит данные.

    Эта команда копирует данные и метаданные из вашей учетной записи хранения в вашей учетной записи хранения Gen2 хранилища Озера данных.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Создание субъекта-службы для учетной записи Gen2 хранилища Озера данных Azure

Чтобы создать субъект-службу, см. в разделе [как: Создание приложения Azure Active Directory и субъект-службы с доступом к ресурсам с помощью портала](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

* При выполнении действий, описанных в разделе [Назначение приложению роли](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) этой статьи, не забудьте назначить субъекту-службе роль **участника данных BLOB-объектов хранилища**.

* При выполнении действия, описанные в [получить значения для входа в](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) части статьи, идентификатор приложения и секрет клиента в текстовый файл. Они вам скоро понадобятся.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Создать список скопированные файлы их разрешения

Из локального кластера Hadoop выполните следующую команду:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Эта команда создает список скопированные файлы их разрешения.

> [!NOTE]
> В зависимости от количества файлов в HDFS этой команды может занять много времени для запуска.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Создать список удостоверений и сопоставить их с удостоверениями Azure Active Directory (Добавить)

1. Скачайте `copy-acls.py` скрипта. См. в разделе [загрузить вспомогательные скрипты и настроить вашего граничного узла для их выполнения](#download-helper-scripts) этой статьи.

2. Выполните следующую команду, чтобы создать список уникальных идентификаторов.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Этот сценарий создает файл с именем `id_map.json` , содержит идентификаторы, которые необходимо сопоставить с удостоверениями на основе ADD.

3. Откройте файл `id_map.json` в текстовом редакторе.

4. Для каждого объекта JSON в файле обновления `target` атрибут AAD пользователя имя участника (UPN) или идентификатор объекта (OID), с соответствующим сопоставления удостоверений. Когда все будет готово, сохраните файл. Вам потребуется этот файл на следующем шаге.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Применить разрешения для копирования файлов и примените сопоставления удостоверений

Выполните следующую команду, чтобы применить разрешения к данным, который вы скопировали в учетную запись, Gen2 хранилища Озера данных:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Замените заполнитель `<storage-account-name>` именем вашей учетной записи хранения.

* Замените `<container-name>` заполнитель с именем контейнера.

* Замените `<application-id>` и `<client-secret>` заполнители приложения идентификатор и секрет клиента, собранных при создании субъекта-службы.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Приложение. Разбиение данных на нескольких устройствах Data Box

Перед перемещением данных на устройство Data Box, необходимо загрузить некоторые вспомогательные скрипты, убедитесь, что данные организованы помещается на устройство Data Box и исключить ненужные файлы.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Скачайте вспомогательные скрипты и для их запуска вашего граничного узла

1. Из edge или головной узел кластера Hadoop в локальной выполните следующую команду:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Эта команда Клонирует репозиторий GitHub, который содержит вспомогательные скрипты.

2. Убедитесь, что [jq](https://stedolan.github.io/jq/) пакет, установленный на локальном компьютере.

   ```bash
   
   sudo apt-get install jq
   ```

3. Установка [запросы](http://docs.python-requests.org/en/master/) пакета python.

   ```bash
   
   pip install requests
   ```

4. Разрешения на выполнение набора на необходимые сценарии.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Убедитесь, что данные организованы в соответствии с размерами устройство Data Box

Если размер данных превышает размер одно устройство Data Box, можно разбить файлы группы, которые можно хранить на нескольких устройствах Data Box.

Если данные не превышает размер одна устройство Data Box, вы можете перейти к следующему разделу.

1. С повышенным уровнем разрешений, запустите `generate-file-list` сценарий, который вы скачали, следуя инструкциям в предыдущем разделе.

   Здесь приводится описание параметров команды.

   ```
   sudo -u hdfs ./generate-file-list.py [-h] [-s DATABOX_SIZE] [-b FILELIST_BASENAME]
                    [-f LOG_CONFIG] [-l LOG_FILE]
                    [-v {DEBUG,INFO,WARNING,ERROR}]
                    path

   where:
   positional arguments:
   path                  The base HDFS path to process.

   optional arguments:
   -h, --help            show this help message and exit
   -s DATABOX_SIZE, --databox-size DATABOX_SIZE
                        The size of each Data Box in bytes.
   -b FILELIST_BASENAME, --filelist-basename FILELIST_BASENAME
                        The base name for the output filelists. Lists will be
                        named basename1, basename2, ... .
   -f LOG_CONFIG, --log-config LOG_CONFIG
                        The name of a configuration file for logging.
   -l LOG_FILE, --log-file LOG_FILE
                        Name of file to have log output written to (default is
                        stdout/stderr)
   -v {DEBUG,INFO,WARNING,ERROR}, --log-level {DEBUG,INFO,WARNING,ERROR}
                        Level of log information to output. Default is 'INFO'.
   ```

2. Скопируйте созданный файл перечислены в HDFS, чтобы они были доступны для [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) задания.

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Исключить ненужные файлы

Вам потребуется исключить некоторые каталоги из DisCp задания. Например Исключите каталоги, содержащие сведения о состоянии, для которых сохраняются под управлением кластера.

На локальный кластер Hadoop, где вы планируете запустить задание DistCp создайте файл, который указывает список каталогов, которые вы хотите исключить.

Ниже приведен пример:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Следующие шаги

Узнайте, как работает Gen2 хранилища Озера данных с кластерами HDInsight. См. раздел [Use Azure Data Lake Storage Gen2 with Azure HDInsight clusters](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) (Использование хранилища Azure Data Lake поколения 2 с кластерами Azure HDInsight).
