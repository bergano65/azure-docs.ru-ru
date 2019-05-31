---
title: Переносить данные в службу хранилища Azure из Amazon S3 сегментов с помощью AzCopy v10 | Документация Майкрософт
description: Передача данных с помощью AzCopy и Amazon S3 сегментов
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: c0bc74ac0fe45f2502064340a0c3ce5b82694b06
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245704"
---
# <a name="copy-data-from-amazon-s3-buckets-by-using-azcopy"></a>Копирование данных из Amazon S3 сегментов с помощью AzCopy

AzCopy — программа командной строки, который можно использовать для копирования больших двоичных объектов или файлов из учетной записи хранения. Эта статья поможет вам копировать объекты, папок и контейнеров из S3, Amazon Web Services (AWS) в хранилище больших двоичных объектов с помощью AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Выберите, каким образом необходимо предоставить учетные данные авторизации

* Авторизация в службе хранилища Azure, используйте Azure Active Directory (AD) или маркер подписи общего доступа (SAS).

* Чтобы авторизовать с AWS S3, используйте ключ доступа AWS и секретный ключ доступа.

### <a name="authorize-with-azure-storage"></a>Авторизовать со службой хранилища Azure

См. в разделе [начало работы с AzCopy](storage-use-azcopy-v10.md) статью, чтобы скачать AzCopy, а также выберите, как необходимо предоставить учетные данные авторизации в службе хранилища.

> [!NOTE]
> В примерах в этой статье предполагается, что вы прошли проверку подлинности личности с помощью `AzCopy login` команды. AzCopy использует учетную запись Azure AD для авторизации доступа к данным в хранилище BLOB-объектов.
>
> Если вы предпочитаете использовать маркер SAS для авторизации доступа к данным больших двоичных объектов, можно добавить этот маркер на URL-адрес ресурса в каждой команде AzCopy.
>
> Например, `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Авторизация с AWS S3

Соберите свой ключ доступа AWS и секретный ключ доступа, а затем задайте эти переменные среды:

| Операционная система | Команда  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-folders-and-buckets"></a>Копировать объекты, папок и контейнеров

AzCopy использует [поместить блок из URL-адрес](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API, поэтому данные копируются напрямую между AWS S3 и серверов хранилища. Эти операции копирования не использовать пропускную способность сети компьютера.

### <a name="copy-an-object"></a>Копирование объекта

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<object-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>"` |
| **Пример** | `azcopy cp "https://s3.amazonaws.com/mybucket/myobject" "https://mystorageaccount.blob.core.windows.net/mycontainer/myblob"` |

> [!NOTE]
> Примеры в этой статье использовать стиле путь URL-адреса, для AWS S3 контейнерах (например: `http://s3.amazonaws.com/<bucket-name>`). 
>
> Можно также использовать виртуальный размещенных стиле URL-адреса в (например: `http://bucket.s3.amazonaws.com`). 
>
> Дополнительные сведения о размещении виртуальных сегментов, см. в разделе [виртуального размещения из сегментов]] (https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html).

### <a name="copy-a-folder"></a>Скопируйте папку

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<folder-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<folder-name>" --recursive=true` |
| **Пример** | `azcopy cp "https://s3.amazonaws.com/mybucket/myfolder" "https://mystorageaccount.blob.core.windows.net/mycontainer/myfolder" --recursive=true` |

### <a name="copy-a-bucket"></a>Скопируйте контейнер

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true` |
| **Пример** | `azcopy cp "https://s3.amazonaws.com/mybucket" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Скопируйте все сегменты во всех регионах

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy cp "https://s3.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Пример** | `azcopy cp "https://s3.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Скопируйте все сегменты в определенном регионе S3

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy cp "https://s3-<region-name>.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Пример** | `azcopy cp "https://s3-rds.eu-north-1.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Обработка различий в правила именования объектов

AWS S3 имеет другой набор соглашения об именовании для имен контейнеров по сравнению с контейнерами BLOB-объектов Azure. Вы можете прочесть о них [здесь](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Если вы решили скопировать группы контейнеров в учетной записи хранения Azure, операция копирования может завершиться ошибкой из-за различия имен.

AzCopy обрабатывает два из наиболее распространенных проблем, которые могут возникнуть; контейнеры, содержащие точки и сегменты, может содержать последовательные дефисы. Имена контейнеров AWS S3 может содержать точек, последовательные дефисы, но контейнер в Azure нельзя. AzCopy замещающий точки, дефисы и последовательные дефисы на число, представляющее количество последовательных дефисов (например: контейнер с именем `my----bucket` становится `my-4-bucket`. 

Кроме того как AzCopy копирует файлы, он проверяет наличие конфликтов имен и пытается разрешить их. Например, если существуют сегменты с именем `bucket-name` и `bucket.name`, AzCopy разрешает контейнер с именем `bucket.name` первой для `bucket-name` и затем `bucket-name-2`.

## <a name="handle-differences-in-object-metadata"></a>Обработка различий в метаданных объекта

AWS S3 и Azure позволяют разных наборов символов в именах ключи объекта. Читайте о символах, AWS S3 использует [здесь](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys). На стороне Azure, ключи объекта BLOB-объектов следовать правилам именования для [ C# идентификаторы](https://docs.microsoft.com/dotnet/csharp/language-reference/).

Как часть AzCopy `copy` можно указать значение для команды необязательно `s2s-invalid-metadata-handle` флаг, указывающий способ обработки файлов, где метаданные файла содержит несовместимые имена ключей. В следующей таблице каждое значение флага.

| Значение флага | Описание  |
|--------|-----------|
| **ExcludeIfInvalid** | (Вариант по умолчанию) Метаданные не включена в перемещаемый объект. AzCopy заносит в журнал предупреждение. |
| **FailIfInvalid** | Объекты не копируются. AzCopy заносит в журнал ошибку и включает эту ошибку в число неудач, который отображается в сводке передачи.  |
| **RenameIfInvalid**  | AzCopy разрешает недопустимые метаданные ключа и копирует объект в Azure с помощью пара значений ключа разрешить метаданные. Чтобы узнать, точно, какие действия принимает AzCopy для переименования объекта ключи, см. в разделе [как AzCopy переименовывает ключи объекта](#rename-logic) разделе ниже. Если AzCopy не удалось переименовать раздел, не будет скопирован объект. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>Как AzCopy переименовывает ключи объекта

AzCopy выполняет следующие действия.

1. Заменяет недопустимые символы «_».

2. Добавляет строку `rename_` в начале нового допустимый ключ.

   Этот ключ будет использоваться для сохранения исходных метаданных **значение**.

3. Добавляет строку `rename_key_` в начале нового допустимый ключ.
   Этот ключ будет использоваться для сохранения исходные метаданные недопустимый **ключ**.
   Этот ключ можно использовать для восстановления метаданных в стороне Azure, так как ключ метаданных сохраняется как значение в службе хранилища больших двоичных объектов.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные примеры можно найти в любой из следующих статей:

- [Начало работы с AzCopy](storage-use-azcopy-v10.md)

- [Передача данных с помощью AzCopy и BLOB-хранилища](storage-use-azcopy-blobs.md)

- [Передача данных с помощью AzCopy и хранилищем файлов](storage-use-azcopy-files.md)

- [Настроить, оптимизировать и устранение неполадок с AzCopy](storage-use-azcopy-configure.md)