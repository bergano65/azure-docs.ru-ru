---
title: Загрузка данных в среды службы хранилища Azure для аналитики | Документация Майкрософт
description: Перемещение данных в хранилище больших двоичных объектов Azure и из него
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: a5db14b99a81c373fbc72f523798e1f3bbdf9285
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344507"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Загрузка данных в среды хранения для аналитики

Процесс обработки и анализа данных группы требует приема или загрузки данных в различные среды хранения для обработки или анализа наиболее подходящим способом на каждом этапе процесса. Для обработки обычно используются следующие места хранения данных: хранилище больших двоичных объектов Azure, базы данных SQL Azure, SQL Server на виртуальной машине Azure, HDInsight (Hadoop) и Машинное обучение Azure. 

В следующих статьях описывается, как выполняется прием данных в различных целевых средах, где данные могут храниться и обрабатываться:

* в [хранилище BLOB-объектов Azure](move-azure-blob.md) или из него;
* в [SQL Server на виртуальной машине Azure](move-sql-server-virtual-machine.md);
* в [Базе данных SQL Azure](move-sql-azure.md);
* в [таблице Hive](move-hive-tables.md);
* в [секционированных таблицах SQL](parallel-load-sql-partitioned-tables.md);
* из [локальной базы данных SQL Server](move-sql-azure-adf.md).

Целевые среды, в которые требуется принять данные, чтобы достичь целей анализа определят технические требования и потребности бизнеса, а также исходное расположение, формат и размер данных. Нередки сценарии, когда требуется перемещать данные между несколькими средами для выполнения различных задач, необходимых для построения прогнозирующей модели. Эта последовательность задач может включать в себя, например, просмотр данных, предварительную обработку, очистку, понижение частоты выборки и обучение модели.
