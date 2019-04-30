---
title: Требования к хранилищу BLOB-объектов Microsoft Azure Data Box | Документация Майкрософт
description: Дополнительные сведения о поддерживаемых версиях пакетов SDK, API-интерфейсов, клиентских библиотек для хранилища BLOB-объектов Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 71e0ebf7d7851ae65a6fba67a1695d755fd98bb1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61436500"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Требования к хранилищу BLOB-объектов Azure Data Box

В этой статье перечислены версии API-интерфейсы Azure, клиентских библиотек Azure и средств, поддерживаемых с хранилищем BLOB-объект поля данных. Хранилище BLOB-объектов Data Box предоставляет функции управления большими двоичными объектами с помощью семантики, согласованной с Azure. В этой статье также перечислены известные различия между хранилищем BLOB-объектов Azure Data Box и службой хранилища Azure.

Прежде чем подключиться к хранилищу BLOB-объектов Data Box, советуем внимательно ознакомиться с приведенной информацией и при необходимости обращаться к ней.


## <a name="storage-differences"></a>Различия хранилищ

|     Компонент                                             |     Хранилище Azure                                     |     Хранилище BLOB-объектов службы Data Box |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Хранилище файлов Azure                                   |    Поддерживаются облачные общие папки с файлами SMB              |    Не поддерживается      |
|    Шифрование службы для неактивных данных                  |    256-битное шифрование AES                             |    256-битное шифрование AES |
|    Тип учетной записи хранения                                 |    Учетные записи хранения общего назначения и учетные записи хранения BLOB-объектов Azure    |    Только общего назначения версии 1|
|    Имя BLOB-объекта                                            |    1024 символов (2048 байт)                     |    880 символов (1760 байт)|
|    Максимальный размер блока большого двоичного объекта                              |    4,75 ТБ (100 МБ х 50 000 блоков)                   |    4,75 ТБ (100 МБ х 50 000 блоков) для Azure Data Box версии 1.8 и более поздних.|
|    Максимальный размер страничного BLOB-объекта                               |    8 ТБ                                               |    1 ТБ                   |
|    Размер страницы страничного BLOB-объекта                                  |    512 байт                                          |    4 КБ                   |

## <a name="supported-api-versions"></a>Поддерживаемые версии API

Следующие версии API служб хранилища Azure поддерживаются с хранилищем BLOB-объектов Data Box:

Azure Data Box версии 1.8 и более поздние

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17);
- [2016-05-31](/rest/api/storageservices/version-2016-05-31);
- [2015-12-11](/rest/api/storageservices/version-2015-12-11);
- [2015-07-08](/rest/api/storageservices/version-2015-07-08);
- [2015-04-05](/rest/api/storageservices/version-2015-04-05) |
## <a name="supported-azure-client-libraries"></a>Поддерживаемые клиентские библиотеки Azure

Для хранилища BLOB-объектов службы Data Box существуют определенные клиентские библиотеки и определенные требования к суффиксу конечной точки. Конечные точки хранилища BLOB-объектов Data Box не имеют полного соответствия с последней версией REST API хранилища BLOB-объектов Azure. Сведения см. в разделе [Поддерживаемые версии API](#supported-api-versions). Для клиентских библиотек хранилища вам необходимо знать версию, совместимую с REST API.

### <a name="azure-data-box-18-onwards"></a>Azure Data Box версии 1.8 и более поздние

| Клиентская библиотека     |Поддерживаемая версия хранилища BLOB-объектов службы Data Box     | Связать   |     Спецификация конечной точки      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    9.2.0                                           |    Пакет NuGet: https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0    <br>Выпуск GitHub: https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0                                                                                                                                                                                               |    Файл app.config                 |
|    Java                |    7.0.0                                           |    Пакет Maven: https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>Выпуск GitHub: https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0                                                                                                                                                                              |    Настройка строки подключения         |
|    Node.js             |    2.8.3                                           |    Ссылка на NPM:   https://www.npmjs.com/package/azure-storage   (Запуска: `npm install azure-storage@2.7.0`)   <br>Выпуск GitHub: https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3                                                                                                                                                                        |    Объявление экземпляра службы    |
|    C++                 |    5.2.0                                           |    Пакет NuGet: https://www.nuget.org/packages/wastorage.v140/5.2.0   <br>Выпуск GitHub: https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0                                                                                                                                                                                                     |    Настройка строки подключения         |
|    PHP                 |    1.2.0                                           |    Выпуск GitHub:<br>Общая версия: https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common   <br>Большой двоичный объект: https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob      <br>Установка через компоновщик (дополнительные сведения см. ниже)                                                                                                             |    Настройка строки подключения         |
|    Python              |    1.1.0                                           |    Выпуск GitHub:<br>Общая версия: https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>Большой двоичный объект: https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob                                                                                                                                                                          |    Объявление экземпляра службы    |
|    Ruby                |    1.0.1                                           |    Пакет RubyGems:<br>Общая версия: https://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>Большой двоичный объект: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>Выпуск GitHub:<br>Общая версия: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>Большой двоичный объект: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    Настройка строки подключения         |



### <a name="install-php-client-via-composer---current"></a>Установка клиента PHP через компоновщик — текущая версия

Для установки через компоновщик сделайте следующее (возьмите для примера большой двоичный объект).
1. Создайте файл с именем composer.json в корневом каталоге проекта со следующим кодом.

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Загрузите `composer.phar` в корневую папку проекта.

3. Выполните команду: установка php composer.phar.

### <a name="endpoint-declaration"></a>Объявление конечной точки

Конечная точка хранилища BLOB-объектов службы Azure Data Box содержит две части: имя региона и домен Data Box. В пакета SDK для хранилища BLOB-объектов поле данных, конечная точка по умолчанию является `\<serial no. of the device>.microsoftdatabox.com`.  Дополнительные сведения о конечной точке службы BLOB-объектов см. в статье [Руководство. Копирование данных в хранилище BLOB-объектов Azure Data Box с помощью REST API](data-box-deploy-copy-data-via-rest.md).
 
## <a name="examples"></a>Примеры

### <a name="net"></a>.NET

Для хранилища BLOB-объектов Data Box суффикс конечной точки указывается в файле `app.config`.

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Для хранилища BLOB-объектов Data Box суффикс конечной точки указывается в настройке строки подключения.

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Для хранилища BLOB-объектов Data Box суффикс конечной точки указывается в экземпляре объявления.

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Для хранилища BLOB-объектов Data Box суффикс конечной точки указывается в настройке строки подключения.

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Для хранилища BLOB-объектов Data Box суффикс конечной точки указывается в настройке строки подключения.

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Для хранилища BLOB-объектов Data Box суффикс конечной точки указывается в экземпляре объявления.

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Для хранилища BLOB-объектов Data Box суффикс конечной точки указывается в настройке строки подключения.

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание Azure Data Box](data-box-deploy-ordered.md)
