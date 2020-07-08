---
title: Мониторинг приложений, выполняющихся в Функциях Azure, с помощью Application Insights (Azure Monitor) | Документация Майкрософт
description: Azure Monitor беспроблемно интегрируется с приложением, запущенным в решении "Функции Azure", и позволяет почти мгновенно отслеживать производительность и выявлять проблемы с приложениями.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: 093448ad0280ada039f1d4e5abd0e83e4be19b03
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85482111"
---
# <a name="monitoring-azure-functions-with-azure-monitor-application-insights"></a>Мониторинг решения "Функции Azure" с помощью Azure Monitor в Application Insights

Решение [Функции Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview) предлагает встроенную интеграцию со службой Azure Application Insights для мониторинга функций. 

Application Insights собирает журналы, данные о производительности и ошибках, а также автоматически обнаруживает аномалии в производительности. Application Insights содержит мощные аналитические средства, которые помогают диагностировать проблемы и анализировать использование функций. Имея в своем распоряжении такой уровень доступа к данным о приложении, вы сможете постоянно повышать его производительность и удобство использования. Вы даже можете применить Application Insights на этапе локальной работы над проектом приложения-функции. 

Все необходимое инструментирование Application Insights встроено в Функции Azure. Вам нужен лишь допустимый ключ инструментирования, чтобы подключить приложение-функцию к ресурсу Application Insights. Этот ключ инструментирования следует поместить в параметры приложения при создании ресурса приложения-функции в Azure. Если у приложения-функции еще нет такого ключа, его можно настроить вручную. Дополнительные сведения о функциях мониторинга в Azure см. [здесь](https://docs.microsoft.com/azure/azure-functions/functions-monitoring?tabs=cmd).

## <a name="distributed-tracing-for-java-applications-on-windows-public-preview"></a>Распределенная трассировка для приложений Java в Windows (общедоступная Предварительная версия)

> [!IMPORTANT]
> В настоящее время эта функция доступна в общедоступной предварительной версии для функций Azure Java в Windows. Распределенная трассировка для функций Java Azure в Linux не поддерживается. Для плана потребления он имеет холодный запуск в течение 8-9 секунд.

Для приложений на языке Java вы можете просматривать более подробные данные о приложениях-функциях, в том числе запросы, зависимости, журналы и метрики. Эти дополнительные данные позволят вам отслеживать и диагностировать сквозные транзакции, а также изучать схему приложения, которая объединяет большое число транзакций в топологическое представление взаимодействий в системе, средней производительности и частоты ошибок.

Комплексная диагностика и схема приложения обеспечивают хорошую видимость каждой транзакции и (или) запроса. Сочетание этих двух функций очень полезно при поиске первопричин проблем с надежностью и узких мест производительности для каждого запроса.

### <a name="how-to-enable-distributed-tracing-for-java-function-apps"></a>Как включить распределенную трассировку для приложений-функций Java?

Перейдите к колонке "Обзор" для приложения-функции, а затем — к разделу конфигурации. В разделе "Параметры приложения" щелкните "+ Новый параметр приложения". Добавьте следующие два параметра приложения с указанными ниже значениями, а затем щелкните "Сохранить" в левом верхнем углу. Итак, все готово!

```
XDT_MicrosoftApplicationInsights_Java -> 1
ApplicationInsightsAgent_EXTENSION_VERSION -> ~2
```

## <a name="next-steps"></a>Next Steps

* Дополнительные инструкции и сведения о мониторинге см. в статье [Мониторинг Функций Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitoring).
* Изучите обзорные сведения [о распределенной трассировке](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing).
* Узнайте, чем [схема приложений](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net) будет полезна для вашего бизнеса.
* Ознакомьтесь с дополнительными сведениями о [запросах и зависимостях для приложений Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent).
* Дополнительные сведения об [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) и [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview).