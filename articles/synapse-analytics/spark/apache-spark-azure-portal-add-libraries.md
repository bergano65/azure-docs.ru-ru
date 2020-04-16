---
title: Добавление и управление библиотеками для Apache Spark в Azure Synapse Analytics
description: Узнайте, как добавлять и управлять библиотеками, используемыми Apache Spark в Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 83dfd1b4df37018329b5d7a707e9b65fdf0782a6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427839"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Добавление и управление библиотеками для Apache Spark в Azure Synapse Analytics

Apache Spark зависит от многих библиотек, чтобы обеспечить функциональность. Эти библиотеки могут быть дополнены или заменены дополнительными библиотеками или обновленными версиями старых библиотек.

Пакеты Python можно добавлять на уровне пула Spark (предварительный просмотр), а пакеты на основе .jar — на уровне определения рабочих мест Spark.

## <a name="adding-or-updating-python-libraries"></a>Добавление или обновление библиотек Python

Apache Spark в Azure Synapse Analytics имеет полную установку Anacondas плюс дополнительные библиотеки. Полный список библиотек можно найти в [поддержке версии Apache Spark.](apache-spark-version-support.md)

При запуске экземпляра Spark создается новая виртуальная среда с помощью этой установки в качестве основы. Кроме того, файл *requirements.txt* (выход из `pip freeze` команды) может быть использован для обновления виртуальной среды. Пакеты, перечисленные в этом файле для установки или обновления, загружаются из PyPi во время кластерного запуска. Этот файл требований используется каждый раз, когда экземпляр Spark создается из этого пула Spark.

> [!IMPORTANT]
>
> - Если установка пакета большой или требует длительного времени для установки, это влияет на время запуска экземпляра Spark.
> - Пакеты, требующие поддержки компилятора во время установки, такие как GCC, не поддерживаются.
> - Пакеты не могут быть понижены, только добавлены или обновлены.

### <a name="requirements-format"></a>Формат требований

Следующий фрагмент показывает формат файла требований. Название пакета PyPi перечислено вместе с точной версией. Этот файл следует формату, описанного в справочной документации [о замораживании пипсов.](https://pip.pypa.io/en/stable/reference/pip_freeze/) Этот пример прикрепляет определенную версию. В этом файле также можно указать "не больше" и "меньше, чем" версии.

```
absl-py==0.7.0

adal==1.2.1

alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Пользовательский интерфейс библиотеки Python

UI для добавления библиотек находится во **вкладке Дополнительные настройки страницы** **пула Create Apache Spark** на портале Azure.

Загрузите файл конфигурации среды с помощью селектора файлов в разделе **Пакеты страницы.**

![Добавление библиотек Python](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "Добавление библиотек Python")

## <a name="next-steps"></a>Дальнейшие действия

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Документация Apache Spark](https://spark.apache.org/docs/2.4.4/)
