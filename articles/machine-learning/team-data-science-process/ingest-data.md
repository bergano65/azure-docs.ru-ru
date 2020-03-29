---
title: Загрузка данных в среды хранения данных Azure - Процесс анализа данных team
description: Узнайте о том, как глотать данные в различных целевых средах, где хранятся и обрабатываются данные.
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
ms.openlocfilehash: f8eab59d810fb825dbebf80d01d8efd2dd0a9841
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720543"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Загрузка данных в среды хранения для аналитики

Процесс team Data Science Process требует, чтобы данные понижались или загружались наиболее подходящим способом на каждом этапе. Направления данных могут включать данные о хранении данных Azure Blob Storage, базах данных S'L Azure, сервере S'L на Azure VM, HDInsight (Hadoop), Synapse Analytics и Azure Machine Learning. 

В следующих статьях описывается, как выполняется прием данных в различных целевых средах, где данные могут храниться и обрабатываться:

* в [хранилище BLOB-объектов Azure](move-azure-blob.md) или из него;
* в [SQL Server на виртуальной машине Azure](move-sql-server-virtual-machine.md);
* Для [базы данных Azure S'L](move-sql-azure.md)
* в [таблице Hive](move-hive-tables.md);
* в [секционированных таблицах SQL](parallel-load-sql-partitioned-tables.md);
* из [локальной базы данных SQL Server](move-sql-azure-adf.md).

Технические и бизнес-потребности, а также начальное местоположение, формат и размер данных будут определять наилучший план приема данных. Это не редкость для лучшего плана, чтобы иметь несколько шагов. Эта последовательность задач может включать в себя, например, просмотр данных, предварительную обработку, очистку, понижение частоты выборки и обучение модели.  Azure Data Factory — рекомендуемый ресурс Azure для организации движения и трансформации данных.
