---
title: Machine Learning Services with R (preview)
description: This article describes Azure SQL Database Machine Learning Services (with R) and explains how it works.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ms.openlocfilehash: ca223de2bc0b26e4968d400ea418761a399dacae
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74462350"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database Machine Learning Services with R (preview)

Службы машинного обучения — это функция Базы данных SQL Azure, которая используется для выполнения скриптов R в базе данных. Эта функция включает в себя пакеты R Microsoft для высокопроизводительной прогнозной аналитики и машинного обучения. Реляционные данные можно использовать в сценариях R с помощью хранимых процедур, скрипта T-SQL, содержащего инструкции R, или кода R, содержащего T-SQL.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

> [!NOTE]
> The preview is available for single databases and elastic pools using the vCore-based purchasing model in the **general purpose** and **business critical** service tiers. In this initial preview, the **hyperscale** service tier and the **managed instance** deployment option are not supported. В настоящее время R — единственный поддерживаемый язык. В настоящее время Python не поддерживается.
>
> The preview is currently available in the following regions: West Europe, North Europe, West US 2, East US, South Central US, North Central US, Canada Central, Southeast Asia, India South, and Australia Southeast.

## <a name="what-you-can-do-with-r"></a>Возможности R

Используйте возможности языка R, чтобы доставлять расширенную аналитику и машинное обучение в базе данных. Данное свойство переносит вычисления и обработку в место хранения данных, тем самым исключая потребность в извлечении данных при помощи сети. Also, you can leverage the power of enterprise R packages to deliver advanced analytics at scale.

Службы машинного обучения включают основу дистрибутива на языке R, которая накладывается на корпоративные пакеты R от Майкрософт. Функции и алгоритмы R от Майкрософт разработаны как для масштаба, так и для служебной программы, доставки прогнозной аналитики, статистического моделирования, визуализации данных и передовых алгоритмов машинного обучения.

### <a name="r-packages"></a>Пакеты R

Most common open-source R packages are pre-installed in Machine Learning Services. Включены также следующие пакеты R от корпорации Майкрософт.

| пакет R | Описание|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro); | Microsoft R Open — это расширенный выпуск R от корпорации Майкрософт. It is a complete open-source platform for statistical analysis and data science. Она на 100 % совместима с R и включает дополнительные возможности для улучшения производительности и репродуктивности. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR — это первичная библиотека для масштабируемых R. Функции этой библиотеки являются наиболее широко используемыми. Преобразования и управление данными, формирование сводных статистических данных, визуализация, различные формы моделирования и анализа можете найти в этих библиотеках. Кроме того, функции в этих библиотеках автоматически распределяют рабочую нагрузку между доступными ядрами для параллельной обработки с возможностью работы с фрагментами данных, которыми координирует и управляет механизм вычислений. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML добавляет алгоритмы машинного обучения для создания пользовательских моделей для анализа текста, изображений и тональности. |

In addition to the pre-installed packages, you can [install additional packages](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Зарегистрируйтесь для ознакомления с предварительной версией

> [!IMPORTANT]
> Sign up for Azure SQL Database Machine Learning Services (preview) is currently closed.

Machine Learning Services with R is not recommended for production workload during the preview.

## <a name="next-steps"></a>Дальнейшие действия

- See the [key differences from SQL Server Machine Learning Services](sql-database-machine-learning-services-differences.md).
- To learn how to use R to query Azure SQL Database Machine Learning Services (preview), see the [Quickstart guide](sql-database-connect-query-r.md).
- To get started with some simple R scripts, see [Create and run simple R scripts in Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-create-script.md).
