---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 2f5b03dd0170da9a9869183d7a412688509525ef
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56741297"
---
Параметр `ApplicationInsights` позволяет добавить в контейнер поддержку телеметрии [Azure Application Insights](https://docs.microsoft.com/azure/application-insights). Служба Application Insights обеспечивает детализированный мониторинг контейнера. Вы можете легко отслеживать доступность, производительность и использование своего контейнера. Вы также можете быстро идентифицировать и диагностировать ошибки в контейнере.

В следующей таблице описаны параметры конфигурации, поддерживаемые в разделе `ApplicationInsights`.

|Обязательно для заполнения| ИМЯ | Тип данных | ОПИСАНИЕ |
|--|------|-----------|-------------|
|Нет | `InstrumentationKey` | Строка | Ключ инструментирования экземпляра Application Insights, которому отправляются данные телеметрии для контейнера. Дополнительные сведения см. в статье [Application Insights для ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Пример:<br>`InstrumentationKey=123456789`|

