---
title: Руководство по Начало работы с Azure Synapse Analytics
description: В этом руководстве изложены основные этапы настройки и использования Azure Synapse Analytics.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: 7affc5aad89fd79e6ba6480f7bf10d37f90dc5e3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075862"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Начало работы с Azure Synapse Analytics

Этот учебник является пошаговым руководством по основным функциональным возможностям Azure Synapse Analytics. Учебник — это идеальная отправная точка для тех, кто хочет ознакомиться с основными сценариями в Azure Synapse Analytics. После выполнения действий, описанных в этом учебнике, будет создана полнофункциональная рабочая область Synapse, в которой можно начать анализ данных с помощью SQL, SQL по запросу и Apache Spark.

В этом учебнике рассматриваются следующие задачи:
* Подготовка рабочей области Synapse в подписке Azure.
* Настройка контроля доступа для учетной записи ADLSGEN2, чтобы она беспрепятственно работала с рабочей областью Synapse.
* Загрузка примера данных NYCTaxi в рабочую область Synapse, которая может использоваться SQL, SQL по запросу и Spark.
* Изменение и выполнение скриптов SQL и записных книжек Synapse с помощью Synapse Studio.
* Запрос таблиц SQL и Spark.
* Загрузка данных из таблиц SQL в кадры данных Spark.
* Загрузка данных в таблицы SQL из кадров данных Spark.
* Просмотр содержимого учетной записи ADLSGEN2.
* Анализ файлов Parquet в учетных записях ADLSGEN2 с помощью Spark и SQL по запросу. 
* Создание конвейера данных, который автоматически запускает записную книжку Synapse каждый час.

Выполните действия в указанном ниже *порядке*, чтобы ознакомиться с различными возможностями и узнать, как выполнять основные функции.

* [Шаг 1. Создание и настройка рабочей области Synapse](get-started-create-workspace.md)
* [Шаг 2. Анализ с использованием пула SQL](get-started-analyze-sql-pool.md)
* [Шаг 3. Анализ с использованием Spark](get-started-analyze-spark.md)
* [Шаг 4. Анализ с использованием SQL по запросу](get-started-analyze-sql-on-demand.md)
* [Шаг 5. Анализ данных в учетной записи хранения](get-started-analyze-storage.md)
* [Шаг 6. Оркестрация с использованием конвейеров](get-started-pipelines.md)
* [Шаг 7. Визуализация данных с помощью Power BI](get-started-visualize-power-bi.md)
