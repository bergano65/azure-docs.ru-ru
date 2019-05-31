---
title: Передавать данные из службы файлов Azure с помощью AzCopy v10 | Документация Майкрософт
description: Передача данных с помощью AzCopy и хранилищем файлов.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 5d11d5c0da350b9abf2e2b06a11a1690cf3f6922
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247114"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Передача данных с помощью AzCopy и хранилищем файлов 

AzCopy — программа командной строки, который можно использовать для копирования больших двоичных объектов или файлов из учетной записи хранения. Эта статья содержит примеры команд, которые работают с файлами Azure.

Перед началом работы ознакомьтесь с разделом [начало работы с AzCopy](storage-use-azcopy-v10.md) статью, чтобы скачать AzCopy и ознакомиться со средством.

## <a name="create-file-shares"></a>Создание файловых ресурсов

Можно использовать AzCopy `make` команду, чтобы создать общую папку. В примере в этом разделе создается файловый ресурс с именем `myfileshare`.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **Пример** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>Upload files

Можно использовать AzCopy `copy` команду, чтобы передать файлы и папки на локальном компьютере.

В этом разделе содержатся следующие примеры:

> [!div class="checklist"]
> * Отправка файла
> * Отправка папки
> * Отправка файлов с использованием подстановочных знаков

> [!NOTE]
> AzCopy не автоматически вычислить и сохранить хэш-код md5 файла. Если требуется AzCopy, чтобы сделать это, добавьте `--put-md5` флаг для каждой команды копирования. Таким образом, когда загружается файл, AzCopy вычисляет хэш MD5 для загрузки данных и проверяет, что хэша MD5, сохраненного в файле `Content-md5` свойство вычисленному хэшу.

### <a name="upload-a-file"></a>Отправка файла

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **Пример** | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-folder"></a>Отправка папки

Этот пример копирует папки (и все файлы в этой папке) для общей папки. Результат — это папка, в общую папку с тем же именем.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "<local-folder-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **Пример** | `azcopy copy "C:\myFolder" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Чтобы скопировать в папку внутри общей папки, укажите имя этой папки в командной строке.

|    |     |
|--------|-----------|
| **Пример** | `azcopy copy "C:\myFolder" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Если указать имя папки, которая не существует в общей папке, AzCopy создает новую папку с таким именем.

### <a name="upload-the-contents-of-a-folder"></a>Отправьте содержимое папки

Вы можете отправить содержимое папки без копирования папку, содержащую сам, используя подстановочный знак (*).

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "<local-folder-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<folder-path>?<SAS-token>` |
| **Пример** | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Добавление `--recursive` флаг для отправки файлов во всех вложенных папках.

## <a name="download-files"></a>Скачивание файлов

Можно использовать AzCopy `copy` совместно использует команду, чтобы скачать файлы, папки и файла на локальный компьютер.

В этом разделе содержатся следующие примеры:

> [!div class="checklist"]
> * скачать файл;
> * Папка загрузки
> * Загрузка файлов с помощью подстановочных знаков

> [!NOTE]
> Если `Content-md5` значение свойства файла содержит хэш, AzCopy вычисляет хэш MD5 для загруженных данных и проверяет, что хэша MD5, сохраненного в файле `Content-md5` свойство вычисленному хэшу. Если эти значения не совпадают, скачивание завершится сбоем, если не переопределить это поведение, добавив `--check-md5=NoCheck` или `--check-md5=LogOnly` для команды copy.

### <a name="download-a-file"></a>скачать файл;

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **Пример** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder\myTextFile.txt"` |

### <a name="download-a-folder"></a>Папка загрузки

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<folder-path>?<SAS-token>" "<local-folder-path>" --recursive` |
| **Пример** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder"  --recursive` |

В этом примере результаты в папку с именем `C:\myFolder\myFileShareFolder` , содержащий все загруженные файлы.

### <a name="download-the-contents-of-a-folder"></a>Загрузить содержимое папки

Можно загрузить содержимое папки без копирования папку, содержащую сам, используя подстановочный знак (*).

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-folder-path>/"` |
| **Пример** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder"` |

> [!NOTE]
> Добавление `--recursive` флаг для загрузки файлов во всех вложенных папках.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные примеры можно найти в любой из следующих статей:

- [Начало работы с AzCopy](storage-use-azcopy-v10.md)

- [Передача данных с помощью AzCopy и BLOB-хранилища](storage-use-azcopy-blobs.md)

- [Передача данных с помощью AzCopy и Amazon S3 сегментов](storage-use-azcopy-s3.md)

- [Настроить, оптимизировать и устранение неполадок с AzCopy](storage-use-azcopy-configure.md)