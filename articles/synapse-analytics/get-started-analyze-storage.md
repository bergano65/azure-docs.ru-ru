---
title: Учебник. Приступая к анализу данных в учетных записях хранения
description: В этом учебнике вы узнаете, как анализировать данные в учетной записи хранения.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 5e3fbd1868cc1216cb7b9d02b2aa8e690af33952
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917687"
---
# <a name="analyze-data-in-a-storage-account"></a>Анализируйте данные в учетной записи хранения

В этом учебнике вы узнаете, как анализировать данные в учетной записи хранения.

## <a name="overview"></a>Обзор

До сих пор были рассмотрены ситуации, когда данные находятся в базах данных в рабочей области. Теперь мы покажем, как работать с файлами в учетных записях хранения. В этом сценарии мы будем использовать основную учетную запись хранения для рабочей области и контейнера, которые мы указали при создании рабочей области.

* Имя учетной записи хранения: **contosolake**
* Имя контейнера в учетной записи хранения: **пользователи**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>Создание файлов CSV и Parquet в учетной записи хранения

Выполните приведенный ниже код в записной книжке. Он создает CSV-файл и Parquet-файл в учетной записи хранения.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats_csvformat")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats_parquetformat")
```

### <a name="analyze-data-in-a-storage-account"></a>Анализируйте данные в учетной записи хранения

Вы можете проанализировать данные в стандартной учетной записи ADLS 2-го поколения для рабочей области или связать учетную запись ADLS 2-го поколения или хранилища BLOB-объектов с рабочей областью, выбрав **Управление** > **Связанные службы** > **Создать** (приведенные ниже действия относятся к основной учетной записи ADLS 2-го поколения).

1. В Synapse Studio перейдите в центр **Данные**, а затем выберите команду **Связанный**.
1. Перейдите в раздел **Учетные записи хранения** > **myworkspace (Primary — contosolake)** .
1. Выберите **Пользователи (Основной)** . Вы увидите папку **NYCTaxi**. Внутри вы увидите две папки: **PassengerCountStats_csvformat** и **PassengerCountStats_parquetformat**.
1. Откройте папку **PassengerCountStats_parquetformat**. Внутри вы увидите PARQUET-файл следующего вида `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet`.
1. Щелкните правой кнопкой мыши **.parquet**, а затем выберите пункт **Создать записную книжку**. Он создает записную книжку с ячейкой следующего вида:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Запустите эту ячейку.
1. Щелкните правой кнопкой мыши файл Parquet и выберите команду **Новый скрипт SQL** > **Выбрать первые 100 строк**. Он создает скрипт SQL таким образом:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

    В окне скрипта для поля **Подключить к** будет установлено значение **бессерверный пул SQL**.

1. Выполните скрипт.



## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Оркестрация действий с помощью конвейеров](get-started-pipelines.md)
