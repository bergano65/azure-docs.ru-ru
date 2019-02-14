---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2019
ms.openlocfilehash: 5228e4986993f792dceb7324bb6ba9f836f29aea
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56213179"
---
К контейнеру применяются следующие параметры конфигурации.

|Обязательно|Параметр|Назначение|
|--|--|--|
|Да|[ApiKey](#apikey-configuration-setting)|Используется для отслеживания данных для выставлении счетов.|
|Нет |[ApplicationInsights](#applicationinsights-setting)|Позволяет добавить в контейнер поддержку телеметрии [Azure Application Insights](https://docs.microsoft.com/azure/application-insights).|
|Да|[Выставление счетов](#billing-configuration-setting)|Задает URI конечной точки для ресурса службы в Azure.|
|Да|[Лицензионное соглашение](#eula-setting)| Указывает, что вы приняли условия лицензии для контейнера.|
|Нет |[Fluentd](#fluentd-settings)|Записывает данные в журнал и (необязательно) передает метрики на сервер Fluentd.|
|Нет |[прокси-серверу HTTP](#http-proxy-credentials-settings)|Настраивает прокси-сервер HTTP для исходящих запросов.|
|Нет |[Logging](#logging-settings)|Обеспечивает поддержку ведения журнала ASP.NET Core для вашего контейнера. |
|Нет |[Подключения](#mount-settings)|Читает и записывает данные с главного компьютера в контейнер и обратно.|
