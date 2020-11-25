---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a6f399e19cadf3d6ce9edfaecb3d904e62c498aa
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006885"
---
К контейнеру применяются следующие параметры конфигурации:

|Обязательно|Параметр|Назначение|
|--|--|--|
|Да|[ApiKey](#apikey-configuration-setting)|Отслеживает данные для выставления счетов.|
|нет|[ApplicationInsights](#applicationinsights-setting)|Позволяет добавить в контейнер поддержку телеметрии [Azure Application Insights](/azure/application-insights).|
|Да|[Выставление счетов](#billing-configuration-setting)|Задает URI конечной точки для ресурса службы в Azure.|
|Да|[Лицензионное соглашение](#eula-setting)| Указывает, что вы приняли условия лицензии для контейнера.|
|нет|[Fluentd](#fluentd-settings)|Записывает данные в журнал и при необходимости передает метрики на сервер Fluentd.|
|нет|Прокси-сервер HTTP|Настраивает прокси-сервер HTTP для исходящих запросов.|
|нет|[Ведение журнала](#logging-settings)|Обеспечивает поддержку ведения журнала ASP.NET Core для вашего контейнера. |
|нет|[Подключения](#mount-settings)|Считывает и записывает данные с главного компьютера в контейнер и обратно.|