---
title: Перенос данных в службу файлов Azure или из нее с помощью AzCopy V10 | Документация Майкрософт
description: Перенос данных с помощью AzCopy и хранилища файлов.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: eeac35cb96f5001e9ad318d8fe03927d0cd9394e
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68844808"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Перенос данных с помощью AzCopy и хранилища файлов 

AzCopy — это служебная программа командной строки, которую можно использовать для копирования больших двоичных объектов или файлов в учетную запись хранения или из нее. Эта статья содержит примеры команд, которые работают с файлами Azure.

Прежде чем начать, ознакомьтесь со статьей начало [работы с AzCopy](storage-use-azcopy-v10.md) , чтобы скачать AzCopy и ознакомиться с этим инструментом.

## <a name="create-file-shares"></a>Создание общих файловых ресурсов

Для создания общей папки можно `make` использовать команду AzCopy. В примере в этом разделе создается общая папка с именем `myfileshare`.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **Пример** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>Отправить файлы

Для отправки файлов и каталогов `copy` с локального компьютера можно использовать команду AzCopy.

В этом разделе содержатся следующие примеры:

> [!div class="checklist"]
> * Отправить файл
> * Отправка каталога
> * Отправка файлов с помощью подстановочных знаков

> [!NOTE]
> AzCopy не вычисляет и не сохраняет хэш-код MD5 для файла автоматически. Если вы хотите, чтобы AzCopy это сделать, добавьте `--put-md5` флаг к каждой команде копирования. Таким образом, при скачивании файла AzCopy вычисляет хэш MD5 для скачанных данных и проверяет, соответствует ли хэш MD5, хранящийся в `Content-md5` свойстве файла, вычисляемому хэшу.

### <a name="upload-a-file"></a>Отправить файл

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **Пример** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-directory"></a>Отправка каталога

В этом примере каталог (и все файлы в этом каталоге) копируется в общую папку. Результатом является каталог в общей папке с тем же именем.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **Пример** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Чтобы скопировать в каталог в общей папке, просто укажите имя этого каталога в командной строке.

|    |     |
|--------|-----------|
| **Пример** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Если указать имя каталога, который не существует в общей папке, AzCopy создает новый каталог с этим именем.

### <a name="upload-the-contents-of-a-directory"></a>Отправка содержимого каталога

Вы можете отправить содержимое каталога, не копируя сам каталог с помощью подстановочного знака (*).

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "<local-directory-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **Пример** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> `--recursive` Добавьте флаг для отправки файлов во все подкаталоги.

## <a name="download-files"></a>Скачивание файлов

Для загрузки файлов, каталогов `copy` и файловых ресурсов на локальный компьютер можно использовать команду AzCopy.

В этом разделе содержатся следующие примеры:

> [!div class="checklist"]
> * Скачать файл
> * Загрузка каталога
> * Загрузка файлов с помощью подстановочных знаков

> [!NOTE]
> Если значение `Content-md5` свойства файла содержит хэш, AzCopy вычисляет хэш MD5 для скачанных данных и проверяет, соответствует ли хэш MD5, хранящийся в свойстве файла, вычисляемому хэшу. `Content-md5` Если эти значения не совпадают, загрузка завершается ошибкой, если это поведение не переопределено `--check-md5=LogOnly` путем добавления `--check-md5=NoCheck` или к команде Copy.

### <a name="download-a-file"></a>Скачать файл

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **Пример** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Загрузка каталога

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>" "<local-directory-path>" --recursive` |
| **Пример** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"  --recursive` |

В этом примере создается каталог с именем `C:\myDirectory\myFileShareDirectory` , который содержит все скачанные файлы.

### <a name="download-the-contents-of-a-directory"></a>Загрузка содержимого каталога

Вы можете скачать содержимое каталога, не копируя сам каталог с помощью подстановочного знака (*).

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-directory-path>/"` |
| **Пример** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"` |

> [!NOTE]
> `--recursive` Добавьте флаг для скачивания файлов во всех подкаталогах.

## <a name="next-steps"></a>Следующие шаги

Дополнительные примеры приведены в любой из следующих статей:

- [Get started with AzCopy](storage-use-azcopy-v10.md) (Начало работы с AzCopy)

- [Transfer data with AzCopy and blob storage](storage-use-azcopy-blobs.md) (Передача данных с помощью AzCopy и хранилища BLOB-объектов)

- [Передача данных с помощью AzCopy и контейнеров Amazon S3](storage-use-azcopy-s3.md)

- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md) (Настройка, оптимизация и устранение неполадок с AzCopy)