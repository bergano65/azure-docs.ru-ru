---
title: Выборка данных в контейнерах больших двоичных объектов Azure, SQL Server и таблицах Hive | Документация Майкрософт
description: Изучение данных, хранящихся в различных средах Azure.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 80a9dfae-e3a6-4cfb-aecc-5701cfc7e39d
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 60431578ba8b5c740fe5e1124dcdad32c3cb2ede
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344095"
---
# <a name="heading"></a>Выборка данных в контейнерах больших двоичных объектов Azure, SQL Server и таблицах Hive

В следующих статьях описывается, как осуществлять выборку данных, которые хранятся в одной из трех служб Azure:

* Получение [**данных в контейнере больших двоичных объектов Azure**](sample-data-blob.md) осуществляется путем их программного скачивания и последующей выборки с помощью примера кода на языке Python.
* Выборка [**данных SQL Server**](sample-data-sql-server.md) осуществляется с помощью как SQL, так и языка программирования Python. 
* Выборка [**данных таблицы Hive**](sample-data-hive.md) осуществляется с помощью запросов Hive.

Эта задача выборки является одним из этапов [процесса обработки и анализа данных группы (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

**Для чего нужна выборка данных?**

Если размер набора данных, который планируется проанализировать, слишком большой, обычно рекомендуется уменьшить выборку данных до размера, который останется репрезентативным и будет более управляемым. Это способствует пониманию данных, их исследованию и проектированию характеристик. Роль этой операции в процессе аналитики Кортаны заключается в том, чтобы сделать возможным быстрое прототипирование функций обработки данных и моделей машинного обучения.

