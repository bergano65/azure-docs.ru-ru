---
title: Выборка данных из хранилища больших двоичных объектов Azure — Процесс обработки и анализа данных группы
description: Создание выборки данных, содержащихся в хранилище BLOB-объектов Azure, путем их программной загрузки и последующего построения выборки с использованием процедур на языке Python.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 04528d28e9f54710cd0a63372e32b099c2e07fb5
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86026174"
---
# <a name="sample-data-in-azure-blob-storage"></a><a name="heading"></a>Выборка данных в хранилище BLOB-объектов Azure

В этой статья описывается выборка данных, содержащихся в хранилище BLOB-объектов Azure, путем их программной загрузки и последующей выборки с использованием процедур на языке Python.

**Для чего нужна выборка данных?**
Если размер набора данных, который планируется проанализировать, слишком большой, обычно рекомендуется уменьшить выборку данных до размера, который останется репрезентативным и будет более управляемым. Выборка упрощает понимание данных, исследование и проектирование признаков. Роль этой операции в процессе аналитики Кортаны заключается в том, чтобы сделать возможным быстрое прототипирование функций обработки данных и моделей машинного обучения.

Эта задача выборки является одним из этапов [процесса обработки и анализа данных группы (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="download-and-down-sample-data"></a>Скачать данные и уменьшить выборку данных
1. Скачайте данные из хранилища BLOB-объектов Azure с помощью службы BLOB-объектов из следующего примера кода Python: 

    ```python
    from azure.storage.blob import BlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>        
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    #download from blob
    t1=time.time()
    blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+blobname) % (t2 - t1))
    ```

2. Считать данные во фрейм данных Pandas из файла, загруженного выше.

    ```python
    import pandas as pd

    #directly ready from file on disk
    dataframe_blobdata = pd.read_csv(LOCALFILE)
    ```

3. Уменьшить выборку данных с помощью `random.choice``numpy`:

    ```python
    # A 1 percent sample
    sample_ratio = 0.01 
    sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
    sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
    dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]
    ```

Теперь вы можете работать с приведенным выше кадром данных с примером "один процент" для дальнейшего исследования и создания компонентов.

## <a name="upload-data-and-read-it-into-azure-machine-learning"></a><a name="heading"></a>Передача данных и их считывание в службу машинного обучения Azure
С помощью следующего образца кода можно уменьшить выборку данных и использовать их непосредственно в Машинном обучении Azure.

1. Запись фрейма данных в локальный файл

    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```

2. Передача локального файла в BLOB-объект Azure с помощью следующего образца кода:

    ```python
    from azure.storage.blob import BlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    LOCALFILENAME= <local_file_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory

    try:

    #perform upload
    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)

    except:            
        print ("Something went wrong with uploading to the blob:"+ BLOBNAME)
    ```

3. Считывание данных из большого двоичного объекта Azure с помощью модуля [Импорт данных](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) Машинного обучения Azure, как показано на рисунке ниже.

![большой двоичный объект считывателя](./media/sample-data-blob/reader_blob.png)

