---
title: Перенос данных в хранилище BLOB-объектов Azure или из него с помощью AzCopy V10 | Документация Майкрософт
description: Эта статья содержит набор AzCopy примеров команд, помогающих создавать контейнеры, копировать файлы и синхронизировать каталоги между локальными файловыми системами и контейнерами.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 7ff8f3d18564140b4654b1591eec5c0e1f40b7cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89077914"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Перенос данных с помощью AzCopy и хранилища BLOB-объектов

AzCopy — это служебная программа командной строки, которую можно использовать для копирования данных в учетные записи хранения, из или между ними. Эта статья содержит примеры команд, которые работают с хранилищем BLOB-объектов.

> [!TIP]
> В примерах в этой статье аргументы пути заключаются в одинарные кавычки (' '). Используйте одинарные кавычки во всех командных оболочках, кроме командной оболочки Windows (cmd.exe). Если вы используете командную оболочку Windows (cmd.exe), заключите аргументы пути в двойные кавычки ("") вместо одинарных кавычек ("").

## <a name="get-started"></a>Начало работы

Ознакомьтесь с статьей начало [работы с AzCopy](storage-use-azcopy-v10.md) , чтобы скачать AzCopy и узнать о способах предоставления учетных данных авторизации в службе хранилища.

> [!NOTE]
> В примерах в этой статье предполагается, что вы проверили подлинность удостоверения с помощью `AzCopy login` команды. Затем AzCopy использует учетную запись Azure AD для авторизации доступа к данным в хранилище BLOB-объектов.
>
> Если вы предпочитаете использовать маркер SAS для авторизации доступа к данным большого двоичного объекта, можно добавить этот маркер к URL-адресу ресурса в каждой команде AzCopy.
>
> Например: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Создание контейнера

Для создания контейнера можно использовать команду [azcopy make](storage-ref-azcopy-make.md) . В примерах этого раздела создается контейнер с именем `mycontainer` .

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Пример** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Пример** (иерархическое пространство имен) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

Подробную справочную документацию см. в разделе [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Отправка файлов

Для отправки файлов и каталогов с локального компьютера можно использовать команду [azcopy Copy](storage-ref-azcopy-copy.md) .

Этот раздел содержит следующие примеры.

> [!div class="checklist"]
> * Отправка файла
> * Отправка каталога
> * Отправка содержимого каталога 
> * Отправка конкретных файлов

> [!TIP]
> Вы можете настроить операцию отправки с помощью необязательных флагов. Вот несколько примеров.
>
> |Сценарий|Флаг|
> |---|---|
> |Отправьте файлы в виде добавочных или страничных BLOB-объектов.|**--BLOB-тип** = \[ BlockBlob \| PageBlob \| аппендблоб\]|
> |Отправьте файлы на конкретный уровень хранилища (например, на уровень архива).|**--Block-BLOB-уровень** = \[ Нет \| горячий \| стильный \| Архив\]|
> 
> Полный список см. в разделе [Параметры](storage-ref-azcopy-copy.md#options).

### <a name="upload-a-file"></a>Отправка файла

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Пример** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

Можно также передать файл, используя подстановочный знак (*) в любом месте пути к файлу или имени файла. Например: `'C:\myDirectory\*.txt'` или `C:\my*\*.txt` .

### <a name="upload-a-directory"></a>Отправка каталога

В этом примере каталог (и все файлы в этом каталоге) копируется в контейнер больших двоичных объектов. Результатом является каталог в контейнере с тем же именем.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Пример** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Чтобы скопировать в каталог в контейнере, просто укажите имя этого каталога в командной строке.

|    |     |
|--------|-----------|
| **Пример** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Если указать имя каталога, который не существует в контейнере, AzCopy создает новый каталог с этим именем.

### <a name="upload-the-contents-of-a-directory"></a>Отправка содержимого каталога

Вы можете отправить содержимое каталога, не копируя сам каталог с помощью подстановочного знака (*).

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Пример** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> Добавьте `--recursive` флаг для отправки файлов во все подкаталоги.

### <a name="upload-specific-files"></a>Отправка конкретных файлов

Вы можете передать определенные файлы с помощью полных имен файлов, частичных имен с подстановочными знаками (*) или с помощью дат и времени.

#### <a name="specify-multiple-complete-file-names"></a>Указание нескольких полных имен файлов

Используйте команду [azcopy Copy](storage-ref-azcopy-copy.md) с `--include-path` параметром. Отдельные имена файлов разделяются точкой с запятой ( `;` ).

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Пример** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

В этом примере AzCopy передает `C:\myDirectory\photos` каталог и `C:\myDirectory\documents\myFile.txt` файл. Необходимо включить `--recursive` параметр для перемещения всех файлов в `C:\myDirectory\photos` каталоге.

Кроме того, можно исключить файлы с помощью `--exclude-path` параметра. Дополнительные сведения см. в статье [azcopy Copy](storage-ref-azcopy-copy.md) Справочник по документам.

#### <a name="use-wildcard-characters"></a>Использовать подстановочные знаки

Используйте команду [azcopy Copy](storage-ref-azcopy-copy.md) с `--include-pattern` параметром. Укажите частичные имена, которые содержат подстановочные знаки. Разделяйте имена с помощью семиколин ( `;` ). 

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Пример** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

Кроме того, можно исключить файлы с помощью `--exclude-pattern` параметра. Дополнительные сведения см. в статье [azcopy Copy](storage-ref-azcopy-copy.md) Справочник по документам.

`--include-pattern`Параметры и `--exclude-pattern` применяются только к именам файлов, а не к пути.  Если необходимо скопировать все текстовые файлы, существующие в дереве каталогов, используйте `–recursive` параметр для получения всего дерева каталогов, а затем используйте `–include-pattern` и укажите, `*.txt` чтобы получить все текстовые файлы.

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>Передача файлов, которые были изменены после даты и времени 

Используйте команду [azcopy Copy](storage-ref-azcopy-copy.md) с `--include-after` параметром. Укажите дату и время в формате ISO-8601 (например: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Пример** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'` |

Подробные справочные сведения см. в документации по [azcopy Copy](storage-ref-azcopy-copy.md) .

## <a name="download-files"></a>Загрузка файлов

Для загрузки больших двоичных объектов, каталогов и контейнеров на локальный компьютер можно использовать команду [azcopy Copy](storage-ref-azcopy-copy.md) .

Этот раздел содержит следующие примеры.

> [!div class="checklist"]
> * скачать файл;
> * Загрузка каталога
> * Загрузка содержимого каталога
> * Загрузка конкретных файлов

> [!TIP]
> Вы можете настроить операцию загрузки с помощью необязательных флагов. Вот несколько примеров.
>
> |Сценарий|Флаг|
> |---|---|
> |Автоматическое распаковка файлов.|**--Распаковка**|
> |Укажите, насколько подробными должны быть записи журнала, связанные с копированием.|**--уровень** = \[ ведения журнала ПРЕДУПРЕЖДЕНИЕ \| об ошибке \| \| нет\]|
> |Укажите, следует ли перезаписывать конфликтующие файлы и большие двоичные объекты в месте назначения.|**--overwrite** = \[ истинное \| ложный \| \| запрос ифсаурценевер\]|
> 
> Полный список см. в разделе [Параметры](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Если `Content-md5` значение свойства большого двоичного объекта содержит хэш, AzCopy вычисляет хэш MD5 для скачанных данных и проверяет, соответствует ли хэш MD5, хранящийся в свойстве большого двоичного объекта `Content-md5` , соответствующему вычисляемому хэшу. Если эти значения не совпадают, загрузка завершается ошибкой, если это поведение не переопределено путем добавления `--check-md5=NoCheck` или `--check-md5=LogOnly` к команде Copy.

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

В этом примере создается каталог с именем `C:\myDirectory\myBlobDirectory` , который содержит все скачанные файлы.

### <a name="download-the-contents-of-a-directory"></a>Загрузка содержимого каталога

Вы можете скачать содержимое каталога, не копируя сам каталог с помощью подстановочного знака (*).

> [!NOTE]
> В настоящее время этот сценарий поддерживается только для учетных записей, у которых нет иерархического пространства имен.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Пример** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Добавьте `--recursive` флаг для скачивания файлов во всех подкаталогах.

### <a name="download-specific-files"></a>Загрузка конкретных файлов

Вы можете загрузить определенные файлы с помощью полных имен файлов, частичных имен с подстановочными знаками (*) или с помощью дат и времени. 

#### <a name="specify-multiple-complete-file-names"></a>Указание нескольких полных имен файлов

Используйте команду [azcopy Copy](storage-ref-azcopy-copy.md) с `--include-path` параметром. Разделяйте отдельные имена файлов с помощью семиколин ( `;` ).

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Пример** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

В этом примере AzCopy передает `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` каталог и `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` файл. Необходимо включить `--recursive` параметр для перемещения всех файлов в `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` каталоге.

Кроме того, можно исключить файлы с помощью `--exclude-path` параметра. Дополнительные сведения см. в статье [azcopy Copy](storage-ref-azcopy-copy.md) Справочник по документам.

#### <a name="use-wildcard-characters"></a>Использовать подстановочные знаки

Используйте команду [azcopy Copy](storage-ref-azcopy-copy.md) с `--include-pattern` параметром. Укажите частичные имена, которые содержат подстановочные знаки. Разделяйте имена с помощью семиколин ( `;` ).

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Пример** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Кроме того, можно исключить файлы с помощью `--exclude-pattern` параметра. Дополнительные сведения см. в статье [azcopy Copy](storage-ref-azcopy-copy.md) Справочник по документам.

`--include-pattern`Параметры и `--exclude-pattern` применяются только к именам файлов, а не к пути.  Если необходимо скопировать все текстовые файлы, существующие в дереве каталогов, используйте `–recursive` параметр для получения всего дерева каталогов, а затем используйте `–include-pattern` и укажите, `*.txt` чтобы получить все текстовые файлы.

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>Скачать файлы, которые были изменены после даты и времени 

Используйте команду [azcopy Copy](storage-ref-azcopy-copy.md) с `--include-after` параметром. Укажите дату и время в формате ISO-8601 (например: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>` |
| **Пример** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |

Подробные справочные сведения см. в документации по [azcopy Copy](storage-ref-azcopy-copy.md) .

#### <a name="download-previous-versions-of-a-blob"></a>Скачивание предыдущих версий большого двоичного объекта

Если вы включили [Управление версиями BLOB-объектов](../blobs/versioning-enable.md), вы можете скачать одну или несколько предыдущих версий большого двоичного объекта. 

Сначала создайте текстовый файл, содержащий список [идентификаторов версий](../blobs/versioning-overview.md). Каждый идентификатор версии должен находиться в отдельной строке. Пример: 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

Затем используйте команду [azcopy Copy](storage-ref-azcopy-copy.md) с `--list-of-versions` параметром. Укажите расположение текстового файла, содержащего список версий (например: `D:\\list-of-versions.txt` ).  

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-directory-path>' --list-of-versions '<list-of-versions-file>'`|
| **Пример** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt' --list-of-versions 'D:\\list-of-versions.txt'` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt' --list-of-versions 'D:\\list-of-versions.txt'` |

Имя каждого скачанного файла начинается с идентификатора версии, за которым следует имя большого двоичного объекта. 

## <a name="copy-blobs-between-storage-accounts"></a>Копирование больших двоичных объектов между учетными записями хранения

Используйте AzCopy для копирования больших двоичных объектов в другие учетные записи хранения. Операция копирования является синхронной, поэтому когда команда возвращает результат, это означает, что все файлы скопированы. 

AzCopy использует [API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)" [сервер-сервер](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) ", поэтому данные копируются непосредственно между серверами хранилища. Эти операции копирования не используют пропускную способность сети компьютера. Вы можете увеличить пропускную способность этих операций, задав значение `AZCOPY_CONCURRENCY_VALUE` переменной среды. Дополнительные сведения см. в разделе [Оптимизация пропускной способности](storage-use-azcopy-configure.md#optimize-throughput).

> [!NOTE]
> Этот сценарий имеет следующие ограничения в текущем выпуске.
>
> - Необходимо добавить маркер SAS к каждому исходному URL-адресу. Если вы предоставляете учетные данные авторизации с помощью Azure Active Directory (AD), маркер SAS можно опустить только из URL-адреса назначения. Убедитесь, что в целевой учетной записи настроены соответствующие роли. См. [вариант 1. использование Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory).
>-  Учетные записи хранения блочных BLOB-объектов уровня "Премиум" не поддерживают уровни доступа. Исключите уровень доступа большого двоичного объекта из операции копирования, задав для значение (например, `s2s-preserve-access-tier` `false` `--s2s-preserve-access-tier=false` ).

Этот раздел содержит следующие примеры.

> [!div class="checklist"]
> * Копирование большого двоичного объекта в другую учетную запись хранения
> * Копирование каталога в другую учетную запись хранения
> * Копирование контейнера в другую учетную запись хранения
> * Копировать все контейнеры, каталоги и файлы в другую учетную запись хранения

Эти примеры также работают с учетными записями, имеющими иерархическое пространство имен. [Многопротокольный доступ на Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) позволяет использовать один и тот же синтаксис URL-адреса ( `blob.core.windows.net` ) для этих учетных записей.

> [!TIP]
> Вы можете настроить операцию копирования с помощью необязательных флагов. Вот несколько примеров.
>
> |Сценарий|Флаг|
> |---|---|
> |Копирование больших двоичных объектов в виде блоков, страниц или добавочных BLOB-объектов.|**--BLOB-тип** = \[ BlockBlob \| PageBlob \| аппендблоб\]|
> |Копирование на конкретный уровень доступа (например, уровень архива).|**--Block-BLOB-уровень** = \[ Нет \| горячий \| стильный \| Архив\]|
> |Автоматическое распаковка файлов.|**--распаковка** = \[ \|деструктурирование gzip\]|
> 
> Полный список см. в разделе [Параметры](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-blob-to-another-storage-account"></a>Копирование большого двоичного объекта в другую учетную запись хранения

Используйте тот же синтаксис URL-адреса ( `blob.core.windows.net` ) для учетных записей, имеющих иерархическое пространство имен.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Пример** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Пример** (иерархическое пространство имен) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Копирование каталога в другую учетную запись хранения

Используйте тот же синтаксис URL-адреса ( `blob.core.windows.net` ) для учетных записей, имеющих иерархическое пространство имен.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Пример** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Пример** (иерархическое пространство имен)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Копирование контейнера в другую учетную запись хранения

Используйте тот же синтаксис URL-адреса ( `blob.core.windows.net` ) для учетных записей, имеющих иерархическое пространство имен.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Пример** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Пример** (иерархическое пространство имен)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Копировать все контейнеры, каталоги и большие двоичные объекты в другую учетную запись хранения

Используйте тот же синтаксис URL-адреса ( `blob.core.windows.net` ) для учетных записей, имеющих иерархическое пространство имен.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Пример** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **Пример** (иерархическое пространство имен)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Синхронизировать файлы

Вы можете синхронизировать содержимое локальной файловой системы с контейнером больших двоичных объектов. Кроме того, можно синхронизировать контейнеры и виртуальные каталоги друг с другом. Синхронизация является односторонней. Иными словами, вы выбираете, какие из этих двух конечных точек являются источником, и какой из них является назначением. Синхронизация также использует API сервера-сервера. Примеры, приведенные в этом разделе, также работают с учетными записями, имеющими иерархическое пространство имен. 

> [!NOTE]
> Текущий выпуск AzCopy не синхронизируется между другими источниками и назначениями (например, хранилище файлов или контейнеры Amazon Web Services (AWS) S3).

Команда [Sync](storage-ref-azcopy-sync.md) сравнивает имена файлов и метки времени последнего изменения. Установите `--delete-destination` необязательный флаг в значение `true` или, `prompt` чтобы удалить файлы в целевом каталоге, если эти файлы больше не существуют в исходном каталоге.

Если установить `--delete-destination` для флага значение `true` AzCopy, удаляются файлы без указания запроса. Если требуется, чтобы запрос отображался перед тем, как AzCopy удалит файл, установите `--delete-destination` для флага значение `prompt` .

> [!NOTE]
> Чтобы предотвратить случайное удаление, обязательно включите функцию [обратимого удаления](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) , прежде чем использовать `--delete-destination=prompt|true` флаг.

> [!TIP]
> Вы можете настроить операцию синхронизации с помощью необязательных флагов. Вот несколько примеров.
>
> |Сценарий|Флаг|
> |---|---|
> |Укажите, как следует проверять хэши с строгими MD5 при скачивании.|**--Check-MD5** = \[ Не проверять \| Вход \| фаилифдифферент \| фаилифдифферентормиссинг\]|
> |Исключение файлов на основе шаблона.|**--Exclude-путь**|
> |Укажите, насколько подробными должны быть записи журнала, связанные с синхронизацией.|**--уровень** = \[ ведения журнала ПРЕДУПРЕЖДЕНИЕ \| об ошибке \| \| нет\]|
> 
> Полный список см. в разделе [Параметры](storage-ref-azcopy-sync.md#options).

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Обновление контейнера с изменениями в локальной файловой системе

В этом случае контейнер является назначением, а локальная файловая система — источником. 

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Пример** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Обновление локальной файловой системы с изменениями в контейнере

В этом случае локальная файловая система является назначением, а контейнер — источником.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Пример** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>Обновление контейнера с изменениями в другом контейнере

Первый контейнер, который отображается в этой команде, является источником. Второй — назначение.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Пример** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Обновление каталога с изменениями в каталоге в другой общей папке

Первым каталогом, который отображается в этой команде, является источник. Второй — назначение.

|    |     |
|--------|-----------|
| **Синтаксис** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Пример** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные примеры приведены в любой из следующих статей:

- [Начало работы с AzCopy](storage-use-azcopy-v10.md)

- [Руководство. Перенос локальных данных в облачное хранилище с помощью AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Перенос данных с помощью AzCopy и хранилища файлов](storage-use-azcopy-files.md)

- [Передача данных с помощью AzCopy и контейнеров Amazon S3](storage-use-azcopy-s3.md)

- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md) (Настройка, оптимизация и устранение неполадок с AzCopy)
