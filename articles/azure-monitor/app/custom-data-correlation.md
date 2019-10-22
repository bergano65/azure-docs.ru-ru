---
title: Azure Application Insights | Документация Майкрософт
description: ''
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 03f802297c2ecaac0c126ed3de6401b40e1f804b
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678161"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Корреляция данных Application Insights с пользовательскими источниками данных

Application Insights собирает несколько разных типов данных: исключения, трассировки, просмотр страниц и другие. Хотя этого достаточно для исследования производительности, надежности и потребления приложения, бывают случаи, когда полезно коррелировать данные, хранящиеся в Application Insights, с другими полностью настраиваемыми наборами данных.

Ситуации, когда можно использовать настраиваемые данные.

- Таблицы обогащения данных или поиска. Например, дополните имя сервера именем владельца сервера и местом расположения задания, в котором оно может быть найдено. 
- Корреляция с источниками данных, не относящихся к Application Insights. Например, сопоставьте данные о покупке в интернет-магазине с информацией из службы выполненных покупок, чтобы определить, насколько точны оценки времени доставки. 
- Полностью настраиваемые данные. Большинство клиентов любят язык запросов и производительность платформы журнала Azure Monitor, которая поддерживает Application Insights, и хотят использовать ее для запроса данных, которые совсем не связаны с Application Insights. Например, для отслеживания производительности солнечных батарей как части интеллектуальной домашней установки, описанной [здесь](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/).

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Корреляция пользовательских данных с данными Application Insights 

Поскольку Application Insights поддерживается мощной платформой журнала Azure Monitor, для приема данных можно использовать Azure Monitor на полную силу. Используя оператор "join", мы напишем запросы, которые будут коррелировать пользовательские данные с данными, доступными в журналах Azure Monitor. 

## <a name="ingesting-data"></a>Прием данных

В этом разделе мы рассмотрим, как получить данные в журналах Azure Monitor.

Подготовьте новую рабочую область Log Analytics, если у вас ее еще нет, следуя [этим инструкциям](../learn/quick-collect-azurevm.md), которые включают шаг "создание рабочей области".

Начните отправку данных журнала Azure Monitor. Существует несколько вариантов.

- Для синхронного механизма можно напрямую вызвать [API сборщика данных](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) или использовать соединитель Logic App, для этого просто найдите "Azure Log Analytics" и выберите вариант "Отправить данные".

  ![Снимок экрана "Выбор и действие"](./media/custom-data-correlation/01-logic-app-connector.png)  

- Для асинхронного варианта используйте API сборщика данных для сборки конвейера обработки. Подробные сведения можно найти в [этой статье](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api).

## <a name="correlating-data"></a>Корреляция данных

Служба Application Insights основана на платформе журнала Azure Monitor. Поэтому можете использовать [межресурсные соединения](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search), чтобы сопоставить любые данные, которые мы приняли в Azure Monitor, с вашими данными Application Insights.

Например, можно принимать реестр заданий и местоположения в таблице под названием LabLocations_CL в рабочей области Log Analytics под названием myLA. Чтобы просмотреть запросы, отслеживаемые в приложении Application Insights, называемом myAI, и сопоставить имена машин, которые обслуживали запросы в местах расположения этих машин, хранящиеся в ранее упомянутой пользовательской таблице, нужно запустить следующий запрос из Application Insights или контекста Azure Monitor.

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения см. по ссылке [​​API сборщика данных](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api).
- Дополнительные сведения о [межресурсном соединении](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).
