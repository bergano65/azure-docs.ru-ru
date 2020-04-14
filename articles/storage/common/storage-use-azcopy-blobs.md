---
title: Передача данных в или из хранилища Azure Blob с помощью AzCopy v10 Документы Майкрософт
description: Эта статья содержит коллекцию примеров примеров AzCopy, которые помогают создавать контейнеры, копировать файлы и синхронизировать каталоги между локальными файловыми системами и контейнерами.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 73685f124f93bb541f33b3b70727d90ce22b3cdd
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263443"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Передача данных с помощью хранилища AzCopy и Blob

AzCopy — это утилита командной строки, которую можно использовать для копирования данных в учетные записи или между ними. Эта статья содержит примеры команд, которые работают с хранилищем Blob.

> [!TIP]
> Примеры в этой статье приложить аргументы пути с одними цитатами ('). Используйте одиночные кавычки во всех командных оболочках, за исключением оболочки командования Windows (cmd.exe). Если вы используете оболочку командования Windows (cmd.exe), приложить аргументы пути с двойными кавычками ("") вместо отдельных котировок (').

## <a name="get-started"></a>Начало работы

Ознакомьтесь со статьей [«Начало работы» с AzCopy,](storage-use-azcopy-v10.md) чтобы загрузить AzCopy и узнать о способах предоставления учетных данных авторизации службе хранения данных.

> [!NOTE]
> Примеры в этой статье предполагают, что вы подтвердили `AzCopy login` подлинность своей личности с помощью команды. Затем AzCopy использует учетную запись Azure AD для авторизации доступа к данным в хранилище Blob.
>
> Если вы предпочитаете использовать токен SAS для авторизации доступа к данным blob, то вы можете приобщить этот маркер к URL-адресу ресурса в каждой команде AzCopy.
>
> Например: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Создание контейнера

Для создания контейнера можно использовать команду [azcopy make.](storage-ref-azcopy-make.md) Примеры в этом разделе `mycontainer`создают контейнер с именем .

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Пример** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Пример** (иерархическое пространство имен) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

Для подробных справочных документов, см [azcopy сделать](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Upload files

Вы можете использовать команду [копии azcopy](storage-ref-azcopy-copy.md) для загрузки файлов и каталогов с локального компьютера.

Этот раздел содержит следующие примеры.

> [!div class="checklist"]
> * Отправка файла
> * Отправка каталога
> * Отправка содержимого каталога 
> * Загрузка определенных файлов

> [!TIP]
> Вы можете настроить операцию загрузки с помощью дополнительных флагов. Вот несколько примеров.
>
> |Сценарий|Флаг|
> |---|---|
> |Загружайте файлы в виде append Blobs или Page Blobs.|**--blob типа**=\[BlockBlob PageBlob\|\|ПриложениеBlob\]|
> |Загрузка на определенный уровень доступа (например, уровень архива).|**--блок-blob-уровня**=\[\|None\|\|Hot Cool Архив\]|
> |Автоматически декомпрессии файлов.|**--декомпресс**=\[Gzip\|сдуваться\]|
> 
> Для полного списка [см.](storage-ref-azcopy-copy.md#options)

### <a name="upload-a-file"></a>Отправка файла

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Пример** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

Вы также можете загрузить файл, используя символ подстановочного знака (я) в любом месте пути файла или имя файла. Например: `'C:\myDirectory\*.txt'`, `C:\my*\*.txt`или .

### <a name="upload-a-directory"></a>Отправка каталога

Этот пример копирует каталог (и все файлы в этом каталоге) в контейнер с каплей. В результате в контейнере с тем же именем находится каталог.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Пример** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Чтобы скопировать каталог в контейнере, просто укажите имя этого каталога в строке команды.

|    |     |
|--------|-----------|
| **Пример** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Если вы указываете имя каталога, которого не существует в контейнере, AzCopy создает новый каталог с этим именем.

### <a name="upload-the-contents-of-a-directory"></a>Отправка содержимого каталога

Содержимое каталога можно загрузить, не копируя сам содержащий каталог, используя символ подстановочного знака.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Пример** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> Приложить `--recursive` флаг для загрузки файлов во всех подкаталогах.

### <a name="upload-specific-files"></a>Загрузка определенных файлов

Вы можете указать полные имена файлов или использовать частичные имена с символами подстановочных знаков ( яп.

#### <a name="specify-multiple-complete-file-names"></a>Указать несколько полных имен файлов

Используйте команду [копии азкопии](storage-ref-azcopy-copy.md) с опцией. `--include-path` Отдельные отдельные имена файлов`;`с помощью запятой ().

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Пример** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

В этом примере AzCopy передает `C:\myDirectory\photos` `C:\myDirectory\documents\myFile.txt` каталог и файл. Необходимо включить опцию передачи `--recursive` `C:\myDirectory\photos` всех файлов в каталог.

Вы также можете исключить `--exclude-path` файлы с помощью опции. Чтобы узнать больше, смотрите [документы ссылки на копию азкопии.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Используйте символы подстановочных знаков

Используйте команду [копии азкопии](storage-ref-azcopy-copy.md) с опцией. `--include-pattern` Укажите частичные имена, включающие символы подстановочных знаков. Отдельные имена с помощью полуколина ().`;` 

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Пример** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

Вы также можете исключить `--exclude-pattern` файлы с помощью опции. Чтобы узнать больше, смотрите [документы ссылки на копию азкопии.](storage-ref-azcopy-copy.md)

Параметры `--include-pattern` `--exclude-pattern` и параметры применяются только к именам файлов, а не к пути.  Если вы хотите скопировать все текстовые файлы, которые `–recursive` существуют в дереве каталогов, используйте опцию, чтобы получить все дерево каталога, а затем использовать `–include-pattern` и указать, `*.txt` чтобы получить все текстовые файлы.

## <a name="download-files"></a>Загрузка файлов

Вы можете использовать команду [копии azcopy](storage-ref-azcopy-copy.md) для загрузки капли, каталоги и контейнеры на ваш местный компьютер.

Этот раздел содержит следующие примеры.

> [!div class="checklist"]
> * скачать файл;
> * Загрузка каталога
> * Загрузка содержимого каталога
> * Скачать определенные файлы

> [!TIP]
> Вы можете настроить операцию загрузки с помощью дополнительных флагов. Вот несколько примеров.
>
> |Сценарий|Флаг|
> |---|---|
> |Автоматически декомпрессии файлов.|**--декомпресс**=\[Gzip\|сдуваться\]|
> |Укажите, насколько подробными вы хотите, чтобы записи журнала, связанные с копированием, были.|**--Лог-уровня**=\[\|ВНИМАНИЕ\|\|ERROR INFO НЕТ\]|
> |Укажите, если и как перезаписать противоречивые файлы и капли в пункте назначения.|**--перезаписать**=\[\|\|истинные ложные ifSourceNewer\|запрос\]|
> 
> Для полного списка [см.](storage-ref-azcopy-copy.md#options)

> [!NOTE]
> Если `Content-md5` значение свойства капли содержит хэш, AzCopy вычисляет хэш MD5 для скачанных данных и проверяет, что `Content-md5` хэш MD5, хранящийся в свойстве капли, соответствует расчетным хэшам. Если эти значения не совпадают, загрузка не удается, `--check-md5=NoCheck` если `--check-md5=LogOnly` вы не переопределить это поведение, подопяв или к команде копирования.

### <a name="download-a-file"></a>скачать файл;

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Пример** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Загрузка каталога

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Пример** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Этот пример приводит к `C:\myDirectory\myBlobDirectory` тому, что каталог с именем содержит все загруженные файлы.

### <a name="download-the-contents-of-a-directory"></a>Загрузка содержимого каталога

Содержимое каталога можно загрузить, не копируя сам содержащий каталог, используя символ подстановочного знака.

> [!NOTE]
> В настоящее время этот сценарий поддерживается только для учетных записей, которые не имеют иерархического пространства имен.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Пример** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Придатите флаг для загрузки `--recursive` файлов во всех подкаталогах.

### <a name="download-specific-files"></a>Скачать определенные файлы

Вы можете указать полные имена файлов или использовать частичные имена с символами подстановочных знаков ( яп.

#### <a name="specify-multiple-complete-file-names"></a>Указать несколько полных имен файлов

Используйте команду [копии азкопии](storage-ref-azcopy-copy.md) с опцией. `--include-path` Отдельные отдельные имена файлов`;`с помощью полуколина ().

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Пример** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

В этом примере AzCopy передает `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` каталог и файл. Необходимо включить опцию передачи `--recursive` `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` всех файлов в каталог.

Вы также можете исключить `--exclude-path` файлы с помощью опции. Чтобы узнать больше, смотрите [документы ссылки на копию азкопии.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Используйте символы подстановочных знаков

Используйте команду [копии азкопии](storage-ref-azcopy-copy.md) с опцией. `--include-pattern` Укажите частичные имена, включающие символы подстановочных знаков. Отдельные имена с помощью полуколина ().`;`

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Пример** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Вы также можете исключить `--exclude-pattern` файлы с помощью опции. Чтобы узнать больше, смотрите [документы ссылки на копию азкопии.](storage-ref-azcopy-copy.md)

Параметры `--include-pattern` `--exclude-pattern` и параметры применяются только к именам файлов, а не к пути.  Если вы хотите скопировать все текстовые файлы, которые `–recursive` существуют в дереве каталогов, используйте опцию, чтобы получить все дерево каталога, а затем использовать `–include-pattern` и указать, `*.txt` чтобы получить все текстовые файлы.

## <a name="copy-blobs-between-storage-accounts"></a>Копирование больших двоичных объектов между учетными записями хранения

Вы можете использовать AzCopy для копирования капли для других учетных записей хранения. Операция копирования синхронна, поэтому, когда команда возвращается, это указывает на то, что все файлы были скопированы. 

AzCopy использует [AA,](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)доступные для [сервера](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) сервера, поэтому данные копируются непосредственно между серверами хранения данных. Эти операции копирования не используют пропускную способность сети вашего компьютера. Вы можете увеличить пропускную стоимость этих операций, установив значение переменной среды. `AZCOPY_CONCURRENCY_VALUE` Чтобы узнать больше, [см.](storage-use-azcopy-configure.md#optimize-throughput)

> [!NOTE]
> Этот сценарий имеет следующие ограничения в текущем выпуске.
>
> - Вы должны придать маркер SAS токену к каждому URL-адресу источника. Если вы предоставляете учетные данные авторизации с помощью Active Directory (AD), вы можете пропустить токен SAS только из URL-адреса назначения.
>-  Учетные записи хранения премиум-блоков не поддерживают уровни доступа. Опускай уровень доступа капли из операции `s2s-preserve-access-tier` копирования, установив к `false` (Например: `--s2s-preserve-access-tier=false`).

Этот раздел содержит следующие примеры.

> [!div class="checklist"]
> * Копирование капли на другой счет хранения
> * Копирование каталога на другую учетную запись хранения
> * Копирование контейнера на другую учетную запись хранения
> * Копирование всех контейнеров, каталогов и файлов в другую учетную запись хранения

Эти примеры также работают с учетными записями, которые имеют иерархическое пространство имен. [Многопротоколный доступ к хранению data Lake](../blobs/data-lake-storage-multi-protocol-access.md) позволяет использовать`blob.core.windows.net`тот же синтаксис URL-адреса () на этих учетных записях.

> [!TIP]
> Вы можете настроить операцию копирования с помощью дополнительных флагов. Вот несколько примеров.
>
> |Сценарий|Флаг|
> |---|---|
> |Копирование файлов в виде приложения Blobs или Page Blobs.|**--blob типа**=\[BlockBlob PageBlob\|\|ПриложениеBlob\]|
> |Копирование на определенный уровень доступа (например, уровень архива).|**--блок-blob-уровня**=\[\|None\|\|Hot Cool Архив\]|
> |Автоматически декомпрессии файлов.|**--декомпресс**=\[Gzip\|сдуваться\]|
> 
> Для полного списка [см.](storage-ref-azcopy-copy.md#options)

### <a name="copy-a-blob-to-another-storage-account"></a>Копирование капли на другой счет хранения

Используйте тот же`blob.core.windows.net`синтаксис URL () для учетных записей, которые имеют иерархическое пространство имен.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Пример** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Копирование каталога на другую учетную запись хранения

Используйте тот же`blob.core.windows.net`синтаксис URL () для учетных записей, которые имеют иерархическое пространство имен.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Пример** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Пример** (иерархическое пространство имен)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Копирование контейнера на другую учетную запись хранения

Используйте тот же`blob.core.windows.net`синтаксис URL () для учетных записей, которые имеют иерархическое пространство имен.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Пример** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Пример** (иерархическое пространство имен)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Копирование всех контейнеров, каталогов и капли на другой счет хранения

Используйте тот же`blob.core.windows.net`синтаксис URL () для учетных записей, которые имеют иерархическое пространство имен.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Пример** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **Пример** (иерархическое пространство имен)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Синхронизация файлов

Содержимое локальной файловой системы можно синхронизировать с контейнером с каплей. Вы также можете синхронизировать контейнеры и виртуальные каталоги друг с другом. Синхронизация односторонней. Другими словами, вы выбираете, какая из этих двух конечных точек является источником, а какая – пунктом назначения. Синхронизация также использует сервер для серверных AIS. Примеры, приведенные в этом разделе, также работают с учетными записями, которые имеют иерархическое пространство имен. 

> [!NOTE]
> Текущий выпуск AzCopy не синхронизируется между другими источниками и пунктами назначения (например: Хранение файлов или Amazon Web Services (AWS) S3 ведра).

Команда [синхронизации](storage-ref-azcopy-sync.md) сравнивает имена файлов и последние измененные метки времени. Установите `--delete-destination` дополнительный флаг `true` на `prompt` значение или удалить файлы в каталоге назначения, если эти файлы больше не существуют в исходном каталоге.

При установке `--delete-destination` флага `true` AzCopy файлы удаляются без предоставления запроса. Если вы хотите, чтобы запрос появился до того, как AzCopy удалил файл, установите `--delete-destination` флаг. `prompt`

> [!NOTE]
> Чтобы предотвратить случайные удаления, не забудьте включить [функцию мягкого удаления](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) перед использованием флага. `--delete-destination=prompt|true`

> [!TIP]
> Вы можете настроить операцию синхронизации с помощью дополнительных флагов. Вот несколько примеров.
>
> |Сценарий|Флаг|
> |---|---|
> |Укажите, как строго следует проверять хэши MD5 при загрузке.|**--Check-md5**=\[NoCheck\|\|Logonly\|FailIfDifferent FailIfDifferentOrMissing\]|
> |Исключить файлы на основе шаблона.|**--исключить-путь**|
> |Укажите, насколько подробными вы хотите, чтобы записи журнала, связанные с синхронизацией, были.|**--Лог-уровня**=\[\|ВНИМАНИЕ\|\|ERROR INFO НЕТ\]|
> 
> Для полного списка [см.](storage-ref-azcopy-sync.md#options)

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Обновление контейнера с изменениями в локальной файловой системе

В этом случае контейнер является пунктом назначения, а локальная файловая система — источником. 

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Пример** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Обновление локальной файловой системы с изменениями в контейнере

В этом случае локальная файловая система является пунктом назначения, а контейнер — источником.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Пример** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>Обновление контейнера с изменениями в другом контейнере

Первый контейнер, который отображается в этой команде, является источником. Второй - пункт назначения.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Пример** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Обновление каталога с изменениями в каталоге в другом файле

Первым каталогом, который отображается в этой команде, является источник. Второй - пункт назначения.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Пример** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>Дальнейшие действия

Найдите больше примеров в любой из этих статей:

- [Get started with AzCopy](storage-use-azcopy-v10.md) (Начало работы с AzCopy)

- [Руководство. Перенос локальных данных в облачное хранилище с помощью AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md) (Передача данных с помощью AzCopy и хранилища файлов)

- [Передача данных с помощью AzCopy и контейнеров Amazon S3](storage-use-azcopy-s3.md)

- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md) (Настройка, оптимизация и устранение неполадок с AzCopy)
