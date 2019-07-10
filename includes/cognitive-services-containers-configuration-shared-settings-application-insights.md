---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 046c5c5e32c71364e1bf54551989e19eec4937e4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712432"
---
Параметр `ApplicationInsights` позволяет добавить в контейнер поддержку телеметрии [Azure Application Insights](https://docs.microsoft.com/azure/application-insights). Служба Application Insights обеспечивает детализированный мониторинг контейнера. Вы можете легко отслеживать доступность, производительность и использование своего контейнера. Вы также можете быстро идентифицировать и диагностировать ошибки в контейнере.

В следующей таблице описаны параметры конфигурации, поддерживаемые в разделе `ApplicationInsights`.

|Обязательно для заполнения| ИМЯ | Тип данных | Описание |
|--|------|-----------|-------------|
|Нет| `InstrumentationKey` | Строка, | Ключ инструментирования экземпляра Application Insights, которому отправляются данные телеметрии для контейнера. Дополнительные сведения см. в статье [Application Insights для ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Пример:<br>`InstrumentationKey=123456789`|

