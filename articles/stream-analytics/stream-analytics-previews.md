---
title: Предварительная версия функций Azure Stream Analytics
description: В этой статье перечислены функции Azure Stream Analytics, которые сейчас доступны в предварительной версии.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 3e99263f6bf472c256e1747b8567249bbd62a445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969619"
---
# <a name="azure-stream-analytics-preview-features"></a>Предварительная версия функций Azure Stream Analytics

В этой статье перечислены все функции Azure Stream Analytics, которые сейчас доступны в предварительной версии. Предварительную версию функций не рекомендуется использовать в рабочей среде.

## <a name="public-previews"></a>Общедоступные предварительные версии

В общедоступную предварительную версию входят следующие функции. Вы можете воспользоваться этими функциями уже сегодня, однако не используйте их в рабочей среде.

### <a name="online-scaling"></a>Онлайн масштабирование

При онлайн-масштабировании не требуется прекращать работу, если вам необходимо изменить распределение SU. Вы можете увеличить или уменьшить емкость SU работая, не останавливая его. Это основывается на обещании заказчика долгосрочных критически важных трубопроводов, которые Stream Analytics предлагает сегодня. Для получения дополнительной информации [см.](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus)

### <a name="c-custom-de-serializers"></a>Пользовательские десериализаторы
Разработчики могут использовать возможности Azure Stream Analytics для обработки данных в Protobuf, XML или любом пользовательском формате. Можно внедрить [пользовательские десериализаторы](custom-deserializer-examples.md) в C,, которые затем могут быть использованы для десеризации событий, полученных Azure Stream Analytics.

### <a name="extensibility-with-c-custom-code"></a>Расширяемость с помощью пользовательского кода СЗ

Разработчики, создающие модули Stream Analytics в облаке или на IoT Edge, могут писать или повторно использовать пользовательские функции C и вызывать их непосредственно в запросе с помощью [функций, определяемых пользователем.](stream-analytics-edge-csharp-udf-methods.md)


### <a name="debug-query-steps-in-visual-studio"></a>Шаги запроса отогиваем в Visual Studio

Вы можете легко просмотреть промежуточный ряд, установленный на диаграмме данных, при локальном тестировании в инструментах Azure Stream Analytics для Visual Studio. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Локальное тестирование с живыми данными в Коде Visual Studio

Вы можете протестировать свои запросы на живых данных на локальной машине, прежде чем отправить задание в Azure. Каждая итерация тестирования занимает в среднем менее двух-трех секунд, что приводит к очень эффективному процессу разработки.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code для Azure Stream Analytics

Задания Azure Stream Analytics можно создать в Visual Studio Code. Смотрите наш [VS код начала учебник](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).


### <a name="integration-with-azure-machine-learning"></a>Интеграция со службой "Машинное обучение Azure"

Задания Stream Analytics можно масштабировать с помощью функций Машинного обучения. Дополнительные сведения об использовании функций Машинного обучения в задании Stream Analytics см. в статье [Масштабирование заданий Stream Analytics с помощью функций машинного обучения Azure](stream-analytics-scale-with-machine-learning-functions.md). Ознакомьтесь с реальным сценарием в статье [Выполнение анализа тональности с помощью Azure Stream Analytics и Машинного обучения Azure](stream-analytics-machine-learning-integration-tutorial.md).


### <a name="live-data-testing-in-visual-studio"></a>Динамическое тестирование данных в Visual Studio

Инструменты Visual Studio для Azure Stream Analytics усовершенствуют локальный компонент тестирования, который позволяет тестировать запросы к событиям прямой трансляции из облачных источников, таких как Концентратор событий или центр Интернета вещей. Дополнительные сведения см. в статье [Тестирование реальных данных в локальной среде с помощью инструментов Azure Stream Analytics для Visual Studio (предварительная версия)](stream-analytics-live-data-local-testing.md).


### <a name="net-user-defined-functions-on-iot-edge"></a>Определяемые пользователем функции .NET в IoT Edge

С помощью стандартных функций .NET, определяемых пользователем, можно выполнить код .NET Standard как часть конвейера потоковой передачи. Можно создать простые классы C# или импортировать полный проект и библиотеки. В Visual Studio поддерживается полная разработка и отладка интерфейса. Дополнительные сведения см. в статье [Разработка пользовательских функций .NET Standard для заданий Edge в Azure Stream Analytics (предварительная версия)](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Другие предварительные просмотры

Следующие функции также доступны в предварительном просмотре по запросу.

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Высокая производительность в реальном времени с помощью пользовательских моделей ML, управляемых Azure Machine Learning

Azure Stream Analytics поддерживает высокопроизводительные оценки в режиме реального времени, используя пользовательские модели машинного обучения, управляемые Azure Machine Learning, и размещенные в Azure Kubernetes Service (AKS) или Azure Container Instances (ACI), используя рабочий процесс не требует, чтобы вы писали код. [Подпишитесь](https://aka.ms/asapreview1) на предварительный просмотр

### <a name="support-for-azure-stack"></a>Поддержка стека Azure
Эта функция включена во время выполнения Azure IoT Edge, использует пользовательские функции Azure Stack, такие как нативная поддержка локальных входных данных и выходов, работающих на Azure Stack (например, концентраторы событий, IoT Hub, Blob Storage). Эта новая интеграция позволяет создавать гибридные архитектуры, которые могут анализировать ваши данные близко к месту их создания, снижая задержку и максимизируя информацию.
Эта функция позволяет создавать гибридные архитектуры, которые могут анализировать ваши данные близко к месту их создания, снижая задержку и максимизируя понимание. Вы должны [подписаться](https://aka.ms/asapreview1) на этот предварительный просмотр.
