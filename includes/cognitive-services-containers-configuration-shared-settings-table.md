---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7ccbc6c06419d22add7c52829069bb858cb35cf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73484105"
---
К контейнеру применяются следующие параметры конфигурации:

|Обязательно|Параметр|Назначение|
|--|--|--|
|Да|[ApiKey](#apikey-configuration-setting)|Отслеживает данные для выставления счетов.|
|нет|[ApplicationInsights](#applicationinsights-setting)|Позволяет добавить в контейнер поддержку телеметрии [Azure Application Insights](https://docs.microsoft.com/azure/application-insights).|
|Да|[Выставление счетов](#billing-configuration-setting)|Задает URI конечной точки для ресурса службы в Azure.|
|Да|[Лицензионное соглашение](#eula-setting)| Указывает, что вы приняли условия лицензии для контейнера.|
|нет|[Fluentd](#fluentd-settings)|Записывает данные в журнал и при необходимости передает метрики на сервер Fluentd.|
|нет|HTTP Прокси|Настраивает прокси-сервер HTTP для исходящих запросов.|
|нет|[Ведение журнала](#logging-settings)|Обеспечивает поддержку ведения журнала ASP.NET Core для вашего контейнера. |
|нет|[Подключения](#mount-settings)|Считывает и записывает данные с главного компьютера в контейнер и обратно.|
