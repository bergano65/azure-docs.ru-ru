---
title: Перенос данных в службу файлов Azure или из нее с помощью AzCopy V10 | Документация Майкрософт
description: Перенос данных с помощью AzCopy и хранилища файлов.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 27042809b0f60e1e4141d50d20acff1893c71fde
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158244"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Перенос данных с помощью AzCopy и хранилища файлов 

AzCopy — это служебная программа командной строки, которую можно использовать для копирования больших двоичных объектов или файлов в учетную запись хранения или из нее. Эта статья содержит примеры команд, которые работают с файлами Azure.

Прежде чем начать, ознакомьтесь со статьей начало [работы с AzCopy](storage-use-azcopy-v10.md) , чтобы скачать AzCopy и ознакомиться с этим инструментом.

## <a name="create-file-shares"></a>Создание общих файловых ресурсов

Для создания общей папки можно использовать команду [azcopy make](storage-ref-azcopy-make.md) . В примере в этом разделе создается общая папка с именем `myfileshare`.

> [!TIP]
> В примерах этого раздела аргументы пути заключаются в одинарные кавычки (' '). Используйте одинарные кавычки во всех командных оболочках, кроме командной оболочки Windows (cmd. exe). Если вы используете командную оболочку Windows (cmd. exe), заключите аргументы пути в двойные кавычки ("") вместо одинарных кавычек ("").

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>'` |
| **Пример** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Подробную справочную документацию см. в разделе [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Отправка файлов

Для отправки файлов и каталогов с локального компьютера можно использовать команду [azcopy Copy](storage-ref-azcopy-copy.md) .

В этом разделе содержатся следующие примеры:

> [!div class="checklist"]
> * отправить файл;
> * Отправка каталога
> * Отправка содержимого каталога
> * Отправить указанный файл

> [!NOTE]
> AzCopy не вычисляет и не сохраняет хэш-код MD5 для файла автоматически. Если вы хотите, чтобы AzCopy, добавьте к каждой команде копирования флаг `--put-md5`. Таким образом, при скачивании файла AzCopy вычисляет хэш MD5 для скачанных данных и проверяет, соответствует ли хэш MD5, хранящийся в свойстве `Content-md5` файла, соответствующему вычисляемому хэшу.

Подробную справочную документацию см. в разделе [azcopy Copy](storage-ref-azcopy-copy.md).

> [!TIP]
> В примерах этого раздела аргументы пути заключаются в одинарные кавычки (' '). Используйте одинарные кавычки во всех командных оболочках, кроме командной оболочки Windows (cmd. exe). Если вы используете командную оболочку Windows (cmd. exe), заключите аргументы пути в двойные кавычки ("") вместо одинарных кавычек ("").

### <a name="upload-a-file"></a>отправить файл;

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>'` |
| **Пример** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Можно также передать файл, используя подстановочный знак (*) в любом месте пути к файлу или имени файла. Например: `'C:\myDirectory\*.txt'`или `C:\my*\*.txt`.

### <a name="upload-a-directory"></a>Отправка каталога

В этом примере каталог (и все файлы в этом каталоге) копируется в общую папку. Результатом является каталог в общей папке с тем же именем.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **Пример** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Чтобы скопировать в каталог в общей папке, просто укажите имя этого каталога в командной строке.

|    |     |
|--------|-----------|
| **Пример** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Если указать имя каталога, который не существует в общей папке, AzCopy создает новый каталог с этим именем.

### <a name="upload-the-contents-of-a-directory"></a>Отправка содержимого каталога

Вы можете отправить содержимое каталога, не копируя сам каталог с помощью подстановочного знака (*).

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **Пример** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Добавьте флаг `--recursive` для отправки файлов во все подкаталоги.

### <a name="upload-specific-files"></a>Отправка конкретных файлов

Можно указать полные имена файлов или использовать частичные имена с подстановочными знаками (*).

#### <a name="specify-multiple-complete-file-names"></a>Указание нескольких полных имен файлов

Используйте команду [azcopy Copy](storage-ref-azcopy-copy.md) с параметром `--include-path`. Отдельные имена файлов разделяются точкой с запятой (`;`).

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>?<SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Пример** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

В этом примере AzCopy передает `C:\myDirectory\photos` каталог и файл `C:\myDirectory\documents\myFile.txt`. Необходимо включить параметр `--recursive` для перемещения всех файлов в каталоге `C:\myDirectory\photos`.

Кроме того, можно исключить файлы с помощью параметра `--exclude-path`. Дополнительные сведения см. в статье [azcopy Copy](storage-ref-azcopy-copy.md) Справочник по документам.

#### <a name="use-wildcard-characters"></a>Использовать подстановочные знаки

Используйте команду [azcopy Copy](storage-ref-azcopy-copy.md) с параметром `--include-pattern`. Укажите частичные имена, которые содержат подстановочные знаки. Разделяйте имена с помощью семиколин (`;`).

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>?<SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Пример** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

Кроме того, можно исключить файлы с помощью параметра `--exclude-pattern`. Дополнительные сведения см. в статье [azcopy Copy](storage-ref-azcopy-copy.md) Справочник по документам.

Параметры `--include-pattern` и `--exclude-pattern` применяются только к именам файлов, а не к пути.  Если необходимо скопировать все текстовые файлы, существующие в дереве каталогов, используйте параметр `–recursive`, чтобы получить все дерево каталогов, а затем используйте `–include-pattern` и укажите `*.txt`, чтобы получить все текстовые файлы.

## <a name="download-files"></a>Скачивание файлов

Для загрузки файлов, каталогов и файловых ресурсов на локальный компьютер можно использовать команду [azcopy Copy](storage-ref-azcopy-copy.md) .

В этом разделе содержатся следующие примеры:

> [!div class="checklist"]
> * скачать файл;
> * Загрузка каталога
> * Загрузка содержимого каталога
> * Загрузка конкретных файлов

> [!NOTE]
> Если значение свойства `Content-md5` файла содержит хэш, AzCopy вычисляет хэш MD5 для скачанных данных и проверяет, соответствует ли хэш MD5, хранящийся в свойстве `Content-md5` файла, соответствующему вычисляемому хэшу. Если эти значения не совпадают, загрузка завершится ошибкой, если это поведение не будет переопределено путем добавления `--check-md5=NoCheck` или `--check-md5=LogOnly` в команду копирования.

Подробную справочную документацию см. в разделе [azcopy Copy](storage-ref-azcopy-copy.md).

> [!TIP]
> В примерах этого раздела аргументы пути заключаются в одинарные кавычки (' '). Используйте одинарные кавычки во всех командных оболочках, кроме командной оболочки Windows (cmd. exe). Если вы используете командную оболочку Windows (cmd. exe), заключите аргументы пути в двойные кавычки ("") вместо одинарных кавычек ("").

### <a name="download-a-file"></a>скачать файл;

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' '<local-file-path>'` |
| **Пример** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Загрузка каталога

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' '<local-directory-path>' --recursive`|
| **Пример** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

В этом примере создается каталог с именем `C:\myDirectory\myFileShareDirectory`, содержащий все скачанные файлы.

### <a name="download-the-contents-of-a-directory"></a>Загрузка содержимого каталога

Вы можете скачать содержимое каталога, не копируя сам каталог с помощью подстановочного знака (*).

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>' '<local-directory-path>/'` |
| **Пример** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Добавьте флаг `--recursive` для скачивания файлов во всех подкаталогах.

### <a name="download-specific-files"></a>Загрузка конкретных файлов

Можно указать полные имена файлов или использовать частичные имена с подстановочными знаками (*).

#### <a name="specify-multiple-complete-file-names"></a>Указание нескольких полных имен файлов

Используйте команду [azcopy Copy](storage-ref-azcopy-copy.md) с параметром `--include-path`. Разделяйте отдельные имена файлов с помощью семиколин (`;`).

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>?<SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Пример** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

В этом примере AzCopy передает `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` каталог и файл `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt`. Необходимо включить параметр `--recursive` для перемещения всех файлов в каталоге `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos`.

Кроме того, можно исключить файлы с помощью параметра `--exclude-path`. Дополнительные сведения см. в статье [azcopy Copy](storage-ref-azcopy-copy.md) Справочник по документам.

#### <a name="use-wildcard-characters"></a>Использовать подстановочные знаки

Используйте команду [azcopy Copy](storage-ref-azcopy-copy.md) с параметром `--include-pattern`. Укажите частичные имена, которые содержат подстановочные знаки. Разделяйте имена с помощью семиколин (`;`).

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>?<SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Пример** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Кроме того, можно исключить файлы с помощью параметра `--exclude-pattern`. Дополнительные сведения см. в статье [azcopy Copy](storage-ref-azcopy-copy.md) Справочник по документам.

Параметры `--include-pattern` и `--exclude-pattern` применяются только к именам файлов, а не к пути.  Если необходимо скопировать все текстовые файлы, существующие в дереве каталогов, используйте параметр `–recursive`, чтобы получить все дерево каталогов, а затем используйте `–include-pattern` и укажите `*.txt`, чтобы получить все текстовые файлы.

## <a name="copy-files-between-storage-accounts"></a>Копирование файлов между учетными записями хранения

AzCopy можно использовать для копирования файлов в другие учетные записи хранения. Операция копирования является синхронной, поэтому когда команда возвращает значение, это означает, что все файлы скопированы.

AzCopy использует [API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)" [сервер-сервер](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) ", поэтому данные копируются непосредственно между серверами хранилища. Эти операции копирования не используют пропускную способность сети компьютера. Можно увеличить пропускную способность этих операций, задав значение переменной среды `AZCOPY_CONCURRENCY_VALUE`. Дополнительные сведения см. в разделе [Оптимизация пропускной способности](storage-use-azcopy-configure.md#optimize-throughput).

В этом разделе содержатся следующие примеры:

> [!div class="checklist"]
> * Копирование файла в другую учетную запись хранения
> * Копирование каталога в другую учетную запись хранения
> * Копирование общей папки в другую учетную запись хранения
> * Копировать все файловые ресурсы, каталоги и файлы в другую учетную запись хранения

Подробную справочную документацию см. в разделе [azcopy Copy](storage-ref-azcopy-copy.md).

> [!TIP]
> В примерах этого раздела аргументы пути заключаются в одинарные кавычки (' '). Используйте одинарные кавычки во всех командных оболочках, кроме командной оболочки Windows (cmd. exe). Если вы используете командную оболочку Windows (cmd. exe), заключите аргументы пути в двойные кавычки ("") вместо одинарных кавычек ("").

### <a name="copy-a-file-to-another-storage-account"></a>Копирование файла в другую учетную запись хранения

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>'` |
| **Пример** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Копирование каталога в другую учетную запись хранения

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **Пример** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>Копирование общей папки в другую учетную запись хранения

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **Пример** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Копировать все файловые ресурсы, каталоги и файлы в другую учетную запись хранения

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/?<SAS-token>' --recursive'` |
| **Пример** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>Синхронизировать файлы

Вы можете синхронизировать содержимое файлового ресурса с другой общей папкой. Кроме того, можно синхронизировать содержимое каталога в общей папке с содержимым каталога, расположенного в другой общей папке. Синхронизация является односторонней. Иными словами, вы выбираете, какие из этих двух конечных точек являются источником, и какой из них является назначением. Синхронизация также использует API сервера-сервера.

> [!NOTE]
> В настоящее время этот сценарий поддерживается только для учетных записей, у которых нет иерархического пространства имен. Текущий выпуск AzCopy не синхронизируется между службой файлов Azure и хранилищем BLOB-объектов.

Команда [Sync](storage-ref-azcopy-sync.md) сравнивает имена файлов и метки времени последнего изменения. Установите необязательный флаг `--delete-destination` в значение `true` или `prompt`, чтобы удалить файлы в целевом каталоге, если эти файлы больше не существуют в исходном каталоге.

Если для флага `--delete-destination` задано значение `true` AzCopy удаляет файлы без указания запроса. Если требуется, чтобы запрос отображался перед тем, как AzCopy удалит файл, установите флаг `--delete-destination` в значение `prompt`.

Подробную справочную документацию см. в разделе [azcopy Sync](storage-ref-azcopy-sync.md).

> [!TIP]
> В примерах этого раздела аргументы пути заключаются в одинарные кавычки (' '). Используйте одинарные кавычки во всех командных оболочках, кроме командной оболочки Windows (cmd. exe). Если вы используете командную оболочку Windows (cmd. exe), заключите аргументы пути в двойные кавычки ("") вместо одинарных кавычек ("").

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Обновление общей папки с изменениями в другой общей папке

Первый файловый ресурс, отображаемый в этой команде, является источником. Второй — назначение.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **Пример** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Обновление каталога с изменениями в каталоге в другой общей папке

Первым каталогом, который отображается в этой команде, является источник. Второй — назначение.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' --recursive` |
| **Пример** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные примеры приведены в любой из следующих статей:

- [Get started with AzCopy](storage-use-azcopy-v10.md) (Начало работы с AzCopy)

- [Transfer data with AzCopy and blob storage](storage-use-azcopy-blobs.md) (Передача данных с помощью AzCopy и хранилища BLOB-объектов)

- [Передача данных с помощью AzCopy и контейнеров Amazon S3](storage-use-azcopy-s3.md)

- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md) (Настройка, оптимизация и устранение неполадок с AzCopy)
