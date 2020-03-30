---
title: Azure Application Insights | Документация Майкрософт
description: Соотворайте данные из Application Insights в другие наборы данных, такие как таблицы обогащения или поиска данных, неприкладные исследования и пользовательские данные.
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 121e4699bd6a72f6865d3a6ffdef58c1b3806047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082767"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Корреляция данных Application Insights с пользовательскими источниками данных

Application Insights собирает несколько разных типов данных: исключения, трассировки, просмотр страниц и другие. Хотя этого часто бывает достаточно для изучения производительности, надежности и использования приложения, бывают случаи, когда полезно соотнести данные, хранящиеся в Application Insights, с другими полностью пользовательскими наборами данных.

Ситуации, когда можно использовать настраиваемые данные.

- Таблицы обогащения данных или поиска. Например, дополните имя сервера именем владельца сервера и местом расположения задания, в котором оно может быть найдено. 
- Корреляция с источниками данных, не относящихся к Application Insights. Например, сопоставьте данные о покупке в интернет-магазине с информацией из службы выполненных покупок, чтобы определить, насколько точны оценки времени доставки. 
- Полностью настраиваемые данные. Большинство клиентов любят язык запросов и производительность платформы журнала Azure Monitor, которая поддерживает Application Insights, и хотят использовать ее для запроса данных, которые совсем не связаны с Application Insights. Например, для отслеживания производительности солнечных батарей как части интеллектуальной домашней установки, описанной [здесь](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/).

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Корреляция пользовательских данных с данными Application Insights 

Поскольку Application Insights поддерживается мощной платформой журнала Azure Monitor, для приема данных можно использовать Azure Monitor на полную силу. Затем мы будем писать запросы с помощью оператора "присоединиться", который будет соотносить эти пользовательские данные с данными, доступными нам в журналах Azure Monitor. 

## <a name="ingesting-data"></a>Прием данных

В этом разделе мы рассмотрим, как получить данные в журналах Azure Monitor.

Если у вас его еще нет, предоставите новое рабочее пространство Log Analytics, следуя [этим инструкциям](../learn/quick-collect-azurevm.md) и включив шаг «создать рабочее пространство».

Начните отправку данных журнала Azure Monitor. Существует несколько вариантов.

- Для синхронного механизма можно либо напрямую позвонить в [API-идентиматологическому](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) сбору данных, либо использовать наш разъем Logic App — просто ищите «Аналитику журнала Azure» и выбрать опцию «Отправить данные»:

  ![Снимок экрана "Выбор и действие"](./media/custom-data-correlation/01-logic-app-connector.png)  

- Для асинхронного варианта используйте API сборщика данных для сборки конвейера обработки. Подробные сведения можно найти в [этой статье](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api).

## <a name="correlating-data"></a>Корреляция данных

Служба Application Insights основана на платформе журнала Azure Monitor. Поэтому можете использовать [межресурсные соединения](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search), чтобы сопоставить любые данные, которые мы приняли в Azure Monitor, с вашими данными Application Insights.

Например, мы можем глотать наши лабораторные запасы и места в таблицу под названием "LabLocations_CL" в рабочей области Log Analytics под названием "myLA". Если мы хотели просмотреть наши запросы, отслеживаемые в приложении Insights приложение под названием "myAI" и соотнести имена машин, которые служили запросы на места хранимых в ранее упомянутой пользовательской таблице, мы можем запустить следующий запрос от либо анализ приложений, либо контекст Azure Monitor:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Next Steps

- Дополнительные сведения см. по ссылке [​​API сборщика данных](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api).
- Дополнительные сведения о [межресурсном соединении](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).
