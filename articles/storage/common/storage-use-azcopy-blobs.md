---
title: Передавать данные из хранилища BLOB-объектов Azure с помощью AzCopy v10 | Документация Майкрософт
description: В этой статье содержится коллекция AzCopy пример команды, которые помогут вам создавать контейнеры, скопируйте файлы и синхронизации папок между локальной файловой системы и контейнерами.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 98e33f838ee9b6f506bf1dc01e1dd61ad587aa05
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299410"
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

Можно использовать AzCopy `copy` команду, чтобы передать файлы и папки на локальном компьютере.

В этом разделе содержатся следующие примеры:

> [!div class="checklist"]
> * Отправка файла
> * Отправка папки
> * Отправка файлов с использованием подстановочных знаков

> [!NOTE]
> AzCopy не автоматически вычислить и сохранить хэш-код md5 файла. Если требуется AzCopy, чтобы сделать это, добавьте `--put-md5` флаг для каждой команды копирования. Таким образом, если большой двоичный объект загружается, AzCopy вычисляет хэш MD5 для загрузки данных и проверяет, что хэша MD5, сохраненного в большом двоичном объекте `Content-md5` свойство вычисленному хэшу.

### <a name="upload-a-file"></a>Отправка файла

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **Пример** | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **Пример** (иерархического пространства имен) | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> По умолчанию AzCopy передает данные в блочных BLOB-объектов. Для передачи файлов, как добавочных или страничных BLOB-объектов используйте флаг `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

### <a name="upload-a-folder"></a>Отправка папки

В этом примере копирует папки (и все файлы в этой папке) в контейнер больших двоичных объектов. Результатом является папкой в контейнере с тем же именем.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Пример** | `azcopy copy "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Пример** (иерархического пространства имен) | `azcopy copy "C:\myFolder" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

Чтобы скопировать в папку в контейнере, просто укажите имя папки в командной строке.

|    |     |
|--------|-----------|
| **Пример** | `azcopy copy "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder" --recursive` |
| **Пример** (иерархического пространства имен) | `azcopy copy "C:\myFolder" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder" --recursive` |

Если указать имя папки, которая не существует в контейнере, AzCopy создает новую папку с таким именем.

### <a name="upload-the-contents-of-a-folder"></a>Отправьте содержимое папки

Вы можете отправить содержимое папки без копирования папку, содержащую сам, используя подстановочный знак (*).

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "<local-folder-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<folder-path>` |
| **Пример** | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder"` |
| **Пример** (иерархического пространства имен) | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder"` |

> [!NOTE]
> Добавление `--recursive` флаг для отправки файлов во всех вложенных папках.

## <a name="download-files"></a>Скачивание файлов

Можно использовать AzCopy `copy` команду, чтобы скачать большие двоичные объекты, папок и контейнеров на локальный компьютер.

В этом разделе содержатся следующие примеры:

> [!div class="checklist"]
> * скачать файл;
> * Папка загрузки
> * Загрузка файлов с помощью подстановочных знаков

> [!NOTE]
> Если `Content-md5` значение свойства большого двоичного объекта содержит хэш, AzCopy вычисляет хэш MD5 для загруженных данных и проверяет, что хэша MD5, сохраненного в большом двоичном объекте `Content-md5` свойство вычисленному хэшу. Если эти значения не совпадают, скачивание завершится сбоем, если не переопределить это поведение, добавив `--check-md5=NoCheck` или `--check-md5=LogOnly` для команды copy.

### <a name="download-a-file"></a>скачать файл;

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **Пример** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myFolder\myTextFile.txt"` |
| **Пример** (иерархического пространства имен) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myFolder\myTextFile.txt"` |

### <a name="download-a-folder"></a>Папка загрузки

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<folder-path>" "<local-folder-path>" --recursive` |
| **Пример** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder "C:\myFolder"  --recursive` |
| **Пример** (иерархического пространства имен) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder "C:\myFolder"  --recursive` |

В этом примере результаты в папку с именем `C:\myFolder\myBlobFolder` , содержащий все загруженные файлы.

### <a name="download-the-contents-of-a-folder"></a>Загрузить содержимое папки

Можно загрузить содержимое папки без копирования папку, содержащую сам, используя подстановочный знак (*).

> [!NOTE]
> В настоящее время этот сценарий поддерживается только для учетных записей, у которых нет иерархического пространства имен.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-folder-path>/"` |
| **Пример** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder/*" "C:\myFolder"` |

> [!NOTE]
> Добавление `--recursive` флаг для загрузки файлов во всех вложенных папках.

## <a name="copy-blobs-between-storage-accounts"></a>Копирование больших двоичных объектов между учетными записями хранения

Можно использовать AzCopy для копирования больших двоичных объектов в другие учетные записи хранения. Операция копирования является синхронным, поэтому при команда возвращает, это означает, что все файлы были скопированы.

> [!NOTE]
> В настоящее время этот сценарий поддерживается только для учетных записей, у которых нет иерархического пространства имен.

AzCopy использует [поместить блок из URL-адрес](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API, поэтому данные копируются напрямую между серверами хранилища. Эти операции копирования не использовать пропускную способность сети компьютера.

В этом разделе содержатся следующие примеры:

> [!div class="checklist"]
> * Копирование большого двоичного объекта в другую учетную запись хранения
> * Скопируйте папку в другую учетную запись хранения
> * Копирование контейнерам в другую учетную запись хранения
> * Скопируйте все контейнеры, папки и файлы в другую учетную запись хранения

### <a name="copy-a-blob-to-another-storage-account"></a>Копирование большого двоичного объекта в другую учетную запись хранения

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **Пример** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-folder-to-another-storage-account"></a>Скопируйте папку в другую учетную запись хранения

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<folder-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<folder-path>" --recursive` |
| **Пример** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobFolder" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myBlobFolder" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>Копирование контейнерам в другую учетную запись хранения

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Пример** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-folders-and-files-to-another-storage-account"></a>Скопируйте все контейнеры, папки и файлы в другую учетную запись хранения

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **Пример** | `azcopy cp "https://mysourceaccount.blob.core.windows.net" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>Синхронизация файлов

Вы можете синхронизировать содержимое в локальной файловой системе, контейнер больших двоичных объектов. Вы также можете синхронизировать контейнер больших двоичных объектов, локальную файловую систему на компьютере. Синхронизация является односторонней. Другими словами можно выбрать эти две конечные точки, являющийся источником и какой из них является конечным.

> [!NOTE]
> Текущая версия AzCopy не будет синхронизировать между другие источники и назначения (например: Хранилище файлов или S3, Amazon Web Services (AWS) сегментов).

`sync` Команда сравнивает имена файлов и последнего изменения отметки времени. Задайте `--delete-destination` необязательный флаг в значение `true` или `prompt` на удаление файлов в папке назначения, если эти файлы больше не существуют в исходной папке.

Если задать `--delete-destination` флаг `true` AzCopy удаляет файлы без предоставления в строке. Запрос на предшествуют AzCopy удаляет файл, задайте `--delete-destination` флаг `prompt`.

> [!NOTE]
> Чтобы избежать случайного удаления, не забудьте включить [обратимое удаление](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) компонентов, прежде чем использовать `--delete-destination=prompt|true` флаг.

### <a name="synchronize-a-container-to-a-local-file-system"></a>Синхронизировать контейнер локальную файловую систему

В этом случае в локальной файловой системе становится источником, и контейнер является конечным.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy sync "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Пример** | `azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Пример** (иерархического пространства имен) | `azcopy sync "C:\myFolder" "https://<storage-account-name>.dfs.core.windows.net/mycontainer" --recursive` |


### <a name="synchronize-a-local-file-system-to-a-container"></a>Синхронизация локальной файловой системе в контейнер

В этом случае контейнер становится источником и локальной файловой системе является конечным.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myFolder" --recursive` |
| **Пример** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myFolder" --recursive` |
| **Пример** (иерархического пространства имен) | `azcopy sync "https://mystorageaccount.dfs.core.windows.net/mycontainer" "C:\myFolder" --recursive` |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные примеры можно найти в любой из следующих статей:

- [Начало работы с AzCopy](storage-use-azcopy-v10.md)

- [Учебник. Миграция локальных данных в Облачное хранилище с помощью AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Передача данных с помощью AzCopy и хранилищем файлов](storage-use-azcopy-files.md)

- [Передача данных с помощью AzCopy и Amazon S3 сегментов](storage-use-azcopy-s3.md)

- [Настроить, оптимизировать и устранение неполадок с AzCopy](storage-use-azcopy-configure.md)