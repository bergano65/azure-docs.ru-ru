---
title: Выборка данных в различных расположениях хранилища Azure. процесс обработки и анализа данных группы
description: Выборка данных в контейнерах больших двоичных объектов Azure, SQL Server и таблицах Hive позволяет уменьшить их до более репрезентативного и управляемого размера.
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
ms.openlocfilehash: 22e91d50227fcb44c7b90478d76379c14161ae05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "76718609"
---
# <a name="sample-data-in-azure-blob-containers-sql-server-and-hive-tables"></a><a name="heading"></a>Выборка данных в контейнерах больших двоичных объектов Azure, на сервере SQL Server и в таблицах Hive

В следующих статьях описывается, как осуществлять выборку данных, которые хранятся в одной из трех служб Azure:

* Получение [**данных в контейнере больших двоичных объектов Azure**](sample-data-blob.md) осуществляется путем их программного скачивания и последующей выборки с помощью примера кода на языке Python.
* Выборка [**данных SQL Server**](sample-data-sql-server.md) осуществляется с помощью как SQL, так и языка программирования Python. 
* Выборка [**данных таблицы Hive**](sample-data-hive.md) осуществляется с помощью запросов Hive.

Эта задача выборки является одним из этапов [процесса обработки и анализа данных группы (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

**Для чего нужна выборка данных?**

Если размер набора данных, который планируется проанализировать, слишком большой, обычно рекомендуется уменьшить выборку данных до размера, который останется репрезентативным и будет более управляемым. Уменьшении может облегчить понимание данных, исследование и проектирование признаков. Эта роль выборки в процессе аналитики Кортаны заключается в том, чтобы включить быстрое создание прототипов функций обработки данных и моделей машинного обучения.

