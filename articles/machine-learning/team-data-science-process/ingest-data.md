---
title: Загрузка данных в среды хранения Azure — Процесс обработки и анализа данных группы
description: Перемещение данных в хранилище больших двоичных объектов Azure и из него
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 56c45bf6db79ded64574a44399712951e82c1c3e
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472411"
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
