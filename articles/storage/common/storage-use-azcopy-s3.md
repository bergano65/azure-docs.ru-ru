---
title: Перенос данных в хранилище Azure из Amazon S3 контейнеров с помощью AzCopy V10 | Документация Майкрософт
description: Перенос данных с помощью AzCopy и Amazon S3 контейнеров
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 633d960e430d035a187a402e664c70f27cd23756
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648741"
---
# <a name="copy-data-from-amazon-s3-buckets-by-using-azcopy"></a>Копирование данных из Amazon S3 контейнеров с помощью AzCopy

AzCopy — это служебная программа командной строки, которую можно использовать для копирования больших двоичных объектов или файлов в учетную запись хранения или из нее. Эта статья поможет вам скопировать объекты, каталоги и контейнеры из Amazon Web Services (AWS) S3 в хранилище BLOB-объектов Azure с помощью AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Выбор порядка предоставления учетных данных для авторизации

* Для авторизации в службе хранилища Azure используйте Azure Active Directory (AD) или маркер подписанного URL-адрес (SAS).

* Для авторизации с помощью AWS S3 используйте ключ доступа AWS и секретный ключ доступа.

### <a name="authorize-with-azure-storage"></a>Авторизация в службе хранилища Azure

Ознакомьтесь с статьей начало [работы с AzCopy](storage-use-azcopy-v10.md) , чтобы скачать AzCopy, и выберите способ предоставления учетных данных авторизации для службы хранилища.

> [!NOTE]
> В примерах в этой статье предполагается, что вы проверили подлинность удостоверения с помощью `AzCopy login` команды. Затем AzCopy использует учетную запись Azure AD для авторизации доступа к данным в хранилище BLOB-объектов.
>
> Если вы предпочитаете использовать маркер SAS для авторизации доступа к данным большого двоичного объекта, можно добавить этот маркер к URL-адресу ресурса в каждой команде AzCopy.
>
> Например, `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Авторизация с помощью AWS S3

Соберите ключ доступа AWS и секретный ключ доступа, а затем задайте следующие переменные среды:

| Операционная система | Command  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Копирование объектов, каталогов и контейнеров

AzCopy использует [блок размещения из API URL-адреса](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) , поэтому данные копируются непосредственно между AWS S3 и серверами хранилища. Эти операции копирования не используют пропускную способность сети компьютера.

> [!IMPORTANT]
> Эта функция в настоящее время находится на стадии предварительной версии. Если вы решили удалить данные из контейнеров S3 после операции копирования, убедитесь, что данные были правильно скопированы в учетную запись хранения перед удалением данных.

### <a name="copy-an-object"></a>Копирование объекта

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "https://s3.amazonaws.com/<bucket-name>/<object-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>"` |
| **Пример** | `azcopy copy "https://s3.amazonaws.com/mybucket/myobject" "https://mystorageaccount.blob.core.windows.net/mycontainer/myblob"` |

> [!NOTE]
> В примерах в этой статье используются URL-адреса в стиле пути для контейнеров AWS S3 ( `http://s3.amazonaws.com/<bucket-name>`например:). 
>
> Кроме того, можно использовать виртуальные URL-адреса в стиле (например: `http://bucket.s3.amazonaws.com`). 
>
> Дополнительные сведения о виртуальном размещении сегментов см. в разделе [виртуальное размещение сегментов]] (https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html).

### <a name="copy-a-directory"></a>Копирование каталога

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "https://s3.amazonaws.com/<bucket-name>/<directory-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>" --recursive=true` |
| **Пример** | `azcopy copy "https://s3.amazonaws.com/mybucket/mydirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory" --recursive=true` |

### <a name="copy-a-bucket"></a>Копирование контейнера

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "https://s3.amazonaws.com/<bucket-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true` |
| **Пример** | `azcopy copy "https://s3.amazonaws.com/mybucket" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Копировать все контейнеры во всех регионах

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "https://s3.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Пример** | `azcopy copy "https://s3.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Копирование всех контейнеров в заданном регионе S3

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "https://s3-<region-name>.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Пример** | `azcopy copy "https://s3-rds.eu-north-1.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Обработку различий в правилах именования объектов

AWS S3 имеет разный набор соглашений об именовании для имен контейнеров по сравнению с контейнерами больших двоичных объектов Azure. Сведения о них можно прочитать [здесь](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Если вы решили скопировать группу контейнеров в учетную запись хранения Azure, операция копирования может завершиться ошибкой из-за различий в именах.

AzCopy обрабатывает две наиболее распространенные проблемы, которые могут возникнуть. контейнеры, содержащие периоды и сегменты, которые содержат последовательные дефисы. Имена контейнеров S3 AWS могут содержать точки и последовательные дефисы, но контейнер в Azure не может. AzCopy заменяет точки дефисами и последовательными дефисами числом, представляющим количество последовательных дефисов (например, контейнер с именем `my----bucket` преобразуется `my-4-bucket`в. 

Кроме того, так как AzCopy выполняет копирование файлов, он проверяет наличие конфликтов именования и пытается разрешить их. Например, если есть `bucket-name` контейнеры с именем и `bucket.name`, AzCopy разрешает контейнер с именем `bucket.name` First в `bucket-name` и затем в `bucket-name-2`.

## <a name="handle-differences-in-object-metadata"></a>Обработку различий в метаданных объекта

AWS S3 и Azure позволяют использовать разные наборы символов в именах объектных ключей. Вы можете ознакомиться с символами, которые использует AWS S3 [здесь](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys). На стороне Azure ключи объектов большого двоичного объекта соответствуют правилам именования [ C# идентификаторов](https://docs.microsoft.com/dotnet/csharp/language-reference/).

В составе команды AzCopy `copy` можно указать `s2s-invalid-metadata-handle` необязательный флаг, указывающий, как вы хотите управлять файлами, в которых метаданные файла содержат несовместимые имена ключей. В следующей таблице описывается каждое значение флага.

| Значение флага | Описание  |
|--------|-----------|
| **ексклудеифинвалид** | (Параметр по умолчанию) Метаданные не включаются в передаваемый объект. AzCopy регистрирует предупреждение. |
| **фаилифинвалид** | Объекты не копируются. AzCopy регистрирует ошибку и включает эту ошибку в число сбоев, которое отображается в сводке по перемещению.  |
| **ренамеифинвалид**  | AzCopy разрешает недопустимый ключ метаданных и копирует объект в Azure с помощью разрешенной пары "ключ — значение метаданных". Чтобы узнать, какие действия AzCopy для переименования объектных ключей, ознакомьтесь с разделом [как AzCopy переименовывает ключи объектов](#rename-logic) ниже. Если AzCopy не удается переименовать ключ, объект не будет скопирован. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>Как AzCopy переименовывает объектные ключи

AzCopy выполняет следующие действия:

1. Заменяет недопустимые символы символом "_".

2. Добавляет строку `rename_` в начало нового допустимого ключа.

   Этот ключ будет использоваться для сохранения исходного **значения**метаданных.

3. Добавляет строку `rename_key_` в начало нового допустимого ключа.
   Этот ключ будет использоваться для сохранения исходных метаданных недопустимого **ключа**.
   Этот ключ можно использовать для попыток восстановления метаданных на стороне Azure, так как ключ метаданных сохраняется как значение в службе хранилища BLOB-объектов.

## <a name="next-steps"></a>Следующие шаги

Дополнительные примеры приведены в любой из следующих статей:

- [Get started with AzCopy](storage-use-azcopy-v10.md) (Начало работы с AzCopy)

- [Transfer data with AzCopy and blob storage](storage-use-azcopy-blobs.md) (Передача данных с помощью AzCopy и хранилища BLOB-объектов)

- [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md) (Передача данных с помощью AzCopy и хранилища файлов)

- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md) (Настройка, оптимизация и устранение неполадок с AzCopy)