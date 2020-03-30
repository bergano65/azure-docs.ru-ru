---
title: Копирование данных из HDFS с помощью фабрики данных Azure
description: Узнайте, как копировать данные из облака или локального источника HDFS на поддерживаемые приемники хранилища данных с помощью действия копирования в конвейере фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 2cd76afa9412e89c57cfb6c357eb164ce5d3d1c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75830434"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Копирование данных из HDFS с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
> * [Версия 1](v1/data-factory-hdfs-connector.md)
> * [Текущая версия](connector-hdfs.md)

В этой статье описывается, как копировать данные с сервера HDFS. Дополнительные сведения о Фабрике данных Azure см. во [вводной статье](introduction.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем HDFS поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)

В частности, этот соединитель HDFS поддерживает:

- Копирование файлов с помощью **Windows** (Kerberos) или **Anonymous** проверки подлинности.
- Копирование файлов с помощью протокола **webhdfs** или **встроенной поддержки DistCp**.
- Копирование файлов как есть или разбор/генерация файлов с [поддерживаемыми форматами файлов и кодеками сжатия.](supported-file-formats-and-compression-codecs.md)

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Убедитесь, что Integration Runtime доступны **ВСЕ** адреса [сервер_узла_имен]:[порт_узла_имен] и [сервер_узла_данных]:[порт_узла_данных] кластера Hadoop. По умолчанию используются [порт_узла_имен] 50070 и [порт_узла_данных] 50075.

## <a name="getting-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к HDFS.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы HDFS поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **Hdfs** | Да |
| url |URL-адрес в HDFS |Да |
| authenticationType | Допустимые значения — **Анонимная** или **Windows**. <br><br> Чтобы использовать **проверку подлинности Kerberos** для разъема HDFS, обратитесь к [этому разделу,](#use-kerberos-authentication-for-hdfs-connector) чтобы настроить вашу среду для помещений соответственно. |Да |
| userName |Имя пользователя для проверки подлинности Windows. Для проверки подлинности Kerberos укажите `<username>@<domain>.com`. |Да (для проверки подлинности Windows) |
| password |Пароль для проверки подлинности Windows. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). |Да (для проверки подлинности Windows) |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Узнайте больше из [раздела Prerequisites.](#prerequisites) Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |нет |

**Пример: использование анонимной проверки подлинности**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пример: использование проверки подлинности Windows**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Следующие свойства поддерживаются для `location` HDFS в настройках в наборе данных на основе формата:

| Свойство   | Описание                                                  | Обязательно |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Свойство типа `location` под в наборе данных должно быть установлено на **HdfsLocation.** | Да      |
| folderPath | Путь к папке. Если требуется использовать подстановочный знак для фильтрации папки, пропустите эту настройку и укажите в настройках источника активности. | нет       |
| fileName   | Имя файла под данной папкойPath. Если вы хотите использовать подстановочный знак для фильтрации файлов, пропустите эту настройку и укажите в настройках источника активности. | нет       |

**Примере:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником HDFS.

### <a name="hdfs-as-source"></a>HDFS в качестве источника

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Следующие свойства поддерживаются для `storeSettings` HDFS в настройках в формате на основе источника копий:

| Свойство                 | Описание                                                  | Обязательно                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Свойство типа `storeSettings` под должны быть установлены на **HdfsReadSettings.** | Да                                           |
| recursive                | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Обратите внимание, что если для свойства recursive задано значение true, а приемником является файловое хранилище, пустые папки и вложенные папки не создаются в приемнике. Разрешенные значения **верны** (по умолчанию) и **ложные.** | нет                                            |
| подстановочный знакFolderPath       | Путь папки с символами подстановочных знаков для фильтрации папок источника. <br>Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если фактическое имя папки содержит подстановочный знак или escape-символ. <br>Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). | нет                                            |
| подстановочный знакFileName         | Имя файла с символами подстановочных знаков под данной папкойPath/wildcardFolderPath для фильтрации исходных файлов. <br>Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если фактическое имя папки содержит подстановочный знак или escape-символ.  Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). | Да, `fileName` если не указан в наборе данных |
| modifiedDatetimeStart    | Фильтр файлов на основе атрибута: Последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br> Свойства могут иметь значение NULL. Это означает, что фильтры атрибута файла не будут применяться к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени. | нет                                            |
| modifiedDatetimeEnd      | То же, что и выше.                                               | нет                                            |
| distcpSettings | Группа свойств при использовании HDFS DistCp. | нет |
| resourceManagerEndpoint | Конечная точка менеджера ресурсов Yarn | Да, если используется DistCp |
| tempScriptPath | Путь к папке для хранения временного командного скрипта DistCp. Файл скрипта создает фабрика данных, и после завершения задания копирования файл удаляется. | Да, если используется DistCp |
| distcpOptions | Дополнительные параметры для команды DistCp. | нет |
| maxConcurrentConnections | Одновременное число подключений для подключения к хранилищу. Указать только при ограничении одновременного подключения к хранилику данных. | нет                                            |

**Примере:**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HdfsReadSettings",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Примеры фильтров папок и файлов

В этом разделе описываются результаты применения фильтров с подстановочными знаками к пути папки и имени файла.

| folderPath | fileName             | recursive | Структура папки исхода и результат фильтра (файлы **жирным шрифтом** извлекаются) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (пусто, используйте по умолчанию) | false     | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5.csv<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `Folder*`  | (пусто, используйте по умолчанию) | Да      | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `Folder*`  | `*.csv`              | false     | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5.csv<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `Folder*`  | `*.csv`              | Да      | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Использовать DistCp для копирования данных из HDFS

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) является программой командной строки Hadoop для создания распределенной копии в кластере Hadoop. При запуске команды Distcp сначала будут перечислены все файлы, которые будут скопированы, созданы несколько заданий сопоставления в кластере Hadoop, и каждое задание сопоставления будет выполнять двоичное копирование из источника в приемник.

Копирование поддержки активности с помощью DistCp для копирования файлов как в Azure Blob (включая [постановочную копию)](copy-activity-performance.md)или Azure Data Lake Store, и в этом случае она может полностью использовать силу кластера вместо того, чтобы работать на автономном запуске интеграции. При этом пропускная способность копирования будет выше, особенно если ваш кластер очень мощный. Основываясь на вашей конфигурации в Azure Data Factory, действие копирования автоматически создает команду distcp, передает в ваш кластер Hadoop и контролирует статус копирования.

### <a name="prerequisites"></a>Предварительные требования

Чтобы использовать DistCp для копирования файлов "как есть" из HDFS в большой двоичный объект Azure (включая промежуточную копию) или в Azure Data Lake Store, убедитесь, что ваш кластер Hadoop соответствует требованиям ниже:

1. Службы MapReduce и Yarn включены.
2. Версия Yarn — 2.5 или более поздняя.
3. Сервер HDFS интегрирован с вашим целевым хранилищем данных (большой двоичный объект Azure или Azure Data Lake Store):

    - Начиная с версии Hadoop 2.7, изначально поддерживается файловая система больших двоичных объектов Azure. Необходимо указать путь к JAR-файлу в конфигурации среды Hadoop.
    - Файловая система Azure Data Lake Store упаковывается, начиная с Hadoop версии 3.0.0-alpha1. Если ваш кластер Hadoop ниже этой версии, вам нужно вручную импортировать пакеты JAR, связанные с ADLS (azure-datalake-store.jar), в кластер [здесь](https://hadoop.apache.org/releases.html) и указать путь к JAR-файлу в конфигурации среды Hadoop.

4. Подготовьте временную папку в HDFS. Она используется для хранения скрипта оболочки DistCp, поэтому она будет занимать пространство на уровне килобайтов.
5. Убедитесь, что учетная запись пользователя, предоставленная в связанной службе HDFS, имеет разрешение на отправку приложение в Yarn, создание вложенных папок, чтение и запись файлов в указанной выше временной папке.

### <a name="configurations"></a>Конфигурации

См, связанные с конфигурациями и примерами DistCp, смотрите в [разделе источника HDFS.](#hdfs-as-source)

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Использование аутентификации Kerberos для соединителя HDFS

Есть два способа настроить локальную среду для использования аутентификации Kerberos в соединителе HDFS. Вы можете применить тот, который соответствует вашему сценарию.
* Вариант 1. [Присоединение компьютера, где выполняется локальная среда выполнения интеграции к области Kerberos](#kerberos-join-realm)
* Вариант 2: [Включить взаимное доверие между доменом Windows и областью Kerberos](#kerberos-mutual-trust)

### <a name="option-1-join-self-hosted-integration-runtime-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>Вариант 1: Присоединиться к самостоятельной интеграции Runtime машины в области Керберос

#### <a name="requirements"></a>Требования

* Компьютер, где выполняется локальная среда выполнения интеграции, нужно присоединить к области Kerberos. Его нельзя присоединять к домену Windows.

#### <a name="how-to-configure"></a>Порядок настройки

**На компьютере, где выполняется локальная среда выполнения интеграции:**

1.  Запустите служебную программу **Ksetup**, чтобы настроить сервер центра распространения ключей (KDC) и область Kerberos.

    Компьютер должен быть настроен как член рабочей группы, так как области Kerberos отличаются от доменов Windows. Для этого нужно настроить область Kerberos и добавить сервер центра распространения ключей, как описано ниже. При необходимости замените *REALM.COM* значением своей соответствующей области.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Перезапустите** компьютер после выполнения этих двух команд.

2.  Проверьте конфигурацию с помощью команды **Ksetup**. Результат должен выглядеть примерно так:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Настройка фабрики данных Azure**

* Настройте соединитель HDFS на использование **аутентификации Windows**, указав имя участника Kerberos и пароль для подключения к источнику данных HDFS. Проверьте сведения о конфигурации — [свойства связанной службы HDFS](#linked-service-properties).

### <a name="option-2-enable-mutual-trust-between-windows-domain-and-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Вариант 2. Активация взаимного доверия между доменом Windows и областью Kerberos

#### <a name="requirements"></a>Требования

*   Компьютер, где выполняется локальная среда выполнения интеграции, необходимо присоединить к домену Windows.
*   Требуется разрешение на обновление параметров контроллера домена.

#### <a name="how-to-configure"></a>Порядок настройки

> [!NOTE]
> При необходимости замените REALM.COM и AD.COM значениями своей соответствующей области и контроллера домена в следующем руководстве.

**Настройка на сервере центра распространения ключей**

1. Измените конфигурацию центра распространения ключей в файле **krb5.conf**, чтобы создать отношения доверия с доменом Windows, как указано ниже в шаблоне конфигурации. По умолчанию файл конфигурации содержится в папке **/etc/krb5.conf**.

           [logging]
            default = FILE:/var/log/krb5libs.log
            kdc = FILE:/var/log/krb5kdc.log
            admin_server = FILE:/var/log/kadmind.log
            
           [libdefaults]
            default_realm = REALM.COM
            dns_lookup_realm = false
            dns_lookup_kdc = false
            ticket_lifetime = 24h
            renew_lifetime = 7d
            forwardable = true
            
           [realms]
            REALM.COM = {
             kdc = node.REALM.COM
             admin_server = node.REALM.COM
            }
           AD.COM = {
            kdc = windc.ad.com
            admin_server = windc.ad.com
           }
            
           [domain_realm]
            .REALM.COM = REALM.COM
            REALM.COM = REALM.COM
            .ad.com = AD.COM
            ad.com = AD.COM
            
           [capaths]
            AD.COM = {
             REALM.COM = .
            }

   **Перезапустите** службу KDC после настройки.

2. Подготовьте основной директор по имени **\@krbtgt/REALM.COM AD.COM** на сервере KDC со следующей командой:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. В файле **hadoop.security.auth_to_local** добавьте к конфигурации HDFS правило `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**Настройка на контроллере домена**

1.  Выполнить следующие команды **Ksetup,** чтобы добавить запись в области:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Установите доверительные отношения между доменом Windows и областью Kerberos. «пароль» — это пароль для основного **\@krbtgt/REALM.COM AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Выберите алгоритм шифрования, используемый в Kerberos.

    1. Последовательно выберите "Диспетчер серверов" > "Управление групповой политикой" > "Домен" > "Объекты групповой политики" > "Групповая политика по умолчанию или действующая групповая политика", затем нажмите "Редактировать".

    2. Откроется всплывающее окно **редактора управления групповой политикой**. Последовательно выберите "Конфигурация компьютера" > "Политики" > "Параметры Windows" > "Параметры безопасности" > "Локальные политики" > "Параметры безопасности". Затем щелкните **Сетевая безопасность: настройка типов шифрования, разрешенных Kerberos**.

    3. Выберите алгоритм шифрования, который вы хотите использовать при подключении к центру распространения ключей. Обычно можно просто выбрать все доступные параметры.

        ![Типы шифрования конфигурации для Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. С помощью команды **Ksetup** задайте алгоритм шифрования, который будет использоваться для этой области.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Создайте сопоставление между субъектом Kerberos и учетной записью домена, чтобы использовать субъект Kerberos в домене Windows.

    1. Щелкните "Средства администрирования" и выберите **Пользователи и компьютеры Active Directory**.

    2. Налажив расширенные функции, нажав **на View** > **Advanced Features.**

    3. Найдите учетную запись, для которой нужно создать сопоставление, и щелкните ее правой кнопкой мыши, чтобы открыть **Сопоставление имен** > **Имена Kerberos**.

    4. Добавьте субъект из области.

        ![Сопоставление удостоверения безопасности](media/connector-hdfs/map-security-identity.png)

**На компьютере, где выполняется локальная среда выполнения интеграции:**

* Выполните следующие команды **Ksetup**, чтобы добавить запись области.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Настройка фабрики данных Azure**

* Настройте соединитель HDFS для использования **аутентификации Windows**, указав учетную запись домена или имя субъекта-службы Kerberos для подключения к источнику данных HDFS. Проверьте сведения о конфигурации — [свойства связанной службы HDFS](#linked-service-properties).

## <a name="lookup-activity-properties"></a>Свойства активности поиска

Чтобы узнать подробности о свойствах, проверьте [активность поиска.](control-flow-lookup-activity.md)

## <a name="legacy-models"></a>Устаревшие модели

>[!NOTE]
>Следующие модели по-прежнему поддерживаются как для обратной совместимости. Вы предложили использовать новую модель, упомянутую в вышеуказанных разделах в будущем, и ADF авторство UI переключился на генерацию новой модели.

### <a name="legacy-dataset-model"></a>Устаревшая модель набора данных

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type для набора данных должно иметь значение **FileShare**. |Да |
| folderPath | Путь к папке, Фильтр с подстановочными знаками поддерживается. Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если имя фактического файла содержит подстановочный знак или escape-символ. <br/><br/>Примеры: rootfolder/subfolder/. Дополнительные примеры см. в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). |Да |
| fileName |  **Имя или фильтр шаблонов** для файлов по указанному folderPath. Если этому свойству не присвоить значение, набор данных будет указывать на все файлы в папке. <br/><br/>Допустимые знаки подстановки для фильтра: `*` (соответствует нулю или нескольким символам) и `?` (соответствует нулю или одному символу).<br/>Пример 1. `"fileName": "*.csv"`<br/>Пример 2. `"fileName": "???20180427.txt"`<br/>Используйте `^` для экранирования знаков, если фактическое имя папки содержит подстановочный знак или этот escape-символ. |нет |
| modifiedDatetimeStart | Фильтр файлов на основе атрибута: Последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br/><br/> Имейте в виду, общая производительность движения данных будет влияние, позволяя эту настройку, когда вы хотите сделать файл фильтр из огромного количества файлов. <br/><br/> Свойства могут быть NULL, что означает, что фильтр атрибутов файла не будет применен к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени.| нет |
| modifiedDatetimeEnd | Фильтр файлов на основе атрибута: Последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br/><br/> Имейте в виду, общая производительность движения данных будет влияние, позволяя эту настройку, когда вы хотите сделать файл фильтр из огромного количества файлов. <br/><br/> Свойства могут быть NULL, что означает, что фильтр атрибутов файла не будет применен к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени.| нет |
| format | Если вы хотите **копировать файлы как между** файлохранилищами (двоичная копия), пропустите раздел формата как в водах, так и в определениях набора выходных данных.<br/><br/>Если нужно проанализировать или создать файлы определенного формата, поддерживаются следующие типы форматов файлов: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах о [текстовом формате](supported-file-formats-and-compression-codecs-legacy.md#text-format), [формате Json](supported-file-formats-and-compression-codecs-legacy.md#json-format), [формате Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [формате Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format) и [ формате Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Нет (только для сценария двоичного копирования) |
| compression | Укажите тип и уровень сжатия данных. Дополнительные сведения см. в разделе [Поддержка сжатия](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Поддерживаемые типы: **ГЗип,** **Дефлировать**, **B'ip2**, и **цифферлат**.<br/>Поддерживаемые уровни: **Оптимальный** и **Быстрый**. |нет |

>[!TIP]
>Чтобы скопировать все файлы в папке, укажите только **folderPath**.<br>Чтобы скопировать один файл с заданным именем, укажите **folderPath** с частью папки и **fileName** с именем файла.<br>Чтобы скопировать подмножество файлов в папке, укажите **folderPath** с частью папки и **fileName** с фильтром подстановочных знаков.

**Примере:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Модель источника исходной деятельности устаревшей копирования

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type источника действия копирования должно иметь значение **HdfsSource**. |Да |
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Обратите внимание, что если для свойства recursive задано значение true, а приемником является файловое хранилище, в приемнике не будут создаваться пустые папки и вложенные папки.<br/>Разрешенные значения: **правда** (по умолчанию), **ложные** | нет |
| distcpSettings | Группа свойств при использовании HDFS DistCp. | нет |
| resourceManagerEndpoint | Конечная точка менеджера ресурсов Yarn | Да, если используется DistCp |
| tempScriptPath | Путь к папке для хранения временного командного скрипта DistCp. Файл скрипта создает фабрика данных, и после завершения задания копирования файл удаляется. | Да, если используется DistCp |
| distcpOptions | Дополнительные параметры для команды DistCp. | нет |
| maxConcurrentConnections | Одновременное число подключений для подключения к хранилищу. Указать только при ограничении одновременного подключения к хранилику данных. | нет |

**Пример: источник HDFS в копировании деятельности с помощью DistCp**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
