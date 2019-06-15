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
ms.openlocfilehash: 69d7136396c3d989e63b8956d3e703cc7f9666c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66687931"
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

Можно использовать AzCopy `copy` команду, чтобы передать файлы и каталоги на локальном компьютере.

В этом разделе содержатся следующие примеры:

> [!div class="checklist"]
> * Отправка файла
> * Отправить каталог
> * Отправка файлов с использованием подстановочных знаков

> [!NOTE]
> AzCopy не автоматически вычислить и сохранить хэш-код md5 файла. Если требуется AzCopy, чтобы сделать это, добавьте `--put-md5` флаг для каждой команды копирования. Таким образом, когда загружается файл, AzCopy вычисляет хэш MD5 для загрузки данных и проверяет, что хэша MD5, сохраненного в файле `Content-md5` свойство вычисленному хэшу.

### <a name="upload-a-file"></a>Отправка файла

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **Пример** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-directory"></a>Отправить каталог

Этот пример копирует каталог (и все файлы в этом каталоге) для общей папки. Результат представляет собой каталог в общей папке с таким именем.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **Пример** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Чтобы скопировать каталог в рамках общей папки, просто укажите имя этого каталога в командной строке.

|    |     |
|--------|-----------|
| **Пример** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Если указать имя каталога, который не существует в общей папке, AzCopy создаст новый каталог с таким именем.

### <a name="upload-the-contents-of-a-directory"></a>Отправьте содержимое папки

Вы можете отправить содержимое каталога без копирования каталога, содержащего сам, используя подстановочный знак (*).

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "<local-directory-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **Пример** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Добавление `--recursive` флаг для отправки файлов во всех вложенных каталогах.

## <a name="download-files"></a>Скачивание файлов

Можно использовать AzCopy `copy` совместно использует команду, чтобы скачать файлы, каталоги и файл на локальный компьютер.

В этом разделе содержатся следующие примеры:

> [!div class="checklist"]
> * скачать файл;
> * Скачать каталог
> * Загрузка файлов с помощью подстановочных знаков

> [!NOTE]
> Если `Content-md5` значение свойства файла содержит хэш, AzCopy вычисляет хэш MD5 для загруженных данных и проверяет, что хэша MD5, сохраненного в файле `Content-md5` свойство вычисленному хэшу. Если эти значения не совпадают, скачивание завершится сбоем, если не переопределить это поведение, добавив `--check-md5=NoCheck` или `--check-md5=LogOnly` для команды copy.

### <a name="download-a-file"></a>скачать файл;

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **Пример** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Скачать каталог

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>" "<local-directory-path>" --recursive` |
| **Пример** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"  --recursive` |

В этом примере результаты в каталог с именем `C:\myDirectory\myFileShareDirectory` , содержащий все загруженные файлы.

### <a name="download-the-contents-of-a-directory"></a>Загрузить содержимое каталога

Можно загрузить содержимое каталога без копирования каталога, содержащего сам, используя подстановочный знак (*).

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-directory-path>/"` |
| **Пример** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"` |

> [!NOTE]
> Добавление `--recursive` флаг для загрузки файлов во всех вложенных каталогах.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные примеры можно найти в любой из следующих статей:

- [Get started with AzCopy](storage-use-azcopy-v10.md) (Начало работы с AzCopy)

- [Transfer data with AzCopy and blob storage](storage-use-azcopy-blobs.md) (Передача данных с помощью AzCopy и хранилища BLOB-объектов)

- [Передача данных с помощью AzCopy и контейнеров Amazon S3](storage-use-azcopy-s3.md)

- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md) (Настройка, оптимизация и устранение неполадок с AzCopy)