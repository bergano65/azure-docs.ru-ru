---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 2f5b03dd0170da9a9869183d7a412688509525ef
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116685"
---
Параметр `ApplicationInsights` позволяет добавить в контейнер поддержку телеметрии [Azure Application Insights](https://docs.microsoft.com/azure/application-insights). Служба Application Insights обеспечивает детализированный мониторинг контейнера. Вы можете легко отслеживать доступность, производительность и использование своего контейнера. Вы также можете быстро идентифицировать и диагностировать ошибки в контейнере.

В следующей таблице описаны параметры конфигурации, поддерживаемые в разделе `ApplicationInsights`.

|Обязательно| ИМЯ | Тип данных | ОПИСАНИЕ |
|--|------|-----------|-------------|
|Нет | `InstrumentationKey` | Строка | Ключ инструментирования экземпляра Application Insights, которому отправляются данные телеметрии для контейнера. Дополнительные сведения см. в статье [Application Insights для ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Пример:<br>`InstrumentationKey=123456789`|

