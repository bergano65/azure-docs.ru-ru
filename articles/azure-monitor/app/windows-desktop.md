---
title: Мониторинг использования и производительности классических приложений для Windows
description: Анализ использования и производительности классического приложения для Windows с помощью Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/29/2019
ms.openlocfilehash: a9dfc32a0f33db5639d5f74667a90a248dc358a1
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73052457"
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
6. Запустите приложение и просмотрите данные телеметрии в ресурсе, созданном в портал Azure.

## <a name="telemetry"></a>Пример кода
```csharp
using Microsoft.ApplicationInsights;

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
        {
            e.Cancel = true;

            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="override-storage-of-computer-name"></a>Переопределить хранилище имени компьютера

По умолчанию этот пакет SDK будет выполнять сбор и хранение имени компьютера, порожденного системой телеметрии. Чтобы переопределить коллекцию, необходимо использовать инициализатор телеметрии:

**Напишите пользовательский TelemetryInitializer, как показано ниже.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here, you can pass an empty string if needed.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```
Создайте экземпляр инициализатора в методе `Program.cs` `Main()` ниже, указав ключ инструментирования:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
        }
```

## <a name="next-steps"></a>Дальнейшие действия
* [Создание панели мониторинга](../../azure-monitor/app/overview-dashboard.md)
* [Поиск по журналу диагностики](../../azure-monitor/app/diagnostic-search.md)
* [Изучение метрик](../../azure-monitor/app/metrics-explorer.md)
* [Написание запросов аналитики](../../azure-monitor/app/analytics.md)

