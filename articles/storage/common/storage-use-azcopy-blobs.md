---
title: Перенос данных в хранилище BLOB-объектов Azure или из него с помощью AzCopy V10 | Документация Майкрософт
description: Эта статья содержит набор AzCopy примеров команд, помогающих создавать контейнеры, копировать файлы и синхронизировать каталоги между локальными файловыми системами и контейнерами.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 2ab561c387467086a40aa6676af347a107c2c452
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68641117"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Перенос данных с помощью AzCopy и хранилища BLOB-объектов

AzCopy — это служебная программа командной строки, которую можно использовать для копирования данных в учетные записи хранения, из или между ними. Эта статья содержит примеры команд, которые работают с хранилищем BLOB-объектов.

## <a name="get-started"></a>Начало работы

Ознакомьтесь с статьей начало [работы с AzCopy](storage-use-azcopy-v10.md) , чтобы скачать AzCopy и узнать о способах предоставления учетных данных авторизации в службе хранилища.

> [!NOTE]
> В примерах в этой статье предполагается, что вы проверили подлинность удостоверения с помощью `AzCopy login` команды. Затем AzCopy использует учетную запись Azure AD для авторизации доступа к данным в хранилище BLOB-объектов.
>
> Если вы предпочитаете использовать маркер SAS для авторизации доступа к данным большого двоичного объекта, можно добавить этот маркер к URL-адресу ресурса в каждой команде AzCopy.
>
> Например, `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"`.

## <a name="create-a-container"></a>Создать контейнер

Для создания контейнера можно использовать `make` команду AzCopy. В примерах этого раздела создается контейнер с именем `mycontainer`.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **Пример** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **Пример** (иерархическое пространство имен) | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>Upload files

Для отправки файлов и каталогов `copy` с локального компьютера можно использовать команду AzCopy.

В этом разделе содержатся следующие примеры:

> [!div class="checklist"]
> * Отправить файл
> * Отправка каталога
> * Отправка файлов с помощью подстановочных знаков

> [!NOTE]
> AzCopy не вычисляет и не сохраняет хэш-код MD5 для файла автоматически. Если вы хотите, чтобы AzCopy это сделать, добавьте `--put-md5` флаг к каждой команде копирования. Таким образом, при скачивании большого двоичного объекта AzCopy вычисляет хэш MD5 для скачанных данных и проверяет, соответствует ли хэш MD5, хранящийся в `Content-md5` свойстве большого двоичного объекта, соответствующему вычисляемому хэшу.

### <a name="upload-a-file"></a>Отправить файл

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **Пример** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **Пример** (иерархическое пространство имен) | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> AzCopy по умолчанию передает данные в блочные BLOB-объекты. Для отправки файлов в качестве добавочных больших двоичных объектов, или `--blob-type=[BlockBlob|PageBlob|AppendBlob]`страничные BLOB-объекты используют флаг.

### <a name="upload-a-directory"></a>Отправка каталога

В этом примере каталог (и все файлы в этом каталоге) копируется в контейнер больших двоичных объектов. Результатом является каталог в контейнере с тем же именем.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Пример** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Пример** (иерархическое пространство имен) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

Чтобы скопировать в каталог в контейнере, просто укажите имя этого каталога в командной строке.

|    |     |
|--------|-----------|
| **Пример** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |
| **Пример** (иерархическое пространство имен) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

Если указать имя каталога, который не существует в контейнере, AzCopy создает новый каталог с этим именем.

### <a name="upload-the-contents-of-a-directory"></a>Отправка содержимого каталога

Вы можете отправить содержимое каталога, не копируя сам каталог с помощью подстановочного знака (*).

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "<local-directory-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **Пример** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory"` |
| **Пример** (иерархическое пространство имен) | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory"` |

> [!NOTE]
> `--recursive` Добавьте флаг для отправки файлов во все подкаталоги.

## <a name="download-files"></a>Скачивание файлов

Для загрузки больших двоичных `copy` объектов, каталогов и контейнеров на локальный компьютер можно использовать команду AzCopy.

В этом разделе содержатся следующие примеры:

> [!div class="checklist"]
> * Скачать файл
> * Загрузка каталога
> * Загрузка файлов с помощью подстановочных знаков

> [!NOTE]
> Если значение `Content-md5` свойства большого двоичного объекта содержит хэш, AzCopy вычисляет хэш MD5 для скачанных данных и проверяет, соответствует ли хэш MD5, хранящийся в свойстве большого двоичного объекта, соответствующему вычисляемому хэшу. `Content-md5` Если эти значения не совпадают, загрузка завершается ошибкой, если это поведение не переопределено `--check-md5=LogOnly` путем добавления `--check-md5=NoCheck` или к команде Copy.

### <a name="download-a-file"></a>Скачать файл

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **Пример** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |
| **Пример** (иерархическое пространство имен) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Загрузка каталога

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>" "<local-directory-path>" --recursive` |
| **Пример** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |
| **Пример** (иерархическое пространство имен) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |

В этом примере создается каталог с именем `C:\myDirectory\myBlobDirectory` , который содержит все скачанные файлы.

### <a name="download-the-contents-of-a-directory"></a>Загрузка содержимого каталога

Вы можете скачать содержимое каталога, не копируя сам каталог с помощью подстановочного знака (*).

> [!NOTE]
> В настоящее время этот сценарий поддерживается только для учетных записей, у которых нет иерархического пространства имен.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` |
| **Пример** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*" "C:\myDirectory"` |

> [!NOTE]
> `--recursive` Добавьте флаг для скачивания файлов во всех подкаталогах.

## <a name="copy-blobs-between-storage-accounts"></a>Копирование больших двоичных объектов между учетными записями хранения

AzCopy можно использовать для копирования BLOB-объектов в другие учетные записи хранения. Операция копирования является синхронной, поэтому когда команда возвращает значение, это означает, что все файлы скопированы.

> [!NOTE]
> В настоящее время этот сценарий поддерживается только для учетных записей, у которых нет иерархического пространства имен. 

AzCopy использует [API](https://docs.microsoft.com/en-us/rest/api/storageservices/put-page-from-url)" [сервер-сервер](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) ", поэтому данные копируются непосредственно между серверами хранилища. Эти операции копирования не используют пропускную способность сети компьютера.

В этом разделе содержатся следующие примеры:

> [!div class="checklist"]
> * Копирование большого двоичного объекта в другую учетную запись хранения
> * Копирование каталога в другую учетную запись хранения
> * Копирование контейнеров в другую учетную запись хранения
> * Копировать все контейнеры, каталоги и файлы в другую учетную запись хранения

> [!NOTE]
> В текущем выпуске необходимо добавить маркер SAS к каждому исходному URL-адресу. Если вы предоставляете учетные данные авторизации с помощью Azure Active Directory (AD), маркер SAS можно опустить только из URL-адреса назначения. 

### <a name="copy-a-blob-to-another-storage-account"></a>Копирование большого двоичного объекта в другую учетную запись хранения

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **Пример** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-directory-to-another-storage-account"></a>Копирование каталога в другую учетную запись хранения

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Пример** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>Копирование контейнеров в другую учетную запись хранения

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Пример** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-directories-and-files-to-another-storage-account"></a>Копировать все контейнеры, каталоги и файлы в другую учетную запись хранения

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **Пример** | `azcopy cp "https://mysourceaccount.blob.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>Синхронизировать файлы

Вы можете синхронизировать содержимое локальной файловой системы с контейнером больших двоичных объектов. Синхронизация является односторонней. Иными словами, вы выбираете, какие из этих двух конечных точек являются источником, и какой из них является назначением.

> [!NOTE]
> В настоящее время этот сценарий поддерживается только для учетных записей, у которых нет иерархического пространства имен. Текущий выпуск AzCopy не синхронизируется между другими источниками и назначениями (например: Хранилище файлов или Amazon Web Services (AWS) — контейнеры S3).

`sync` Команда сравнивает имена файлов и метки времени последнего изменения. Установите необязательный флаг в `true` значение или `prompt` , чтобы удалить файлы в целевом каталоге, если эти файлы больше не существуют в исходном каталоге. `--delete-destination`

Если установить `--delete-destination` для `true` флага значение AzCopy, удаляются файлы без указания запроса. Если требуется, чтобы запрос отображался перед тем, как AzCopy удалит файл, `--delete-destination` установите для `prompt`флага значение.

> [!NOTE]
> Чтобы предотвратить случайное удаление, обязательно включите функцию обратимого [удаления](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) , прежде чем использовать `--delete-destination=prompt|true` флаг.

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Обновление контейнера с изменениями в локальной файловой системе

В этом случае контейнер является назначением, а локальная файловая система — источником.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy sync "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Пример** | `azcopy sync "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Обновление локальной файловой системы изменениями в контейнере

В этом случае локальная файловая система является назначением, а контейнер — источником.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myDirectory" --recursive` |
| **Пример** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myDirectory" --recursive` |
|

## <a name="next-steps"></a>Следующие шаги

Дополнительные примеры приведены в любой из следующих статей:

- [Get started with AzCopy](storage-use-azcopy-v10.md) (Начало работы с AzCopy)

- [Учебник. Перенос локальных данных в облачное хранилище с помощью AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md) (Передача данных с помощью AzCopy и хранилища файлов)

- [Передача данных с помощью AzCopy и контейнеров Amazon S3](storage-use-azcopy-s3.md)

- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md) (Настройка, оптимизация и устранение неполадок с AzCopy)
