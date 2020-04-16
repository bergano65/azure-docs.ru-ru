---
title: Используйте .NET для Apache Spark с помощью аналитики Azure Synapse
description: Узнайте об использовании .NET и Apache Spark для обработки пакетов, потоковой передачи в реальном времени, машинного обучения и записи специальных запросов в блокнотах Azure Synapse Analytics.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 6943be033653d88b272817d2dcf58042aaaab616
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430517"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Используйте .NET для Apache Spark с помощью аналитики Azure Synapse

[.NET для Apache Spark](https://dot.net/spark) является бесплатной, с открытым исходным кодом и кросс-платформенной поддержкой .NET для Spark. .NET для Apache Spark предоставляет привязки .NET для Spark, которые позволяют получить доступ к AIS Spark через C и F. С помощью .NET для Apache Spark у вас есть возможность писать и выполнять функции, определяемые пользователем, для Spark с помощью .NET. AIS .NET для Spark позволяют получить доступ ко всем аспектам Spark, которые помогут вам анализировать ваши данные, включая Spark S'L и структурированную потоковую передачу.

Вы можете анализировать данные с помощью .NET для Apache Spark с помощью определений пакетных работ Spark или с помощью интерактивных ноутбуков Azure Synapse Analytics. В этой статье вы узнаете, как использовать .NET для Apache Spark с Azure Synapse с использованием обоих методов. 

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Отправка пакетных заданий с помощью определения задания Spark

Посетите учебник, чтобы узнать, как использовать аналитику Azure Synapse для [создания определений вакансий Apache Spark для бассейнов Synapse Spark.](apache-spark-job-definitions.md) Если вы не упаковали приложение для отправки в Azure Synapse, выполните следующие шаги.

1. Выполнить следующие команды для публикации приложения. Не забудьте заменить *mySparkApp* с пути к вашему приложению.

   **На Windows:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **На Linux:**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET для Apache Spark в ноутбуках Azure Synapse Analytics

При создании нового ноутбука вы выбираете ядро языка, которое вы хотите выразить свою бизнес-логику. Существует поддержка ядра для нескольких языков, в том числе C.

Чтобы использовать .NET для Apache Spark в блокноте Azure Synapse Analytics, выберите в качестве ядра **.NET Spark (C)** и прикрепите ноутбук к существующему пулу Spark.

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET для Apache Spark в ноутбуках Azure Synapse Analytics 

Ноутбуки являются отличным вариантом для прототипирования конвейеров и сценариев Apache Spark. Вы можете начать работать с пониманием, фильтрацией, отображением и визуализацией данных быстро и эффективно. Инженеры по обработке данных, специалисты по обработке данных, бизнес-аналитики и инженеры машинного обучения могут сотрудничать в области совместного использования совместного интерактивного документа. Вы видите непосредственные результаты исследования данных и можете визуализировать свои данные в одном блокноте.

### <a name="how-to-use-notebooks"></a>Как использовать записные книжки

При создании нового ноутбука вы выбираете ядро языка, которое вы хотите выразить свою бизнес-логику. Существует поддержка ядра для нескольких языков, в том числе C. 

Чтобы использовать .NET для Apache Spark в блокноте Azure Synapse Analytics, выберите в качестве ядра **.NET Spark (C)** и прикрепите ноутбук к существующему пулу Spark. 

Ноутбук .NET Spark основан на интерактивном опыте .NET и предоставляет интерактивный опыт C-сс возможностью использования `spark` .NET для Spark из коробки с переменной сессии Spark, уже предопределенной.

### <a name="sparknet-c-kernel-features"></a>Spark.NET особенности ядра C

Следующие функции доступны при использовании .NET для Apache Spark в блокноте Azure Synapse Analytics:

* Декларативный HTML: Генерировать выход из ваших ячеек с помощью HTML-синтаксиса, таких как заголовки, пулевые списки и даже отображение изображений.
* Простые операторы C »(например, задания, печать для консоли, метательные исключения и так далее).
* Многолинейные блоки кода C '(например, если операторы, циклы foreach, определения классов и так далее).
* Доступ к стандартной библиотеке СЗ (например, System, LIN, Enumerables и так далее).
* Поддержка [языковых функций C'8.0.](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* «искра» как заранее определенная переменная, чтобы дать вам доступ к сеансу Apache Spark.
* Поддержка определения [функций, определяемых пользователями .NET, которые могут выполняться в пределах Apache Spark.](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql)
* Поддержка визуализации выходных данных из заданий Spark с использованием различных диаграмм (таких как строка, бар или гистограмма) и макетов (например, одноместных, наложенных и так далее) с помощью `XPlot.Plotly` библиотеки.
* Возможность включения пакетов NuGet в свой ноутбук на C'.

## <a name="next-steps"></a>Дальнейшие действия

* [.NET для документации Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET Интерактивный](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)