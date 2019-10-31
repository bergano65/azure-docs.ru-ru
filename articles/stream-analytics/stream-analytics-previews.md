---
title: Предварительная версия функций Azure Stream Analytics
description: В этой статье перечислены функции Azure Stream Analytics, которые сейчас доступны в предварительной версии.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: df3e8c1cd91c676c64d15c46c5acdc3d5bcfaa8e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161372"
---
# <a name="azure-stream-analytics-preview-features"></a>Предварительная версия функций Azure Stream Analytics

В этой статье перечислены все функции Azure Stream Analytics, которые сейчас доступны в предварительной версии. Предварительную версию функций не рекомендуется использовать в рабочей среде.

## <a name="public-previews"></a>Общедоступные предварительные версии

В общедоступную предварительную версию входят следующие функции. Вы можете воспользоваться этими функциями уже сегодня, однако не используйте их в рабочей среде.

### <a name="online-scaling"></a>Оперативное масштабирование

При использовании оперативного масштабирования вам не нужно прекращать работу, если вам нужно изменить выделение SU. Вы можете увеличить или уменьшить емкость SU для выполняющегося задания, не прибегая к его прерыванию. Это основано на клиентской системе долгосрочных критически важных конвейеров, которые Stream Analytics предложения сегодня. Дополнительные сведения см. в разделе [настройка Azure Stream Analytics единиц потоковой передачи](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus).

### <a name="c-custom-de-serializers"></a>C#Пользовательские десериализаторы
Разработчики могут использовать возможности Azure Stream Analytics для обработки данных в protobuf, XML или в любом пользовательском формате. Можно реализовать [пользовательские десериализаторы](custom-deserializer-examples.md) в C#, которые затем можно использовать для отмены сериализации событий, полученных Azure Stream Analytics.

### <a name="extensibility-with-c-custom-code"></a>Расширяемость C# с помощью пользовательского кода

Разработчики, создающие Stream Analytics модули в облаке или на IoT Edge, могут писать C# или повторно использовать пользовательские функции и вызывать их непосредственно в запросе с помощью [определяемых пользователем функций](stream-analytics-edge-csharp-udf-methods.md).

### <a name="managed-identity-authentication-with-power-bi"></a>Аутентификация управляемого удостоверения с помощью Power BI

Azure Stream Analytics обеспечивает полную поддержку управляемой проверки подлинности на основе удостоверений с помощью Power BI для динамического взаимодействия с панелями мониторинга.

### <a name="anomaly-detection"></a>Обнаружение аномалий

Azure Stream Analytics "модели машинного обучения поддерживают определение *пика* и *DIP* в дополнение к двунаправленному, медленному положительному и медленному обнаружению неблагоприятных тенденций. Дополнительные сведения см. [на странице обнаружение аномалий в Azure Stream Analytics](stream-analytics-machine-learning-anomaly-detection.md).

### <a name="debug-query-steps-in-visual-studio"></a>Отладка шагов запроса в Visual Studio

Вы можете легко просмотреть промежуточный набор строк на диаграмме данных при выполнении локального тестирования в Azure Stream Analytics инструментов для Visual Studio. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Локальное тестирование с использованием динамических данных в Visual Studio Code

Перед отправкой задания в Azure можно проверить запросы к данным в режиме реального времени на локальном компьютере. В среднем каждая тестовая итерация занимает менее двух – три секунды, что приводит к очень эффективному процессу разработки.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code для Azure Stream Analytics

Задания Azure Stream Analytics можно создать в Visual Studio Code. Ознакомьтесь с нашим [VS Code руководстве по началу работы](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).


### <a name="anomaly-detection"></a>Обнаружение аномалий

Помимо обнаружения двунаправленных, медленно положительных и медленно негативных тенденций, Azure Stream Analytics вводит новые модели машинного обучения с поддержкой обнаружения *пиков* и *спадов*. Дополнительные сведения см. [на странице обнаружение аномалий в Azure Stream Analytics](stream-analytics-machine-learning-anomaly-detection.md).


### <a name="integration-with-azure-machine-learning"></a>Интеграция со службой "Машинное обучение Azure"

Задания Stream Analytics можно масштабировать с помощью функций Машинного обучения. Дополнительные сведения об использовании функций Машинного обучения в задании Stream Analytics см. в статье [Масштабирование заданий Stream Analytics с помощью функций машинного обучения Azure](stream-analytics-scale-with-machine-learning-functions.md). Ознакомьтесь с реальным сценарием в статье [Выполнение анализа тональности с помощью Azure Stream Analytics и Машинного обучения Azure](stream-analytics-machine-learning-integration-tutorial.md).


### <a name="live-data-testing-in-visual-studio"></a>Динамическое тестирование данных в Visual Studio

Инструменты Visual Studio для Azure Stream Analytics усовершенствуют локальный компонент тестирования, который позволяет тестировать запросы к событиям прямой трансляции из облачных источников, таких как Концентратор событий или центр Интернета вещей. Дополнительные сведения см. в статье [Тестирование реальных данных в локальной среде с помощью инструментов Azure Stream Analytics для Visual Studio (предварительная версия)](stream-analytics-live-data-local-testing.md).


### <a name="net-user-defined-functions-on-iot-edge"></a>Определяемые пользователем функции .NET в IoT Edge

С помощью стандартных функций .NET, определяемых пользователем, можно выполнить код .NET Standard как часть конвейера потоковой передачи. Можно создать простые классы C# или импортировать полный проект и библиотеки. В Visual Studio поддерживается полная разработка и отладка интерфейса. Дополнительные сведения см. в статье [Разработка пользовательских функций .NET Standard для заданий Edge в Azure Stream Analytics (предварительная версия)](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Другие предварительные версии

Следующие функции также доступны в предварительной версии по запросу.

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Высокая производительность в режиме реального времени с пользовательскими моделями машинного обучения, управляемыми Машинное обучение Azure

Azure Stream Analytics поддерживает высокопроизводительную оценку в реальном времени, используя пользовательские предварительно обученные модели Машинное обучение, управляемые Машинное обучение Azure и размещенные в службе Azure Kubernetes Service (AKS) или службы "экземпляры контейнеров Azure" (ACI) с помощью рабочего процесса. Это не требует написания кода. [Зарегистрируйтесь](https://aka.ms/asapreview1) для просмотра

### <a name="support-for-azure-stack"></a>Поддержка Azure Stack
Эта функция, включенная в среде выполнения Azure IoT Edge, использует пользовательские функции Azure Stack, такие как собственная поддержка локальных входов и выходов, выполняемых на Azure Stack (например, концентраторы событий, центр Интернета вещей, хранилище BLOB-объектов). Эта новая интеграция позволяет создавать гибридные архитектуры, которые могут анализировать данные, близко к их созданию, снижению задержки и максимальному увеличению аналитических данных.
Эта функция позволяет создавать гибридные архитектуры, которые могут анализировать данные, близко к их созданию, снижению задержки и максимальному увеличению аналитических данных. Необходимо [зарегистрироваться](https://aka.ms/asapreview1) для просмотра этой предварительной версии.
