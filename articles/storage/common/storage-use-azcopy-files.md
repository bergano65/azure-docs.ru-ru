---
title: Передача данных в или из файлов Azure с помощью AzCopy v10 Документы Майкрософт
description: Передача данных с помощью AzCopy и хранения файлов.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 8aa0e5304825b3f016694a40b3fc1e176518237a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526694"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Transfer data with AzCopy and file storage (Передача данных с помощью AzCopy и хранилища файлов) 

AzCopy — это утилита командной строки, которую можно использовать для копирования капли или файлов в учетную запись или из учетной записи хранения. В этой статье содержатся примеры команд, которые работают с файлами Azure.

Прежде чем начать, смотрите статью [Get started с AzCopy,](storage-use-azcopy-v10.md) чтобы загрузить AzCopy и ознакомиться с инструментом.

## <a name="create-file-shares"></a>Создание файлов

Для создания общего файла можно использовать команду [azcopy make.](storage-ref-azcopy-make.md) Пример в этом разделе создает `myfileshare`раздел файла с именем .

> [!TIP]
> Примеры в этом разделе приложить аргументы пути с отдельными цитатами ('). Используйте одиночные кавычки во всех командных оболочках, за исключением оболочки командования Windows (cmd.exe). Если вы используете оболочку командования Windows (cmd.exe), приложить аргументы пути с двойными кавычками ("") вместо отдельных котировок (').

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>'` |
| **Пример** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Для подробных справочных документов, см [azcopy сделать](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Upload files

Вы можете использовать команду [копии azcopy](storage-ref-azcopy-copy.md) для загрузки файлов и каталогов с локального компьютера.

Этот раздел содержит следующие примеры.

> [!div class="checklist"]
> * Отправка файла
> * Отправка каталога
> * Отправка содержимого каталога
> * Загрузить определенный файл

> [!NOTE]
> AzCopy автоматически не вычисляет и не хранит хэш-код md5 файла. Если вы хотите, чтобы AzCopy сделал `--put-md5` это, а затем привяжите флаг к каждой команде копии. Таким образом, при загрузке файла AzCopy вычисляет хэш MD5 для скачанных данных и проверяет, что хэш MD5, хранящийся в свойстве файла, `Content-md5` соответствует расчетливому хэшу.

Для подробных справочных документов [см.](storage-ref-azcopy-copy.md)

> [!TIP]
> Примеры в этом разделе приложить аргументы пути с отдельными цитатами ('). Используйте одиночные кавычки во всех командных оболочках, за исключением оболочки командования Windows (cmd.exe). Если вы используете оболочку командования Windows (cmd.exe), приложить аргументы пути с двойными кавычками ("") вместо отдельных котировок (').

### <a name="upload-a-file"></a>Отправка файла

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **Пример** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Вы также можете загрузить файл, используя символ подстановочного знака (я) в любом месте пути файла или имя файла. Например: `'C:\myDirectory\*.txt'`, `C:\my*\*.txt`или .

### <a name="upload-a-directory"></a>Отправка каталога

Этот пример копирует каталог (и все файлы в этом каталоге) на общий файл. В результате получается каталог в одном и том же имени.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Пример** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Чтобы скопировать каталог в файле, просто укажите имя этого каталога в строке команды.

|    |     |
|--------|-----------|
| **Пример** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Если вы указываете имя каталога, которого не существует в доле файла, AzCopy создает новый каталог под этим именем.

### <a name="upload-the-contents-of-a-directory"></a>Отправка содержимого каталога

Содержимое каталога можно загрузить, не копируя сам содержащий каталог, используя символ подстановочного знака.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **Пример** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Приложить `--recursive` флаг для загрузки файлов во всех подкаталогах.

### <a name="upload-specific-files"></a>Загрузка определенных файлов

Вы можете указать полные имена файлов или использовать частичные имена с символами подстановочных знаков ( яп.

#### <a name="specify-multiple-complete-file-names"></a>Указать несколько полных имен файлов

Используйте команду [копии азкопии](storage-ref-azcopy-copy.md) с опцией. `--include-path` Отдельные отдельные имена файлов`;`с помощью запятой ().

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Пример** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

В этом примере AzCopy передает `C:\myDirectory\photos` `C:\myDirectory\documents\myFile.txt` каталог и файл. Необходимо включить опцию передачи `--recursive` `C:\myDirectory\photos` всех файлов в каталог.

Вы также можете исключить `--exclude-path` файлы с помощью опции. Чтобы узнать больше, смотрите [документы ссылки на копию азкопии.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Используйте символы подстановочных знаков

Используйте команду [копии азкопии](storage-ref-azcopy-copy.md) с опцией. `--include-pattern` Укажите частичные имена, включающие символы подстановочных знаков. Отдельные имена с помощью полуколина ().`;`

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Пример** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

Вы также можете исключить `--exclude-pattern` файлы с помощью опции. Чтобы узнать больше, смотрите [документы ссылки на копию азкопии.](storage-ref-azcopy-copy.md)

Параметры `--include-pattern` `--exclude-pattern` и параметры применяются только к именам файлов, а не к пути.  Если вы хотите скопировать все текстовые файлы, которые `–recursive` существуют в дереве каталогов, используйте опцию, чтобы получить все дерево каталога, а затем использовать `–include-pattern` и указать, `*.txt` чтобы получить все текстовые файлы.

## <a name="download-files"></a>Загрузка файлов

Вы можете использовать команду [копии azcopy](storage-ref-azcopy-copy.md) для загрузки файлов, каталогов и файлов на локальном компьютере.

Этот раздел содержит следующие примеры.

> [!div class="checklist"]
> * скачать файл;
> * Загрузка каталога
> * Загрузка содержимого каталога
> * Скачать определенные файлы

> [!NOTE]
> Если `Content-md5` значение свойства файла содержит хэш, AzCopy вычисляет хэш MD5 для скачанных данных и проверяет, что хэш MD5, хранящийся в свойстве файла, `Content-md5` соответствует расчетным хэшам. Если эти значения не совпадают, загрузка не удается, `--check-md5=NoCheck` если `--check-md5=LogOnly` вы не переопределить это поведение, подопяв или к команде копирования.

Для подробных справочных документов [см.](storage-ref-azcopy-copy.md)

> [!TIP]
> Примеры в этом разделе приложить аргументы пути с отдельными цитатами ('). Используйте одиночные кавычки во всех командных оболочках, за исключением оболочки командования Windows (cmd.exe). Если вы используете оболочку командования Windows (cmd.exe), приложить аргументы пути с двойными кавычками ("") вместо отдельных котировок (').

### <a name="download-a-file"></a>скачать файл;

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'` |
| **Пример** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Загрузка каталога

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive` |
| **Пример** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

Этот пример приводит к `C:\myDirectory\myFileShareDirectory` тому, что каталог с именем содержит все загруженные файлы.

### <a name="download-the-contents-of-a-directory"></a>Загрузка содержимого каталога

Содержимое каталога можно загрузить, не копируя сам содержащий каталог, используя символ подстановочного знака.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **Пример** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Придатите флаг для загрузки `--recursive` файлов во всех подкаталогах.

### <a name="download-specific-files"></a>Скачать определенные файлы

Вы можете указать полные имена файлов или использовать частичные имена с символами подстановочных знаков ( яп.

#### <a name="specify-multiple-complete-file-names"></a>Указать несколько полных имен файлов

Используйте команду [копии азкопии](storage-ref-azcopy-copy.md) с опцией. `--include-path` Отдельные отдельные имена файлов`;`с помощью полуколина ().

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Пример** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

В этом примере AzCopy передает `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` каталог и файл. Необходимо включить опцию передачи `--recursive` `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` всех файлов в каталог.

Вы также можете исключить `--exclude-path` файлы с помощью опции. Чтобы узнать больше, смотрите [документы ссылки на копию азкопии.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Используйте символы подстановочных знаков

Используйте команду [копии азкопии](storage-ref-azcopy-copy.md) с опцией. `--include-pattern` Укажите частичные имена, включающие символы подстановочных знаков. Отдельные имена с помощью полуколина ().`;`

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Пример** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Вы также можете исключить `--exclude-pattern` файлы с помощью опции. Чтобы узнать больше, смотрите [документы ссылки на копию азкопии.](storage-ref-azcopy-copy.md)

Параметры `--include-pattern` `--exclude-pattern` и параметры применяются только к именам файлов, а не к пути.  Если вы хотите скопировать все текстовые файлы, которые `–recursive` существуют в дереве каталогов, используйте опцию, чтобы получить все дерево каталога, а затем использовать `–include-pattern` и указать, `*.txt` чтобы получить все текстовые файлы.

## <a name="copy-files-between-storage-accounts"></a>Копирование файлов между учетными записями хранения

Можно использовать AzCopy для копирования файлов на другие учетные записи хранения. Операция копирования синхронна, поэтому, когда команда возвращается, это указывает на то, что все файлы были скопированы.

AzCopy использует [AA,](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)доступные для [сервера](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) сервера, поэтому данные копируются непосредственно между серверами хранения данных. Эти операции копирования не используют пропускную способность сети вашего компьютера. Вы можете увеличить пропускную стоимость этих операций, установив значение переменной среды. `AZCOPY_CONCURRENCY_VALUE` Чтобы узнать больше, [см.](storage-use-azcopy-configure.md#optimize-throughput)

Этот раздел содержит следующие примеры.

> [!div class="checklist"]
> * Копирование файла на другую учетную запись хранения
> * Копирование каталога на другую учетную запись хранения
> * Копирование общего файла на другую учетную запись хранения
> * Копирование всех файлов, каталогов и файлов на другую учетную запись хранения

Для подробных справочных документов, см [azcopy копию](storage-ref-azcopy-copy.md).

> [!TIP]
> Примеры в этом разделе приложить аргументы пути с отдельными цитатами ('). Используйте одиночные кавычки во всех командных оболочках, за исключением оболочки командования Windows (cmd.exe). Если вы используете оболочку командования Windows (cmd.exe), приложить аргументы пути с двойными кавычками ("") вместо отдельных котировок (').

### <a name="copy-a-file-to-another-storage-account"></a>Копирование файла на другую учетную запись хранения

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **Пример** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Копирование каталога на другую учетную запись хранения

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Пример** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>Копирование общего файла на другую учетную запись хранения

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Пример** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Копирование всех файлов, каталогов и файлов на другую учетную запись хранения

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **Пример** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>Синхронизация файлов

Можно синхронизировать содержимое файла с другой долей файла. Можно также синхронизировать содержимое каталога в файле с содержимым каталога, который находится в другой доле файла. Синхронизация односторонней. Другими словами, вы выбираете, какая из этих двух конечных точек является источником, а какая – пунктом назначения. Синхронизация также использует сервер для серверных AIS.

> [!NOTE]
> В настоящее время этот сценарий поддерживается только для учетных записей, которые не имеют иерархического пространства имен. Текущий выпуск AzCopy не синхронизируется между файлами Azure и Blob Storage.

Команда [синхронизации](storage-ref-azcopy-sync.md) сравнивает имена файлов и последние измененные метки времени. Установите `--delete-destination` дополнительный флаг `true` на `prompt` значение или удалить файлы в каталоге назначения, если эти файлы больше не существуют в исходном каталоге.

При установке `--delete-destination` флага `true` AzCopy файлы удаляются без предоставления запроса. Если вы хотите, чтобы запрос появился до того, как AzCopy удалил файл, установите `--delete-destination` флаг. `prompt`

Для подробных справочных документов [см.](storage-ref-azcopy-sync.md)

> [!TIP]
> Примеры в этом разделе приложить аргументы пути с отдельными цитатами ('). Используйте одиночные кавычки во всех командных оболочках, за исключением оболочки командования Windows (cmd.exe). Если вы используете оболочку командования Windows (cmd.exe), приложить аргументы пути с двойными кавычками ("") вместо отдельных котировок (').

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Обновление общего файла с изменениями в другой доле файла

Первая доля файлов, которая отображается в этой команде, является источником. Второй - пункт назначения.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Пример** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Обновление каталога с изменениями в каталоге в другом файле

Первым каталогом, который отображается в этой команде, является источник. Второй - пункт назначения.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **Пример** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="next-steps"></a>Дальнейшие действия

Найдите больше примеров в любой из этих статей:

- [Get started with AzCopy](storage-use-azcopy-v10.md) (Начало работы с AzCopy)

- [Transfer data with AzCopy and blob storage](storage-use-azcopy-blobs.md) (Передача данных с помощью AzCopy и хранилища BLOB-объектов)

- [Передача данных с помощью AzCopy и контейнеров Amazon S3](storage-use-azcopy-s3.md)

- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md) (Настройка, оптимизация и устранение неполадок с AzCopy)
