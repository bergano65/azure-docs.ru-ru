---
title: Использование .NET для Apache Spark с помощью Azure синапсе Analytics
description: Узнайте об использовании .NET и Apache Spark для пакетной обработки, потоковой передачи в режиме реального времени, машинного обучения и написания нерегламентированных запросов в записных книжках Azure синапсе Analytics.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 6943be033653d88b272817d2dcf58042aaaab616
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430517"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Использование .NET для Apache Spark с помощью Azure синапсе Analytics

[.NET для Apache Spark](https://dot.net/spark) — бесплатная, открытая и кросс-платформенная поддержка .NET для Spark. .NET для Apache Spark предоставляет привязки .NET для Spark, которые позволяют получать доступ к API Spark через C# и F #. Используя .NET для Apache Spark, вы можете писать и выполнять определяемые пользователем функции для Spark с помощью .NET. Интерфейсы API .NET для Spark позволяют получить доступ ко всем аспектам Spark, которые помогают анализировать данные, включая Spark SQL и структурированную потоковую передачу.

Вы можете анализировать данные с помощью .NET для Apache Spark с помощью определений пакетных заданий Spark или интерактивных записных книжек Azure синапсе Analytics. Из этой статьи вы узнаете, как использовать .NET для Apache Spark с Azure синапсе с помощью обоих методов. 

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Отправка пакетных заданий с помощью определения задания Spark

Сведения об использовании Azure синапсе Analytics для [создания определений заданий Apache Spark для пулов синапсе Spark](apache-spark-job-definitions.md)см. в этом руководстве. Если вы не упакованы в приложение для отправки в Azure синапсе, выполните следующие действия.

1. Выполните следующие команды, чтобы опубликовать приложение. Не забудьте заменить *миспаркапп* на путь к приложению.

   **В Windows:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **В Linux:**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET для Apache Spark в записных книжках Azure синапсе Analytics

При создании записной книжки следует выбрать ядро языка, для которого необходимо выразить бизнес-логику. Поддерживается поддержка ядра для нескольких языков, включая C#.

Чтобы использовать .NET для Apache Spark в записной книжке Azure синапсе Analytics, выберите **.NET Spark (C#)** в качестве ядра и подключите записную книжку к существующему пулу Spark.

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET для Apache Spark в записных книжках Azure синапсе Analytics 

Записные книжки — это отличный вариант для создания прототипов .NET для Apache Spark конвейеров и сценариев. Вы можете быстро и эффективно приступить к работе со сведениями, фильтрацией, отображением и визуализацией данных. Разработчики данных, специалисты по обработке и анализу данных, бизнес-аналитики и инженеры машинного обучения могут совместно работать над общим интерактивным документом. Вы видите немедленные результаты исследования данных и можете визуализировать данные в одной и той же записной книжке.

### <a name="how-to-use-notebooks"></a>Как использовать записные книжки

При создании новой записной книжки следует выбрать ядро языка, для которого требуется выразить бизнес-логику. Поддерживается поддержка ядра для нескольких языков, включая C#. 

Чтобы использовать .NET для Apache Spark в записной книжке Azure синапсе Analytics, выберите **.NET Spark (C#)** в качестве ядра и подключите записную книжку к существующему пулу Spark. 

Записная книжка .NET Spark основана на интерактивном интерфейсе .NET и предоставляет интерактивные возможности C# с возможностью использования .NET для Spark из Box с уже предопределенной переменной `spark` сеанса Spark.

### <a name="sparknet-c-kernel-features"></a>Функции ядра C# Spark.NET

При использовании .NET для Apache Spark в записной книжке Azure синапсе Analytics доступны следующие функции:

* Декларативный HTML: создание выходных данных из ячеек с помощью синтаксиса HTML, такого как заголовки, маркированные списки и даже отображение изображений.
* Простые инструкции C# (такие как назначения, печать в консоли, создание исключений и т. д.).
* Многострочные блоки кода C# (например, операторы If, циклы foreach, определения классов и т. д.).
* Доступ к стандартной библиотеке C# (такой как System, LINQ, Enumerable и т. д.).
* Поддержка [функций языка C# 8,0](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Spark в качестве предварительно определенной переменной для предоставления доступа к сеансу Apache Spark.
* Поддержка определения [определяемых пользователем функций .NET, которые могут выполняться в Apache Spark](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql).
* Поддержка визуализации выходных данных из заданий Spark с использованием различных диаграмм (например, линейной, линейчатой или гистограммы) и макетов (например, Single, наложенные и т. д `XPlot.Plotly` .) с помощью библиотеки.
* Возможность включения пакетов NuGet в записную книжку C#.

## <a name="next-steps"></a>Дальнейшие шаги

* [Документация по .NET для Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [Интерактивный .NET](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)