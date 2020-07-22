---
title: Загрузка данных в среды службы хранилища Azure. процесс обработки и анализа данных группы
description: Сведения о приеме данных в различных целевых средах, где хранятся и обрабатываются данные.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76720543"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Загрузка данных в среды хранения для аналитики

Процесс обработки и анализа данных группы требует, чтобы данные были приняты или загружены наиболее подходящим образом на каждом этапе. Назначения данных могут включать хранилище BLOB-объектов Azure, базы данных SQL Azure, SQL Server на виртуальной машине Azure, HDInsight (Hadoop), синапсе Analytics и Машинное обучение Azure. 

В следующих статьях описывается, как выполняется прием данных в различных целевых средах, где данные могут храниться и обрабатываться:

* в [хранилище BLOB-объектов Azure](move-azure-blob.md) или из него;
* в [SQL Server на виртуальной машине Azure](move-sql-server-virtual-machine.md);
* В [базу данных SQL Azure](move-sql-azure.md)
* в [таблице Hive](move-hive-tables.md);
* в [секционированных таблицах SQL](parallel-load-sql-partitioned-tables.md);
* из [локальной базы данных SQL Server](move-sql-azure-adf.md).

Лучшие планы приема данных определяют технические и бизнес-потребности, а также начальное расположение, формат и размер данных. Нередко оптимальным планом является наличие нескольких шагов. Эта последовательность задач может включать в себя, например, просмотр данных, предварительную обработку, очистку, понижение частоты выборки и обучение модели.  Фабрика данных Azure — это рекомендуемый ресурс Azure для управления перемещением и преобразованием данных.
