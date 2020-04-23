---
title: Сравнение вариантов хранения для использования с кластерами Azure HDInsight
description: Предоставляет обзор типов хранилища и того, как они работают с Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: b351b9a4edd81da2983cca93127513f9041716bd
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869840"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Сравнение вариантов хранения для использования с кластерами Azure HDInsight

При создании кластеров HDInsight можно выбрать один из нескольких различных служб хранения данных Azure:

* [Хранилище Azure](./overview-azure-storage.md)
* [Azure Data Lake Storage 2-го поколения](./overview-data-lake-storage-gen1.md)
* [Хранилище Azure Data Lake Storage 1-го поколения](./overview-data-lake-storage-gen2.md)

В этой статье предоставлен обзор типов хранилища и их уникальных функций.

## <a name="storage-types-and-features"></a>Типы и функции хранения

В следующей таблице приведены итоги служб хранения Azure, которые поддерживаются различными версиями HDInsight:

| Служба хранения данных | Тип учетной записи | Тип пространства имен | Поддерживаемые службы | Поддерживаемые уровни производительности | Поддерживаемые уровни доступа | Версия HDInsight | Тип кластера |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage 2-го поколения| Общего назначения версии 2 | Иерархическая (файловая система) | BLOB-объект | Стандартный | Горячий, Прохладный, Архив | 3.6+ | Все, кроме Spark 2.1 и 2.2|
|Хранилище Azure| Общего назначения версии 2 | Объект | BLOB-объект | Стандартный | Горячий, Прохладный, Архив | 3.6+ | All |
|Хранилище Azure| Общего назначения версии 1 | Объект | BLOB-объект | Стандартный | Недоступно | All | All |
|Хранилище Azure| Складка Blob | Объект | Блочный BLOB-объект | Стандартный | Горячий, Прохладный, Архив | All | All |
|Хранилище Azure Data Lake Storage 1-го поколения| Недоступно | Иерархическая (файловая система) | Недоступно | Недоступно | Недоступно | 3.6 Только | Все, кроме HBase |

Для кластеров HDInsight только вторичные учетные записи хранилища могут быть типа BlobStorage, а Page Blob не является поддерживаемым вариантом хранения.

Для получения дополнительной информации о типах учетных записей Извезов Azure смотрите [обзор учетной записи хранения Azure](../storage/common/storage-account-overview.md)

Для получения дополнительной информации о уровнях доступа к хранилищам Azure можно надо получить [данные Azure Blob: Premium (предварительный просмотр), Hot, Cool и Archive](../storage/blobs/storage-blob-storage-tiers.md)

Можно создавать кластеры, используя комбинации служб для первичного и дополнительного вторичного хранения. В следующей таблице кратко излагаются конфигурации кластерных хранилищ, которые в настоящее время поддерживаются в HDInsight:

| Версия HDInsight | Первичное хранение | Вторичное хранение | Поддерживается |
|---|---|---|---|
| 3.6 & 4.0 | Общая цель V1, общая цель V2 | Общая цель V1, общая цель V2, BlobStorage (Блок Blobs) | Да |
| 3.6 & 4.0 | Общая цель V1, общая цель V2 | Data Lake Storage 2-го поколения | нет |
| 3.6 & 4.0 | Хранение данных озера Gen2 | Data Lake Storage 2-го поколения | Да |
| 3.6 & 4.0 | Хранение данных озера Gen2 | Общая цель V1, общая цель V2, BlobStorage (Блок Blobs) | Да |
| 3.6 & 4.0 | Data Lake Storage 2-го поколения | Data Lake Storage 1-го поколения | нет |
| 3.6 | Data Lake Storage 1-го поколения | Data Lake Storage 1-го поколения | Да |
| 3.6 | Data Lake Storage 1-го поколения | Общая цель V1, общая цель V2, BlobStorage (Блок Blobs) | Да |
| 3.6 | Data Lake Storage 1-го поколения | Data Lake Storage 2-го поколения | нет |
| 4,0 | Data Lake Storage 1-го поколения | Любой | нет |
| 4,0 | Общая цель V1, общая цель V2 | Data Lake Storage 1-го поколения | нет |

Это может быть одна или несколько учетных записей Data Lake Storage Gen2, если все они настроены, чтобы использовать один и тот же управляемый инталь для кластерного доступа.

> [!NOTE]
> Первичное хранилище Data Lake Storage Gen2 не поддерживается для кластеров Spark 2.1 или 2.2.

## <a name="next-steps"></a>Дальнейшие действия

* [Обзор хранения azure](./overview-azure-storage.md)
* [Обзор данных Azure Data Lake Data Gen1](./overview-data-lake-storage-gen1.md)
* [Обзор данных Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)
* [Общие сведения о хранилище Azure Data Lake Storage Gen2 (предварительная версия)](../storage/blobs/data-lake-storage-introduction.md)
* [Введение в хранилище Azure](../storage/common/storage-introduction.md)
