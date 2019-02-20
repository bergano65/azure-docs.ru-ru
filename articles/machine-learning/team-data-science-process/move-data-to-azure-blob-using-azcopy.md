---
title: Копирование данных хранилища BLOB-объектов с помощью AzCopy — командный процесс обработки и анализа данных
description: Копирование данных в хранилище BLOB-объектов Azure и из него с помощью AzCopy
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6c0951eb6ad3b7651da97e1a49c5edf5ab55a199
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209580"
---
# <a name="copy-data-to-and-from-azure-blob-storage-using-azcopy"></a>Копирование данных в хранилище BLOB-объектов Azure и из него с помощью AzCopy
AzCopy — это служебная программа командной строки, разработанная для отправки, скачивания и копирования данных из хранилищ BLOB-объектов, файлов и таблиц Microsoft Azure.

Инструкции по установке программы AzCopy и дополнительные сведения о ее использовании с платформой Azure см. в статье [Приступая к работе со служебной программой командной строки AzCopy](../../storage/common/storage-use-azcopy.md).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Если используется виртуальная машина, созданная с помощью скриптов, предоставленных [виртуальными машинами для обработки и анализа данных в Azure](virtual-machines.md), то программа AzCopy уже установлена на виртуальной машине.
> 
> [!NOTE]
> Полное описание базовых принципов использования хранилища BLOB-объектов Azure см. в статьях [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) и [Основные понятия службы BLOB-объектов](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Предварительные требования
Для выполнения указаний в этом документе у вас должна быть подписка Azure, учетная запись хранения и соответствующий ключ к хранилищу данных для этой учетной записи. Чтобы отправлять и скачивать данные, необходимо знать имя учетной записи хранения Azure и ее ключ.

* Сведения о настройке подписки Azure см. на странице [Создайте бесплатную учетную запись Azure уже сегодня](https://azure.microsoft.com/pricing/free-trial/).
* Инструкции по созданию учетной записи хранения и получению сведений об учетной записи и ключах см. в статье [Об учетных записях хранения Azure](../../storage/common/storage-create-storage-account.md).

## <a name="run-azcopy-commands"></a>Выполнение команд AzCopy
Чтобы выполнить команды AzCopy, откройте командное окно и перейдите к каталогу установки AzCopy на компьютере, где располагается исполняемый файл AzCopy.exe. 

В командах AzCopy используется следующий базовый синтаксис:

    AzCopy /Source:<source> /Dest:<destination> [Options]

> [!NOTE]
> Можно добавить место установки AzCopy к системному пути, а затем выполнить команды из любого каталога. По умолчанию AzCopy устанавливается в *%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy* или *%ProgramFiles%\Microsoft SDKs\Azure\AzCopy*.
> 
> 

## <a name="upload-files-to-an-azure-blob"></a>Передача файлов в большой двоичный объект Azure
Для отправки файла используйте следующую команду:

    # Upload from local file system
    AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## <a name="download-files-from-an-azure-blob"></a>Скачивание файлов из большого двоичного объекта Azure
Для скачивания файла из большого двоичного объекта Azure используйте следующую команду:

    # Downloading blobs to local file system
    AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## <a name="copy-blobs-between-azure-containers"></a>Копирование больших двоичных объектов между контейнерами Azure
Для копирования больших двоичных объектов между контейнерами Azure используйте следующую команду:

    # Copying blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be copied. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>Советы по использованию AzCopy
> [!TIP]
> 1. При **отправке** с параметром */S* файлы отправляются рекурсивно. Без этого параметра файлы из подкаталогов не отправляются.  
> 2. При **скачивании** файла с параметром */S* выполняется рекурсивный поиск по контейнеру до тех пор, пока не будут скачаны все файлы в указанном каталоге и его подкаталогах или все файлы, соответствующие указанному шаблону в заданном каталоге или его подкаталогах.  
> 3. При использовании параметра **/Source** нельзя указать *определенный файл большого двоичного объекта* для скачивания. Чтобы скачать определенный файл, укажите имя файла большого двоичного объекта для скачивания, используя параметр */Pattern* . **/S** можно использовать для рекурсивного поиска шаблона имени файла с помощью AzCopy. Без параметра шаблона AzCopy скачивает все файлы, содержащиеся в этом каталоге.
> 
> 

