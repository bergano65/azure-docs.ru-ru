---
title: Выборка данных в различных службах Azure — командный процесс обработки и анализа данных
description: Выборка данных в контейнерах больших двоичных объектов Azure, SQL Server и таблицах Hive позволяет уменьшить их до более репрезентативного и управляемого размера.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: fc91aec80f74488125649cfe807757ba5ae49c9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60253099"
---
# <a name="heading"></a>Выборка данных в контейнерах больших двоичных объектов Azure, SQL Server и таблицах Hive

В следующих статьях описывается, как осуществлять выборку данных, которые хранятся в одной из трех служб Azure:

* Получение [**данных в контейнере больших двоичных объектов Azure**](sample-data-blob.md) осуществляется путем их программного скачивания и последующей выборки с помощью примера кода на языке Python.
* Выборка [**данных SQL Server**](sample-data-sql-server.md) осуществляется с помощью как SQL, так и языка программирования Python. 
* Выборка [**данных таблицы Hive**](sample-data-hive.md) осуществляется с помощью запросов Hive.

Эта задача выборки является одним из этапов [процесса обработки и анализа данных группы (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

**Для чего нужна выборка данных?**

Если размер набора данных, который планируется проанализировать, слишком большой, обычно рекомендуется уменьшить выборку данных до размера, который останется репрезентативным и будет более управляемым. Это способствует пониманию данных, их исследованию и проектированию характеристик. Роль этой операции в процессе аналитики Кортаны заключается в том, чтобы сделать возможным быстрое прототипирование функций обработки данных и моделей машинного обучения.

