---
title: Передавать данные из хранилища BLOB-объектов Azure с помощью AzCopy v10 | Документация Майкрософт
description: В этой статье содержится коллекция AzCopy пример команды, помогающие создавать контейнеры, копирования файлов и синхронизация каталогов между локальной файловой системы и контейнерами.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: f95af348eb11abee5a46a89e08da5bf4eb873c42
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566145"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Передача данных с помощью AzCopy и BLOB-хранилища

AzCopy — программа командной строки, который можно использовать для копирования данных, из или между учетными записями хранения. Эта статья содержит пример команды, работающие с хранилищем BLOB-объектов.

## <a name="get-started"></a>Начало работы

См. в разделе [начало работы с AzCopy](storage-use-azcopy-v10.md) статью, чтобы скачать AzCopy и узнайте о том, что может предоставить учетные данные авторизации в службе хранилища.

> [!NOTE]
> В примерах в этой статье предполагается, что вы прошли проверку подлинности личности с помощью `AzCopy login` команды. AzCopy использует учетную запись Azure AD для авторизации доступа к данным в хранилище BLOB-объектов.
>
> Если вы предпочитаете использовать маркер SAS для авторизации доступа к данным больших двоичных объектов, можно добавить этот маркер на URL-адрес ресурса в каждой команде AzCopy.
>
> Например, `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"`.

## <a name="create-a-container"></a>Создание контейнера

Можно использовать AzCopy `make` команду, чтобы создать контейнер. В примерах в этом разделе создается контейнер с именем `mycontainer`.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **Пример** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **Пример** (иерархического пространства имен) | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>Upload files

Можно использовать AzCopy `copy` команду, чтобы передать файлы и каталоги на локальном компьютере.

В этом разделе содержатся следующие примеры:

> [!div class="checklist"]
> * Отправка файла
> * Отправить каталог
> * Отправка файлов с использованием подстановочных знаков

> [!NOTE]
> AzCopy не автоматически вычислить и сохранить хэш-код md5 файла. Если требуется AzCopy, чтобы сделать это, добавьте `--put-md5` флаг для каждой команды копирования. Таким образом, если большой двоичный объект загружается, AzCopy вычисляет хэш MD5 для загрузки данных и проверяет, что хэша MD5, сохраненного в большом двоичном объекте `Content-md5` свойство вычисленному хэшу.

### <a name="upload-a-file"></a>Отправка файла

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **Пример** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **Пример** (иерархического пространства имен) | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> По умолчанию AzCopy передает данные в блочных BLOB-объектов. Для передачи файлов, как добавочных или страничных BLOB-объектов используйте флаг `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

### <a name="upload-a-directory"></a>Отправить каталог

В этом примере копирует каталог (и все файлы в этом каталоге) в контейнер больших двоичных объектов. Результат представляет собой каталог в контейнере с тем же именем.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Пример** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Пример** (иерархического пространства имен) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

Чтобы скопировать каталог в контейнере, просто укажите имя этого каталога в командной строке.

|    |     |
|--------|-----------|
| **Пример** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |
| **Пример** (иерархического пространства имен) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

Если указать имя каталога, который не существует в контейнере, AzCopy создаст новый каталог с таким именем.

### <a name="upload-the-contents-of-a-directory"></a>Отправьте содержимое папки

Вы можете отправить содержимое каталога без копирования каталога, содержащего сам, используя подстановочный знак (*).

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "<local-directory-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **Пример** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory"` |
| **Пример** (иерархического пространства имен) | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory"` |

> [!NOTE]
> Добавление `--recursive` флаг для отправки файлов во всех вложенных каталогах.

## <a name="download-files"></a>Скачивание файлов

Можно использовать AzCopy `copy` команду, чтобы скачать большие двоичные объекты, каталоги и контейнеры для локального компьютера.

В этом разделе содержатся следующие примеры:

> [!div class="checklist"]
> * скачать файл;
> * Скачать каталог
> * Загрузка файлов с помощью подстановочных знаков

> [!NOTE]
> Если `Content-md5` значение свойства большого двоичного объекта содержит хэш, AzCopy вычисляет хэш MD5 для загруженных данных и проверяет, что хэша MD5, сохраненного в большом двоичном объекте `Content-md5` свойство вычисленному хэшу. Если эти значения не совпадают, скачивание завершится сбоем, если не переопределить это поведение, добавив `--check-md5=NoCheck` или `--check-md5=LogOnly` для команды copy.

### <a name="download-a-file"></a>скачать файл;

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **Пример** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |
| **Пример** (иерархического пространства имен) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Скачать каталог

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>" "<local-directory-path>" --recursive` |
| **Пример** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |
| **Пример** (иерархического пространства имен) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |

В этом примере результаты в каталог с именем `C:\myDirectory\myBlobDirectory` , содержащий все загруженные файлы.

### <a name="download-the-contents-of-a-directory"></a>Загрузить содержимое каталога

Можно загрузить содержимое каталога без копирования каталога, содержащего сам, используя подстановочный знак (*).

> [!NOTE]
> В настоящее время этот сценарий поддерживается только для учетных записей, у которых нет иерархического пространства имен.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` |
| **Пример** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*" "C:\myDirectory"` |

> [!NOTE]
> Добавление `--recursive` флаг для загрузки файлов во всех вложенных каталогах.

## <a name="copy-blobs-between-storage-accounts"></a>Копирование больших двоичных объектов между учетными записями хранения

Можно использовать AzCopy для копирования больших двоичных объектов в другие учетные записи хранения. Операция копирования является синхронным, поэтому при команда возвращает, это означает, что все файлы были скопированы.

> [!NOTE]
> В настоящее время этот сценарий поддерживается только для учетных записей, у которых нет иерархического пространства имен. 

AzCopy использует [поместить блок из URL-адрес](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API, поэтому данные копируются напрямую между серверами хранилища. Эти операции копирования не использовать пропускную способность сети компьютера.

В этом разделе содержатся следующие примеры:

> [!div class="checklist"]
> * Копирование большого двоичного объекта в другую учетную запись хранения
> * Копирование каталога в другой учетной записи хранения
> * Копирование контейнерам в другую учетную запись хранения
> * Скопируйте все контейнеры, каталоги и файлы в другую учетную запись хранения

> [!NOTE]
> В текущем выпуске необходимо добавить маркер SAS для каждого URL-адрес источника. Если вы предоставите учетные данные авторизации с помощью Azure Active Directory (AD), можно опустить маркер SAS только из URL-адрес назначения. 

### <a name="copy-a-blob-to-another-storage-account"></a>Копирование большого двоичного объекта в другую учетную запись хранения

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **Пример** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-directory-to-another-storage-account"></a>Копирование каталога в другой учетной записи хранения

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>" --recursive` |
| **Пример** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>Копирование контейнерам в другую учетную запись хранения

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Пример** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-directories-and-files-to-another-storage-account"></a>Скопируйте все контейнеры, каталоги и файлы в другую учетную запись хранения

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **Пример** | `azcopy cp "https://mysourceaccount.blob.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>Синхронизация файлов

Вы можете синхронизировать содержимое в локальной файловой системе с контейнер больших двоичных объектов. Синхронизация является односторонней. Другими словами можно выбрать эти две конечные точки, являющийся источником и какой из них является конечным.

> [!NOTE]
> В настоящее время этот сценарий поддерживается только для учетных записей, у которых нет иерархического пространства имен. Текущая версия AzCopy не будет синхронизировать между другие источники и назначения (например: Хранилище файлов или S3, Amazon Web Services (AWS) сегментов).

`sync` Команда сравнивает имена файлов и последнего изменения отметки времени. Задайте `--delete-destination` необязательный флаг в значение `true` или `prompt` для удаления файлов в каталоге назначения в том случае, если эти файлы больше не существуют в исходном каталоге.

Если задать `--delete-destination` флаг `true` AzCopy удаляет файлы без предоставления в строке. Запрос на предшествуют AzCopy удаляет файл, задайте `--delete-destination` флаг `prompt`.

> [!NOTE]
> Чтобы избежать случайного удаления, не забудьте включить [обратимое удаление](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) компонентов, прежде чем использовать `--delete-destination=prompt|true` флаг.

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Обновление контейнера с изменениями в локальной файловой системе

В этом случае контейнер является конечным, и локальной файловой системе является источником.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy sync "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Пример** | `azcopy sync "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Обновление локальной файловой системе с изменениями в контейнер

В этом случае копируются в локальную файловую систему, а контейнер является источником.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myDirectory" --recursive` |
| **Пример** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myDirectory" --recursive` |
|

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные примеры можно найти в любой из следующих статей:

- [Get started with AzCopy](storage-use-azcopy-v10.md) (Начало работы с AzCopy)

- [Учебник. Миграция локальных данных в Облачное хранилище с помощью AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md) (Передача данных с помощью AzCopy и хранилища файлов)

- [Передача данных с помощью AzCopy и контейнеров Amazon S3](storage-use-azcopy-s3.md)

- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md) (Настройка, оптимизация и устранение неполадок с AzCopy)