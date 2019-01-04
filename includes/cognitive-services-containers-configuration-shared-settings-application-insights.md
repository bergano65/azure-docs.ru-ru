---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 31a0acbfd0c9714bf2cbdf20f9f1f82edb07f05a
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977400"
---
Параметр `ApplicationInsights` позволяет добавить в контейнер поддержку телеметрии [Azure Application Insights](https://docs.microsoft.com/azure/application-insights). Служба Application Insights обеспечивает детализированный мониторинг контейнера. Вы можете легко отслеживать доступность, производительность и использование своего контейнера. Вы также можете быстро идентифицировать и диагностировать ошибки в контейнере.

В следующей таблице описаны параметры конфигурации, поддерживаемые в разделе `ApplicationInsights`.

|Обязательно| ИМЯ | Тип данных | ОПИСАНИЕ |
|--|------|-----------|-------------|
|Нет | `InstrumentationKey` | Строка | Ключ инструментирования экземпляра Application Insights, которому отправляются данные телеметрии для контейнера. Дополнительные сведения см. в статье [Application Insights для ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Пример:<br>`InstrumentationKey=123456789`|

