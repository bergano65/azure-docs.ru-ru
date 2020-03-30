---
title: Копирование данных от Amazon S3 до хранилища Azure с помощью AzCopy Документы Майкрософт
description: Передача данных с помощью AzCopy и контейнеров Amazon S3
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: a3180593eaf8c01c772fd761d88b5f5b9f7657ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941503"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>Копирование данных от Amazon S3 до azure Storage с помощью AzCopy

AzCopy — это утилита командной строки, которую можно использовать для копирования капли или файлов в учетную запись или из учетной записи хранения. Эта статья поможет скопировать объекты, каталоги и ведра от Amazon Web Services (AWS) S3 до хранилища azure blob с помощью AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Выберите, как вы будете предоставлять учетные данные авторизации

* Чтобы авторизоваться с хранилищем Azure, используйте маркер Active Directory (AD) или маркер общей подписи доступа (SAS).

* Чтобы авторизоваться с AWS S3, используйте ключ доступа AWS и секретный ключ доступа.

### <a name="authorize-with-azure-storage"></a>Разрешить с помощью хранилища Azure

Просмотрите статью Get Started со статьей [AzCopy,](storage-use-azcopy-v10.md) чтобы загрузить AzCopy, и выберите, как вы предоставите учетные данные авторизации службе хранения.

> [!NOTE]
> Примеры в этой статье предполагают, что вы подтвердили `AzCopy login` подлинность своей личности с помощью команды. Затем AzCopy использует учетную запись Azure AD для авторизации доступа к данным в хранилище Blob.
>
> Если вы предпочитаете использовать токен SAS для авторизации доступа к данным blob, то вы можете приобщить этот маркер к URL-адресу ресурса в каждой команде AzCopy.
>
> Например: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Авторизация с AWS S3

Соберите ключ доступа AWS и секретный ключ доступа, а затем установите следующие переменные среды:

| Операционная система | Команда  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Macos** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Копирование объектов, каталогов и ведер

AzCopy использует [блок Put From URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API, поэтому данные копируются непосредственно между серверами AWS S3 и серверами хранения. Эти операции копирования не используют пропускную способность сети вашего компьютера.

> [!IMPORTANT]
> Эта функция в настоящее время находится на стадии предварительной версии. Если вы решили удалить данные из ведер S3 после операции копирования, убедитесь, что данные были надлежащим образом скопированы на вашу учетную запись хранения, прежде чем удалить данные.

> [!TIP]
> Примеры в этом разделе приложить аргументы пути с отдельными цитатами ('). Используйте одиночные кавычки во всех командных оболочках, за исключением оболочки командования Windows (cmd.exe). Если вы используете оболочку командования Windows (cmd.exe), приложить аргументы пути с двойными кавычками ("") вместо отдельных котировок (').

 Эти примеры также работают с учетными записями, которые имеют иерархическое пространство имен. [Многопротоколный доступ к хранению data Lake](../blobs/data-lake-storage-multi-protocol-access.md) позволяет использовать`blob.core.windows.net`тот же синтаксис URL-адреса () на этих учетных записях. 

### <a name="copy-an-object"></a>Копирование объекта

Используйте тот же`blob.core.windows.net`синтаксис URL () для учетных записей, которые имеют иерархическое пространство имен.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Пример** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> Примеры в этой статье используют URL-адреса в стиле пути `http://s3.amazonaws.com/<bucket-name>`для ведер AWS S3 (например: ). 
>
> Вы также можете использовать виртуальные URL-адреса в `http://bucket.s3.amazonaws.com`стиле хостов (например: ). 
>
> Чтобы узнать больше о виртуальном хостинге ведер,https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html)см.

### <a name="copy-a-directory"></a>Копирование каталога

Используйте тот же`blob.core.windows.net`синтаксис URL () для учетных записей, которые имеют иерархическое пространство имен.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Пример** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Пример** (иерархическое пространство имен)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>Копировать ведро

Используйте тот же`blob.core.windows.net`синтаксис URL () для учетных записей, которые имеют иерархическое пространство имен.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **Пример** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |
| **Пример** (иерархическое пространство имен)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Копирование всех ведер во всех регионах

Используйте тот же`blob.core.windows.net`синтаксис URL () для учетных записей, которые имеют иерархическое пространство имен.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Пример** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Пример** (иерархическое пространство имен)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Копирование всех ведер в определенном регионе S3

Используйте тот же`blob.core.windows.net`синтаксис URL () для учетных записей, которые имеют иерархическое пространство имен.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Пример** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Пример** (иерархическое пространство имен)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Обработка различий в правилах именования объектов

AWS S3 имеет другой набор именования конвенций для имен ведра по сравнению с контейнерами Azure blob. Вы можете прочитать о них [здесь](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Если вы решите скопировать группу ведер в учетную запись хранения Azure, операция копирования может выйти из строя из-за различий в именовании.

AzCopy обрабатывает две наиболее распространенные проблемы, которые могут возникнуть; ведра, содержащие периоды и ведра, содержащие последовательные дефисы. Имена ведра AWS S3 могут содержать периоды и последовательные дефисы, но контейнер в Azure не может. AzCopy заменяет периоды с дефисами и последовательными дефисами с числом, которое представляет число последовательных дефисов (Например: ведро с именем `my----bucket` становится `my-4-bucket`. 

Кроме того, по мере того, как AzCopy копирует файлы, она проверяет их именования и попытки их устранения. Например, если есть ведра `bucket-name` `bucket.name`с именем и , `bucket.name` AzCopy разрешает ведро, названное сначала, `bucket-name` а затем `bucket-name-2`.

## <a name="handle-differences-in-object-metadata"></a>Обработка различий в метадатах объектов

AWS S3 и Azure разрешают различные наборы символов в названиях ключей объектов. Вы можете прочитать о символах, которые использует AWS S3 [здесь](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys). На стороне Azure клавиши объекта blob придерживаются правил именования [идентификаторов C.](https://docs.microsoft.com/dotnet/csharp/language-reference/)

Как часть команды `copy` AzCopy можно предоставить значение `s2s-invalid-metadata-handle` для дополнительного флага, которое определяет, как вы хотели бы обрабатывать файлы, где метаданные файла содержат несовместимые имена ключей. В следующей таблице описывается значение каждого флага.

| Значение флага | Описание  |
|--------|-----------|
| **ИсключитьИФНедействительное** | (Опция по умолчанию) Метаданные не включены в переданный объект. AzCopy регистрирует предупреждение. |
| **FailIfInvalid** | Объекты не копируется. AzCopy регистрирует ошибку и включает ошибку в сбой, который отображается в резюме передачи.  |
| **Переименование**  | AzCopy разрешает недействительный ключ метаданных и копирует объект в Azure с помощью разослатой пары ключей метаданных. Чтобы узнать, какие именно шаги предпринимает AzCopy для переименования ключей объекта, см. [How AzCopy renames object keys](#rename-logic) Если AzCopy не может переименовать ключ, то объект не будет скопирован. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>Как AzCopy переименовывает клавиши объектов

AzCopy выполняет следующие действия:

1. Заменяет недействительные символы на «я».

2. Добавляет строку `rename_` к началу нового действительного ключа.

   Этот ключ будет использоваться для сохранения исходного **значения**метаданных.

3. Добавляет строку `rename_key_` к началу нового действительного ключа.
   Этот ключ будет использоваться для сохранения исходного недействительного **ключа**метаданных.
   Этот ключ можно использовать для восстановления метаданных в стороне Azure, так как ключ метаданных сохраняется как значение службы хранения Blob.

## <a name="next-steps"></a>Дальнейшие действия

Найдите больше примеров в любой из этих статей:

- [Get started with AzCopy](storage-use-azcopy-v10.md) (Начало работы с AzCopy)

- [Transfer data with AzCopy and blob storage](storage-use-azcopy-blobs.md) (Передача данных с помощью AzCopy и хранилища BLOB-объектов)

- [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md) (Передача данных с помощью AzCopy и хранилища файлов)

- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md) (Настройка, оптимизация и устранение неполадок с AzCopy)