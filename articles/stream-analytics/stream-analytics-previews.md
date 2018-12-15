---
title: Предварительная версия функций Azure Stream Analytics
description: В этой статье перечислены функции Azure Stream Analytics, которые сейчас доступны в предварительной версии.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 637afab45e04c68777f8d1b42817c912cdc09941
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53133753"
---
# <a name="azure-stream-analytics-preview-features"></a>Предварительная версия функций Azure Stream Analytics

В этой статье перечислены все функции Azure Stream Analytics, которые сейчас доступны в предварительной версии. Предварительную версию функций не рекомендуется использовать в рабочей среде.

## <a name="public-previews"></a>Общедоступные предварительные версии

В общедоступную предварительную версию входят следующие функции. Вы можете воспользоваться этими функциями уже сегодня, однако не используйте их в рабочей среде.

### <a name="integration-with-azure-machine-learning"></a>Интеграция со службой "Машинное обучение Azure"

Задания Stream Analytics можно масштабировать с помощью функций Машинного обучения. Дополнительные сведения об использовании функций Машинного обучения в задании Stream Analytics см. в статье [Масштабирование заданий Stream Analytics с помощью функций машинного обучения Azure](stream-analytics-scale-with-machine-learning-functions.md). Ознакомьтесь с реальным сценарием в статье [Выполнение анализа тональности с помощью Azure Stream Analytics и Машинного обучения Azure](stream-analytics-machine-learning-integration-tutorial.md).

### <a name="session-windows"></a>Окна сеанса

В Stream Analytics имеется встроенная поддержка функций управления окнами. Это позволяет разработчикам выполнять сложные задания по обработке потоков с минимальными усилиями. [Окна сеанса](https://msdn.microsoft.com/azure/stream-analytics/reference/session-window-azure-stream-analytics) группируют события, поступающие одновременно, отфильтровывая периоды времени, в течение которых данные отсутствовали. Дополнительные сведения о функции управления окнами см. [здесь](stream-analytics-window-functions.md).

### <a name="blob-output-partitioning-by-custom-time"></a>Секционирование выходных данных больших двоичных объектов по настраиваемому времени

Azure Stream Analytics может выводить данные хранилища BLOB-объектов, исходя из настраиваемых атрибутов времени. Дополнительные сведения см. в статье [Пользовательские шаблоны даты и времени в пути для выходных данных хранилища BLOB-объектов Azure Stream Analytics (предварительная версия)](stream-analytics-custom-path-patterns-blob-storage-output.md).

### <a name="javascript-user-defined-aggregate"></a>Пользовательские статистические выражения JavaScript

Azure Stream Analytics поддерживает пользовательские статистические выражения (UDA) на языке JavaScript, которые позволяют реализовывать сложную бизнес-логику с отслеживанием состояния. Сведения о том, как использовать UDA, см. в документации [Пользовательские статистические выражения JavaScript в Azure Stream Analytics (предварительная версия)](stream-analytics-javascript-user-defined-aggregates.md). 

### <a name="live-data-testing-in-visual-studio"></a>Динамическое тестирование данных в Visual Studio

Инструменты Visual Studio для Azure Stream Analytics усовершенствуют локальный компонент тестирования, который позволяет тестировать запросы к событиям прямой трансляции из облачных источников, таких как Концентратор событий или центр Интернета вещей. Дополнительные сведения см. в статье [Тестирование реальных данных в локальной среде с помощью инструментов Azure Stream Analytics для Visual Studio (предварительная версия)](stream-analytics-live-data-local-testing.md).

### <a name="net-user-defined-functions-on-iot-edge"></a>Определяемые пользователем функции .NET в IoT Edge

С помощью стандартных функций .NET, определяемых пользователем, можно выполнить код .NET Standard как часть конвейера потоковой передачи. Можно создать простые классы C# или импортировать полный проект и библиотеки. В Visual Studio поддерживается полная разработка и отладка интерфейса. Дополнительные сведения см. в статье [Разработка пользовательских функций .NET Standard для заданий Edge в Azure Stream Analytics (предварительная версия)](stream-analytics-edge-csharp-udf-methods.md).

## <a name="private-previews"></a>Закрытые предварительные версии

В закрытую предварительную версию входят следующие функции. Чтобы получить доступ к этим предварительным версиям, перейдите на страницу [входа](https://aka.ms/ASApreview1) закрытой предварительной версии Azure Stream Analytics.

### <a name="anomaly-detection"></a>Обнаружение аномалий

Помимо обнаружения двунаправленных, медленно положительных и медленно негативных тенденций, Azure Stream Analytics вводит новые модели машинного обучения с поддержкой обнаружения *пиков* и *спадов*.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge"></a>Пользовательский десериализатор C# для Azure Stream Analytics в IoT Edge

Теперь разработчики могут реализовывать пользовательские десериализаторы в C# для десериализации событий, полученных в Azure Stream Analytics. Примеры форматов, которые можно десериализировать, включают в себя Parquet, Protobuf, XML и любой двоичный формат.

### <a name="blob-output-partitioning-by-custom-attribute"></a>Секционирование выходных данных больших двоичных объектов по настраиваемому атрибуту

Теперь существует возможность секционировать выходные данные Azure Stream Analytics в хранилище BLOB-объектов, основываясь на любом столбце в запросе.

### <a name="managed-identities-for-azure-resources-authentication-to-azure-data-lake-storage"></a>Управляемые удостоверения для аутентификации ресурсов Azure в Azure Data Lake Storage

Теперь конвейеры, работающие в реальном времени, с управляемыми удостоверениями для аутентификации на основе ресурсов Azure можно вводить в эксплуатацию во время записи в Azure Data Lake Storage 1-го поколения, что позволяет создавать задания программно. Дополнительные сведения см. в статье [Аутентификация заданий Azure Stream Analytics с помощью управляемых удостоверений для вывода данных в Azure Data Lake Storage 1-го поколения (предварительная версия)](stream-analytics-managed-identities-adls.md).

## <a name="next-steps"></a>Дополнительная информация

* [Eight new features in Azure Stream Analytics](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/) (Восемь новых возможностей в службе Azure Stream Analytics)

* [4 new features now available in Azure Stream Analytics](https://azure.microsoft.com/blog/4-new-features-now-available-in-azure-stream-analytics/) (4 новые функции, которые теперь доступны в Azure Stream Analytics)
