---
title: Предварительная версия функций Azure Stream Analytics
description: В этой статье перечислены функции Azure Stream Analytics, которые сейчас доступны в предварительной версии.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 8/07/2020
ms.openlocfilehash: e11d5b14bdf6b134fefea79a1f709ec73499bd20
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815714"
---
# <a name="azure-stream-analytics-preview-features"></a>Предварительная версия функций Azure Stream Analytics

В этой статье перечислены все функции Azure Stream Analytics, которые сейчас доступны в предварительной версии. Предварительную версию функций не рекомендуется использовать в рабочей среде.

## <a name="public-previews"></a>Общедоступные предварительные версии

В общедоступную предварительную версию входят следующие функции. Вы можете воспользоваться этими функциями уже сегодня, однако не используйте их в рабочей среде.

### <a name="authenticate-to-sql-database-output-with-managed-identities"></a>Проверка подлинности в выходных данных Базы данных SQL с управляемыми удостоверениями

Azure Stream Analytics поддерживает [проверку подлинности управляемого удостоверения](../active-directory/managed-identities-azure-resources/overview.md) для приемников выходных данных Базы данных SQL Azure. Управляемые удостоверения устраняют ограничения методов проверки подлинности на основе пользователей, например необходимость повторной проверки подлинности из-за изменения пароля. 

### <a name="output-to-azure-synapse-analytics"></a>Вывод в Azure Synapse Analytics

Задания Azure Stream Analytics могут выводиться в таблицу пула SQL в [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) и обрабатывать пропускную способность до 200 МБ/сек. Это удовлетворяет самые ресурсоемкие требования аналитики в режиме реального времени и обработку данных по горячим путям для таких рабочих нагрузок, как составление отчетов и панель мониторинга.  

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Высокопроизводительная оценка в режиме реального времени с помощью пользовательских моделей ML, управляемых Машинным обучением Azure.

Azure Stream Analytics поддерживает высокопроизводительную оценку в режиме реального времени, используя предварительно подготовленные модели Машинного обучения, управляемые Машинным обучением Azure и размещенные в Службе Azure Kubernetes Service (AKS) или Экземплярах контейнеров Azure (ACI), используя рабочий процесс, не требующий написания кода. [Регистрация](https://aka.ms/asapreview1) для предварительной версии

### <a name="c-custom-de-serializers"></a>Пользовательские десериализаторы на C#
Разработчики могут использовать возможности Azure Stream Analytics для обработки данных в Protobuf, XML или любом другом пользовательском формате. Вы можете реализовать [пользовательские десериализаторы](custom-deserializer-examples.md) на C#, которые затем можно использовать для десериализации событий, получаемых Azure Stream Analytics.

### <a name="extensibility-with-c-custom-code"></a>Расширяемость с помощью пользовательского кода на C#

Разработчики, создающие модули Stream Analytics в облаке или IoT Edge, могут записывать или повторно использовать пользовательские функции C# и вызывать их непосредственно в запросе через [пользовательские функции](stream-analytics-edge-csharp-udf-methods.md).

### <a name="debug-query-steps-in-visual-studio"></a>Отладка шагов запроса в Visual Studio

Вы можете легко просмотреть промежуточный набор строк на диаграмме данных при локальном тестировании в инструментах Azure Stream Analytics для Visual Studio. 


### <a name="live-data-testing-in-visual-studio"></a>Динамическое тестирование данных в Visual Studio

Инструменты Visual Studio для Azure Stream Analytics усовершенствуют локальный компонент тестирования, который позволяет тестировать запросы к событиям прямой трансляции из облачных источников, таких как Концентратор событий или центр Интернета вещей. Дополнительные сведения см. в статье [Тестирование реальных данных в локальной среде с помощью инструментов Azure Stream Analytics для Visual Studio (предварительная версия)](stream-analytics-live-data-local-testing.md).

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code для Azure Stream Analytics

Задания Azure Stream Analytics можно создать в Visual Studio Code. Дополнительные сведения см. в [руководстве по началу работы в VS Code](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Локальное тестирование с фактическими данными в Visual Studio Code

Перед отправкой задания в Azure вы можете протестировать ваши запросы на предмет наличия данных на локальном компьютере в режиме реального времени. Каждая тестовая итерация занимает в среднем менее двух-трех секунд, что делает процесс разработки очень эффективным.

## <a name="other-previews"></a>Другие предварительные версии

Следующие функции также доступны в предварительной версии по запросу.

### <a name="support-for-azure-stack"></a>Поддержка Azure Stack
Эта функция, включенная в среду выполнения Azure IoT Edge, использует пользовательские функции Azure Stack, такие как встроенная поддержка локальных входов и выходов, работающих в Azure Stack (например, Центры событий Azure, Центр Интернета вещей, хранилище BLOB-объектов). Эта новая интеграция позволяет создавать гибридные архитектуры, которые могут анализировать данные вблизи места их создания, снижая задержки и максимизируя аналитические сведения.
Эта функция позволяет создавать гибридные архитектуры, которые могут анализировать данные вблизи места их создания, снижая задержки и максимизируя аналитические сведения. Чтобы получить эту предварительную версию, необходимо [зарегистрироваться](https://aka.ms/asapreview1).
