---
title: Предварительная версия функций Azure Stream Analytics
description: В этой статье перечислены функции Azure Stream Analytics, которые сейчас доступны в предварительной версии.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 587304968cdf3a3763e47b9f8b614fe67aebf534
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798034"
---
# <a name="azure-stream-analytics-preview-features"></a>Предварительная версия функций Azure Stream Analytics

В этой статье перечислены все функции Azure Stream Analytics, которые сейчас доступны в предварительной версии. Предварительную версию функций не рекомендуется использовать в рабочей среде.

## <a name="public-previews"></a>Общедоступные предварительные версии

В общедоступную предварительную версию входят следующие функции. Вы можете воспользоваться этими функциями уже сегодня, однако не используйте их в рабочей среде.

### <a name="one-click-integration-with-event-hubs"></a>Одним щелчком интеграции с концентраторами событий 
Благодаря этой интеграции теперь можно визуализировать входящие данные и начать писать на портале концентратора событий запрос Stream Analytics с одним щелчком мыши. Когда запрос готов, можно его пробуем в несколько щелчков мышью и начать в реальном времени. Это также значительно сократить время и ресурсы для разработки решений аналитики в реальном времени. Документация доступна [здесь](https://docs.microsoft.com/azure/event-hubs/process-data-azure-stream-analytics).

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code для Azure Stream Analytics

Задания Azure Stream Analytics можно создать в Visual Studio Code. См. в разделе наших [VS Code Приступая к работе с](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).

### <a name="anomaly-detection"></a>Обнаружение аномалий

Помимо обнаружения двунаправленных, медленно положительных и медленно негативных тенденций, Azure Stream Analytics вводит новые модели машинного обучения с поддержкой обнаружения *пиков* и *спадов*. Дополнительные сведения см. в статье [обнаружение аномалий в Azure Stream Analytics](stream-analytics-machine-learning-anomaly-detection.md).

### <a name="integration-with-azure-machine-learning"></a>Интеграция со службой "Машинное обучение Azure"

Задания Stream Analytics можно масштабировать с помощью функций Машинного обучения. Дополнительные сведения об использовании функций Машинного обучения в задании Stream Analytics см. в статье [Масштабирование заданий Stream Analytics с помощью функций машинного обучения Azure](stream-analytics-scale-with-machine-learning-functions.md). Ознакомьтесь с реальным сценарием в статье [Выполнение анализа тональности с помощью Azure Stream Analytics и Машинного обучения Azure](stream-analytics-machine-learning-integration-tutorial.md).

### <a name="javascript-user-defined-aggregate"></a>Пользовательские статистические выражения JavaScript

Azure Stream Analytics поддерживает пользовательские статистические выражения (UDA) на языке JavaScript, которые позволяют реализовывать сложную бизнес-логику с отслеживанием состояния. Сведения о том, как использовать UDA, см. в документации [Пользовательские статистические выражения JavaScript в Azure Stream Analytics (предварительная версия)](stream-analytics-javascript-user-defined-aggregates.md). 

### <a name="live-data-testing-in-visual-studio"></a>Динамическое тестирование данных в Visual Studio

Инструменты Visual Studio для Azure Stream Analytics усовершенствуют локальный компонент тестирования, который позволяет тестировать запросы к событиям прямой трансляции из облачных источников, таких как Концентратор событий или центр Интернета вещей. Дополнительные сведения см. в статье [Тестирование реальных данных в локальной среде с помощью инструментов Azure Stream Analytics для Visual Studio (предварительная версия)](stream-analytics-live-data-local-testing.md).

### <a name="net-user-defined-functions-on-iot-edge"></a>Определяемые пользователем функции .NET в IoT Edge

С помощью стандартных функций .NET, определяемых пользователем, можно выполнить код .NET Standard как часть конвейера потоковой передачи. Можно создать простые классы C# или импортировать полный проект и библиотеки. В Visual Studio поддерживается полная разработка и отладка интерфейса. Дополнительные сведения см. в статье [Разработка пользовательских функций .NET Standard для заданий Edge в Azure Stream Analytics (предварительная версия)](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Другие предварительные версии

Следующие функции также доступны в предварительной версии по запросу.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge-and-cloud"></a>C#пользовательские десериализатор для Azure Stream Analytics в IoT Edge и облаком

Разработчики могут реализовывать пользовательские deserializers в C# выполнить десериализацию событий, полученных службой Azure Stream Analytics. Примеры форматов, которые можно десериализировать, включают в себя Parquet, Protobuf, XML и любой двоичный формат. Зарегистрироваться в этой предварительной версии [здесь](https://aka.ms/asapreview1).

### <a name="support-for-azure-stack"></a>Поддержка Azure Stack
Эта функция включена в среде выполнения Azure IoT Edge, использует пользовательские функции Azure Stack, такие как встроенная поддержка локальных входных данных и выводит, выполняющийся в Azure Stack (например, концентраторов событий, центр Интернета вещей, хранилище BLOB-объектов). Этой новой интеграции позволяет создавать гибридные архитектуры, которые можно анализировать данные близко к место его создания, уменьшая задержки и максимизации insights.
Зарегистрироваться в этой предварительной версии [здесь](https://aka.ms/asapreview1).

