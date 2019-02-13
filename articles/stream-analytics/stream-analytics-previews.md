---
title: Предварительная версия функций Azure Stream Analytics
description: В этой статье перечислены функции Azure Stream Analytics, которые сейчас доступны в предварительной версии.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/05/2019
ms.openlocfilehash: 09f1bdfa4c9a7a179bddf9473b553924bfb58fb7
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768568"
---
# <a name="azure-stream-analytics-preview-features"></a>Предварительная версия функций Azure Stream Analytics

В этой статье перечислены все функции Azure Stream Analytics, которые сейчас доступны в предварительной версии. Предварительную версию функций не рекомендуется использовать в рабочей среде.

## <a name="public-previews"></a>Общедоступные предварительные версии

В общедоступную предварительную версию входят следующие функции. Вы можете воспользоваться этими функциями уже сегодня, однако не используйте их в рабочей среде.

### <a name="sql-database-reference-data"></a>Эталонные данные Базы данных SQL

Azure Stream Analytics поддерживает Базу данных SQL Azure в качестве источника эталонных входных данных. Базу данных SQL можно использовать как источник эталонных данных для задания Stream Analytics на портале Azure и в Visual Studio с помощью средств Stream Analytics. Дополнительные сведения см. в статье [Use reference data from a SQL Database for an Azure Stream Analytics job (Preview)](sql-reference-data.md) (Использование эталонных данных из Базы данных SQL для задания Azure Stream Analytics (предварительная версия)).

### <a name="integration-with-azure-machine-learning"></a>Интеграция со службой "Машинное обучение Azure"

Задания Stream Analytics можно масштабировать с помощью функций Машинного обучения. Дополнительные сведения об использовании функций Машинного обучения в задании Stream Analytics см. в статье [Масштабирование заданий Stream Analytics с помощью функций машинного обучения Azure](stream-analytics-scale-with-machine-learning-functions.md). Ознакомьтесь с реальным сценарием в статье [Выполнение анализа тональности с помощью Azure Stream Analytics и Машинного обучения Azure](stream-analytics-machine-learning-integration-tutorial.md).

### <a name="javascript-user-defined-aggregate"></a>Пользовательские статистические выражения JavaScript

Azure Stream Analytics поддерживает пользовательские статистические выражения (UDA) на языке JavaScript, которые позволяют реализовывать сложную бизнес-логику с отслеживанием состояния. Сведения о том, как использовать UDA, см. в документации [Пользовательские статистические выражения JavaScript в Azure Stream Analytics (предварительная версия)](stream-analytics-javascript-user-defined-aggregates.md). 

### <a name="live-data-testing-in-visual-studio"></a>Динамическое тестирование данных в Visual Studio

Инструменты Visual Studio для Azure Stream Analytics усовершенствуют локальный компонент тестирования, который позволяет тестировать запросы к событиям прямой трансляции из облачных источников, таких как Концентратор событий или центр Интернета вещей. Дополнительные сведения см. в статье [Тестирование реальных данных в локальной среде с помощью инструментов Azure Stream Analytics для Visual Studio (предварительная версия)](stream-analytics-live-data-local-testing.md).

### <a name="net-user-defined-functions-on-iot-edge"></a>Определяемые пользователем функции .NET в IoT Edge

С помощью стандартных функций .NET, определяемых пользователем, можно выполнить код .NET Standard как часть конвейера потоковой передачи. Можно создать простые классы C# или импортировать полный проект и библиотеки. В Visual Studio поддерживается полная разработка и отладка интерфейса. Дополнительные сведения см. в статье [Разработка пользовательских функций .NET Standard для заданий Edge в Azure Stream Analytics (предварительная версия)](stream-analytics-edge-csharp-udf-methods.md).

## <a name="private-previews"></a>Закрытые предварительные версии

В закрытую предварительную версию входят следующие функции.

### <a name="anomaly-detection"></a>Обнаружение аномалий

Помимо обнаружения двунаправленных, медленно положительных и медленно негативных тенденций, Azure Stream Analytics вводит новые модели машинного обучения с поддержкой обнаружения *пиков* и *спадов*.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge"></a>Пользовательский десериализатор C# для Azure Stream Analytics в IoT Edge

Теперь разработчики могут реализовывать пользовательские десериализаторы в C# для десериализации событий, полученных в Azure Stream Analytics. Примеры форматов, которые можно десериализировать, включают в себя Parquet, Protobuf, XML и любой двоичный формат.

### <a name="managed-identities-for-azure-resource-authentication-to-azure-data-lake-storage"></a>Управляемые удостоверения для аутентификации ресурсов Azure в Azure Data Lake Storage

Теперь конвейеры, работающие в реальном времени, с управляемыми удостоверениями для аутентификации на основе ресурсов Azure можно вводить в эксплуатацию во время записи в Azure Data Lake Storage 1-го поколения, что позволяет создавать задания программно. Дополнительные сведения см. в статье [Аутентификация заданий Azure Stream Analytics с помощью управляемых удостоверений для вывода данных в Azure Data Lake Storage 1-го поколения (предварительная версия)](stream-analytics-managed-identities-adls.md).

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code для Azure Stream Analytics

Задания Azure Stream Analytics можно создать в Visual Studio Code.

## <a name="next-steps"></a>Дополнительная информация

* [Eight new features in Azure Stream Analytics](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/) (Восемь новых возможностей в службе Azure Stream Analytics)

* [4 new features now available in Azure Stream Analytics](https://azure.microsoft.com/blog/4-new-features-now-available-in-azure-stream-analytics/) (4 новые функции, которые теперь доступны в Azure Stream Analytics)
