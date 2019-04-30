---
title: Отправка рабочего процесса BAM файлового ввода - Microsoft Genomics
titleSuffix: Azure
description: В этой статье предполагается, что установлен клиент msgen и успешно запустили примеры данных через службу.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 329ac374edbffa65a1b654a05a4934828f25d5de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60780848"
---
# <a name="submit-a-workflow-using-a-bam-file-input"></a>Отправка рабочего процесса с помощью входного BAM-файла

В этой статье показано, как отправить рабочий процесс в службу Microsoft Genomics, если входной файл является одним BAM-файлом. В этом разделе предполагается, что вы уже установили и запустили клиент `msgen` и знаете, как использовать службу хранилища Azure. Если вы успешно отправили рабочий процесс с помощью предоставленных образцов данных, вы готовы перейти к этой статье. 

## <a name="set-up-upload-your-bam-file-to-azure-storage"></a>Настройка. Отправка BAM-файла в хранилище Azure
Предположим, что у вас есть один BAM-файл, *reads.bam*, и вы отправили его в свою учетную запись хранения *myaccount* в Azure как **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/inputs/reads<span></span>.bam<span></span>**. У вас есть URL-адрес API и ключ доступа. Вы хотите разместить выходные данные на странице **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.



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
  --input-blob-name-1 reads.bam ^
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
  --input-blob-name-1 reads.bam \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Если вы предпочитаете использовать файл конфигурации, он должен содержать следующие строки:

``` config.txt
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Отправьте файл `config.txt` с помощью этого вызова: `msgen submit -f config.txt`

## <a name="next-steps"></a>Дальнейшие действия
С помощью этой статьи вы научились отправлять BAM-файлы в службу хранилища Azure и освоили отправку рабочего процесса в службу Microsoft Genomics через клиент Python `msgen`. Дополнительные сведения об отправке рабочего процесса и других командах, которые можно использовать в службе Microsoft Genomics, см. в разделе [часто задаваемых вопросов](frequently-asked-questions-genomics.md). 
