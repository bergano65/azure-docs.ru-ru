---
title: Переход из магазина hdFS на прем еневие в хранилище Azure с помощью ящика данных Azure
description: Перенос данных из предприимчезного магазина HDFS в хранилище Azure
author: normesta
ms.service: storage
ms.date: 02/14/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: c0c6a8637223727a9b0c88245d939605f6a8530e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302006"
---
# <a name="migrate-from-on-prem-hdfs-store-to-azure-storage-with-azure-data-box"></a>Переход из магазина hdFS на прем еневие в хранилище Azure с помощью ящика данных Azure

Данные можно перенести из собственного хранилища HDFS в кластер Hadoop в Azure Storage (blob Storage или Data Lake Storage Gen2) с помощью устройства Data Box. Вы можете выбрать из 80-ТБ коробки данных или 770-ТБ данных Box Heavy.

Эта статья поможет вам выполнить следующие задачи:

> [!div class="checklist"]
> * Приготовьтесь к переносу данных.
> * Копируйте данные на поле данных или устройство Data Box Heavy.
> * Отправь устройство обратно в корпорацию Майкрософт.
> * Применение разрешений на доступ к файлам и каталогам (только Data Lake Storage Gen2)

## <a name="prerequisites"></a>Предварительные требования

Вам нужны эти вещи, чтобы завершить миграцию.

* Учетная запись хранения Azure.

* Кластер on-premises Hadoop, содержащий исходные данные.

* [Устройство Ящик данных Azure](https://azure.microsoft.com/services/storage/databox/).

  * [Закажите box или](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) [Data Box Heavy.](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered) 

  * Кабель и подключите box [или](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) к предварительной сети.

Если вы готовы, давайте начнем.

## <a name="copy-your-data-to-a-data-box-device"></a>Копирование данных на устройство Box данных

Если данные вписываются в одно устройство Box Data Box, вы скопируете данные на устройство Data Box. 

Если размер данных превышает емкость устройства Data Box, используйте [необязательную процедуру для разделения данных на несколько устройств Data Box,](#appendix-split-data-across-multiple-data-box-devices) а затем выполните этот шаг. 

Чтобы скопировать данные из вашего собственного магазина HDFS на устройство Box данных, вы установите несколько вещей, а затем воспользуетесь инструментом [DistCp.](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)

Выполните следующие действия, чтобы скопировать данные через API REST памяти Blob/Object на устройство Box. Интерфейс REST API сделает устройство отображаться в качестве магазина HDFS в кластере.

1. Перед копированием данных через REST определите примитивы безопасности и соединения для подключения к интерфейсу REST на коробке данных или в коробке данных Heavy. Войдите в локальный веб-ui Box и перейдите на страницу **Connect и copy.** Против учетной записи хранилища Azure для вашего устройства, в **настройках доступа,** найти и выбрать **REST.**

    ![Страница "Подключиться и скопировать"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. В учетной записи хранения доступа и загрузке диалогов данных скопируйте **конечную точку службы Blob** и **ключ учетной записи Storage.** С точки зрения обслуживания капли, `https://` опустить и задний черту.

    В этом случае конечная `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`точка: . Часть хоста URI, которую вы `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`будете использовать, это: . Например, см. [Connect to REST over http](/azure/databox/data-box-deploy-copy-data-via-rest) 

     ![Диалог "Доступ к учетной записи хранения и загрузке данных"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Добавьте конечную точку и IP-адрес узла `/etc/hosts` Box или Data Box Heavy на каждом узлах.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    Если вы используете другой механизм для DNS, следует убедиться, что конечная точка Data Box может быть решена.

4. Установите переменную `azjars` оболочки `hadoop-azure` к `azure-storage` расположению файлов и банки. Вы можете найти эти файлы в каталоге установки Hadoop.

    Чтобы определить, существуют ли эти `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`файлы, используйте следующую команду: . Замените `<hadoop_install_dir>` заполнитель на путь к каталогу, где вы установили Hadoop. Обязательно используйте полностью квалифицированные пути.

    Примеры:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Создайте контейнер для хранения данных, который требуется использовать для копирования данных. В этой команде также следует указать каталог назначения. Это может быть фиктивный каталог назначения на данный момент.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Замените `<blob_service_endpoint>` заполнитель с именем вашей конечной точки службы blob.

    * Замените `<account_key>` заполнителя ключом доступа вашей учетной записи.

    * Замените `<container-name>` заполнитель на имя вашего контейнера.

    * Замените `<destination_directory>` заполнитель с именем каталога, который вы хотите скопировать ваши данные.

6. Выполнить команду списка, чтобы убедиться, что ваш контейнер и каталог были созданы.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Замените `<blob_service_endpoint>` заполнитель с именем вашей конечной точки службы blob.

   * Замените `<account_key>` заполнителя ключом доступа вашей учетной записи.

   * Замените `<container-name>` заполнитель на имя вашего контейнера.

7. Копирование данных из хранилища Hadoop HDFS в хранилище Data Box Blob в контейнер, созданный ранее. Если каталог, в который вы копируете, не найден, команда автоматически создает его.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Замените `<blob_service_endpoint>` заполнитель с именем вашей конечной точки службы blob.

    * Замените `<account_key>` заполнителя ключом доступа вашей учетной записи.

    * Замените `<container-name>` заполнитель на имя вашего контейнера.

    * Замените `<exlusion_filelist_file>` заполнитель именем файла, содержащего список исключений файлов.

    * Замените `<source_directory>` заполнитель именем каталога, содержащего данные, которые необходимо скопировать.

    * Замените `<destination_directory>` заполнитель с именем каталога, который вы хотите скопировать ваши данные.

    `-libjars` Опция используется для `hadoop-azure*.jar` того, `azure-storage*.jar` чтобы сделать и зависимые файлы доступными для. `distcp` Это может уже произойти для некоторых кластеров.

    В следующем примере `distcp` показано, как команда используется для копирования данных.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    Для улучшения скорости копирования:

    * Попробуйте изменить количество картографов. (В приведенном `m` выше примере используется 4 картографа.)

    * Попробуйте `distcp` запустить несколько параллельно.

    * Помните, что большие файлы работают лучше, чем небольшие файлы.

## <a name="ship-the-data-box-to-microsoft"></a>Отправка коробки данных в корпорацию Майкрософт

Выполните следующие действия, чтобы подготовить и отправить устройство Box данных в корпорацию Майкрософт.

1. Во-первых, [приготовьтесь к отправке на box или Data Box Heavy.](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest)

2. После завершения подготовки устройства загрузите файлы BOM. Вы будете использовать эти ФАЙЛы BOM или манифест позже для проверки данных, загруженных в Azure.

3. Выключите устройство и удалите кабели.

4. Вызовите курьера для отправки посылки службой доставки UPS.

    * Для устройств Box [Ship your Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)с данными см.

    * Для тяжелых устройств Data Box [см.](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up)

5. После того, как корпорация Майкрософт получает ваше устройство, оно подключено к сети центра обработки данных и данные загружаются в указанную учетную запись хранилища, указанную при размещении заказа устройства. Проверить на наличие файлов BOM, что все ваши данные загружены в Azure. 

## <a name="apply-access-permissions-to-files-and-directories-data-lake-storage-gen2-only"></a>Применение разрешений на доступ к файлам и каталогам (только Data Lake Storage Gen2)

Данные уже есть в учетной записи хранения Azure. Теперь вы будете применять разрешения на доступ к файлам и каталогам.

> [!NOTE]
> Этот шаг необходим только в том случае, если вы используете Хранилище данных Azure Data Lake Data В. Gen2 в качестве хранилища данных. Если вы используете только учетную запись хранения капли без иерархического пространства имен в качестве хранилища данных, вы можете пропустить этот раздел.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Создайте основной сервис для учетной записи Azure Data Lake Storage Gen2

Чтобы создать основной сервис, см. [Как: Используйте портал для создания приложения Azure AD и принципа службы, которое может получить доступ к ресурсам.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

* При выполнении действий, описанных в разделе [Назначение приложению роли](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) этой статьи, не забудьте назначить субъекту-службе роль **участника данных BLOB-объектов хранилища**.

* При выполнении шагов в [значениях Get для подписания в](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) разделе статьи сохраните идентификатор приложения и значения секрета клиента в текстовый файл. Они вам скоро понадобятся.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Создание списка скопированных файлов с их разрешениями

Из предварительного кластера Hadoop запустите эту команду:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Эта команда генерирует список скопированных файлов с их разрешениями.

> [!NOTE]
> В зависимости от количества файлов в HDFS, эта команда может занять много времени для запуска.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Создание списка идентификационных данных и отображение их в идентификаторах Active Directory (ADD) Azure Active Directory (ADD)

1. Скачать `copy-acls.py` сценарий. Просматривайте [скрипты помощника Скачать и настроили узлы края для выполнения ими](#download-helper-scripts) раздела этой статьи.

2. Запустите эту команду, чтобы создать список уникальных идентификационных данных.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Этот скрипт генерирует `id_map.json` файл с именем, содержащий идентификаторы, необходимые для отображения в идентификаторы на основе ЗАдуктора.

3. Откройте файл `id_map.json` в текстовом редакторе.

4. Для каждого объекта JSON, который отображается в файле, обновите `target` атрибут либо главного имени пользователя AAD (UPN) или ObjectId (OID) с соответствующим отображеным удостоверением личности. После того, как вы закончите, сохраните файл. Этот файл понадобится на следующем этапе.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Применение разрешений к скопированным файлам и применение отображения идентификационных данных

Запустите эту команду, чтобы применить разрешения к данным, которые были скопированы в учетную запись Data Lake Storage Gen2:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Замените заполнитель `<storage-account-name>` именем вашей учетной записи хранения.

* Замените `<container-name>` заполнитель на имя вашего контейнера.

* Замените `<application-id>` `<client-secret>` и заполнителей идентификатором приложения и секрет клиента, который вы собрали при создании основного сервиса.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Приложение: Разделение данных на нескольких устройствах Коробки данных

Перед тем, как переместить данные на устройство Box, необходимо загрузить некоторые скрипты-помощники, убедиться, что ваши данные организованы, чтобы поместиться на устройство Box data, и исключить ненужные файлы.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Скачать скрипты-помощники и настроить узлы края для их запуска

1. От вашего края или головного узла вашего локтевого кластера Hadoop запустите эту команду:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Эта команда клонирует репозиторий GitHub, содержащий скрипты помощника.

2. Убедитесь, что пакет [jq](https://stedolan.github.io/jq/) установлен на локальном компьютере.

   ```bash
   
   sudo apt-get install jq
   ```

3. Установите пакет [Python Запросов.](https://2.python-requests.org/en/master/)

   ```bash
   
   pip install requests
   ```

4. Установите выполнить разрешения на требуемые скрипты.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Убедитесь, что ваши данные организованы, чтобы поместиться на устройстве коробки данных

Если размер данных превышает размер одного устройства Box Data Box, можно разделить файлы на группы, которые можно хранить на нескольких устройствах Box Data Box.

Если ваши данные не превышают размер устройства singe Data Box, вы можете перейти к следующему разделу.

1. С повышенными разрешениями `generate-file-list` запустите скрипт, который вы скачали, следуя указаниям в предыдущем разделе.

   Вот описание параметров команды:

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

2. Копируйте генерируемые списки файлов в HDFS, чтобы они были доступны для задания [DistCp.](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Исключить ненужные файлы

Вам нужно будет исключить некоторые каталоги из задания DisCp. Например, исключить каталоги, содержащие сведения о состоянии, сохраняющие работу кластера.

В предварительном кластере Hadoop, где планируется инициировать задание DistCp, создайте файл, который определяет список каталогов, которые вы хотите исключить.

Ниже приведен пример:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как Data Lake Storage Gen2 работает с кластерами HDInsight. См. раздел [Use Azure Data Lake Storage Gen2 with Azure HDInsight clusters](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) (Использование хранилища Azure Data Lake поколения 2 с кластерами Azure HDInsight).
