---
title: Мониторинг использования и производительности классических приложений для Windows
description: Анализ использования и производительности классического приложения для Windows с помощью Application Insights.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 19040746-3315-47e7-8c60-4b3000d2ddc4
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: mbullwin
ms.openlocfilehash: 567163a5d5ce37eeffb5ef2bc6f9adb7c5b027ec
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66255723"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Мониторинг использования и производительности в классических приложениях для Windows

Все приложения, размещенные на локальном компьютере, в Azure и в других облаках могут воспользоваться преимуществами Application Insights. Все, что нужно — это [разрешить обмен данными](../../azure-monitor/app/ip-addresses.md) со службой Application Insights. Для мониторинга приложений универсальной платформы Windows (UWP) мы рекомендуем использовать [Центр приложений Visual Studio](../../azure-monitor/learn/mobile-center-quickstart.md).

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Отправка данных телеметрии в Application Insights из классического приложения для Windows
1. На [портале Azure](https://portal.azure.com) [создайте ресурс Application Insights](../../azure-monitor/app/create-new-resource.md ). Для параметра типа приложения выберите приложение ASP.NET.
2. Сделайте копию ключа инструментирования. Найдите ключ в раскрывающемся списке "Основные компоненты" нового ресурса, который вы только что создали. 
3. В Visual Studio измените пакеты NuGet вашего проекта приложения и добавьте Microsoft.ApplicationInsights.WindowsServer. (Выберите Microsoft.ApplicationInsights, если нужен чистый API без модулей сбора стандартной телеметрии.)
4. Задайте ключ инструментирования в коде.
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *ваш ключ* `";`
   
    Можно также задать его в файле ApplicationInsights.config (если установлен один из пакетов стандартной телеметрии).
   
    `<InstrumentationKey>`*ваш ключ*`</InstrumentationKey>` 
   
    Если используется файл ApplicationInsights.config, убедитесь, что его свойства в обозревателе решений имеют следующие значения: **"Действие сборки = содержимое", "Копировать в выходной каталог = копировать"** .
5. [Используйте API](../../azure-monitor/app/api-custom-events-metrics.md) для отправки данных телеметрии.
6. Запустите приложение и просмотреть данные телеметрии в ресурс, который вы создали на портале Azure.

## <a name="telemetry"></a>Пример кода
```csharp

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>Дальнейшие действия
* [Создание панели мониторинга](../../azure-monitor/app/overview-dashboard.md)
* [Поиск по журналу диагностики](../../azure-monitor/app/diagnostic-search.md)
* [Изучение метрик](../../azure-monitor/app/metrics-explorer.md)
* [Написание запросов аналитики](../../azure-monitor/app/analytics.md)

