---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2019
ms.openlocfilehash: 1a3de57446c5c92afbc5dd5901e284bc3580f9db
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54479017"
---
К контейнеру применяются следующие параметры конфигурации.

|Обязательно|Параметр|Назначение|
|--|--|--|
|Yes|[apiKey](#apikey-setting)|Используется для отслеживания данных для выставлении счетов.|
|Нет |[ApplicationInsights](#applicationinsights-setting)|Позволяет добавить в контейнер поддержку телеметрии [Azure Application Insights](https://docs.microsoft.com/azure/application-insights).|
|Yes|[Выставление счетов](#billing-setting)|Задает URI конечной точки для ресурса службы в Azure.|
|Yes|[Лицензионное соглашение](#eula-setting)| Указывает, что вы приняли условия лицензии для контейнера.|
|Нет |[Fluentd](#fluentd-settings)|Записывает данные в журнал и (необязательно) передает метрики на сервер Fluentd.|
|Нет |[Прокси-сервер HTTP](#http-proxy-credentials-settings)|Настройка прокси-сервера HTTP для исходящих запросов.|
|Нет |[ведению журналов](#logging-settings)|Обеспечивает поддержку ведения журнала ASP.NET Core для вашего контейнера. |
|Нет |[Подключения](#mount-settings)|Читает и записывает данные с главного компьютера в контейнер и обратно.|
