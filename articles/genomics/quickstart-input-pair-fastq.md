---
title: Отправка рабочего процесса с помощью входных FASTQ-файлов
titleSuffix: Microsoft Genomics
description: В этой статье показано, как отправить рабочий процесс в службу Microsoft Genomics, если ваши входные файлы представляют собой одну пару файлов FAST.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 3806b165e5abb661e53c6a315650d025fd42e17f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72248545"
---
# <a name="submit-a-workflow-using-fastq-file-inputs-in-microsoft-genomics"></a>Отправка рабочего процесса с помощью входных FASTQ-файлов в Microsoft Genomics

В этой статье показано, как отправить рабочий процесс в службу Microsoft Genomics, если ваши входные файлы представляют собой одну пару файлов FAST. В этом разделе предполагается, что вы уже установили и запустили клиент `msgen` и знаете, как использовать службу хранилища Azure. Если вы успешно представили рабочий процесс, используя предоставленные данные образца, вы готовы приступить к этой статье. 

## <a name="set-up-upload-your-fastq-files-to-azure-storage"></a>Настройка: передача FASTQ-файлов в службу хранилища Azure
Предположим, что у вас есть два файла — *reads_1.fq.gz* и *reads_2.fq.gz* — и вы отправили их в свою учетную запись хранения *myaccount* в Azure как **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/inputs/reads_1<span></span>.fq<span></span>.gz<span></span>** и **https://<span></span>myaccount.blob.core.<span></span>windows<span></span>.net/<span></span>inputs/<span></span>reads_2.fq<span></span>.gz<span></span>**. У вас есть URL-адрес API и ключ доступа. Вы хотите разместить выходные данные на странице **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.


## <a name="submit-your-job-to-the-msgen-client"></a>Отправка задания в клиент `msgen` 

Минимальный набор аргументов, которые потребуется предоставить клиенту `msgen` (для наглядности добавлены разрывы строк):

Для Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fq.gz ^
  --input-blob-name-2 reads_2.fq.gz ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```

Для Unix:

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads_1.fq.gz \
  --input-blob-name-2 reads_2.fq.gz \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Если вы предпочитаете использовать файл конфигурации, он должен содержать следующие строки:

```
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads_1.fq.gz
input_blob_name_2:                reads_2.fq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Отправьте файл `config.txt` с помощью этого вызова: `msgen submit -f config.txt`

## <a name="next-steps"></a>Дальнейшие действия
С помощью этой статьи вы научились отправлять пару FASTQ-файлов в службу хранилища Azure и освоили отправку рабочего процесса в службу Microsoft Genomics через клиент Python `msgen`. Чтобы узнать больше о представлении рабочего процесса и других командах, [FAQ](frequently-asked-questions-genomics.md)которые вы можете использовать в службе Microsoft Genomics, см. 
