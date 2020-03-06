---
title: Мониторинг и управление Azure для государственных организаций | Документация Майкрософт
description: Эта статья содержит сравнительный анализ функциональных возможностей и рекомендации по разработке приложений, которые будут использоваться в Azure для государственных организаций.
services: azure-government
cloud: gov
author: gsacavdm
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.topic: article
ms.workload: azure-government
ms.date: 12/11/2019
ms.author: gsacavdm
ms.openlocfilehash: b6f395964c286aca1bc8a1c190edeea00ba6e15c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362263"
---
# <a name="azure-government-monitoring--management"></a>Мониторинг и управление Azure для государственных организаций
В этой статье описаны варианты служб мониторинга и управления, а также представлены рекомендации для среды Azure для государственных организаций.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="advisor"></a>Помощник
Помощник является общедоступным в Azure для государственных организаций.

Дополнительные сведения см. в разделе [общедоступная документация по Advisor](../advisor/advisor-overview.md).

### <a name="variations"></a>Варианты
Следующие рекомендации Advisor сейчас недоступны в Azure для государственных организаций:

* Высокий уровень доступности
  * Настройка VPN-шлюза в значение "активный — активный" для устойчивости подключений
  * Создание оповещений о Работоспособности служб Azure, чтобы узнавать о возникновении проблем в Azure
  * Настройка конечных точек диспетчера трафика для обеспечения устойчивости
  * Использование обратимого удаления для учетной записи хранения Azure
* Производительность
  * Повышение надежности и производительности службы приложений
  * Сокращение срока жизни DNS в профиле диспетчера трафика Microsoft Azure, чтобы ускорить отработку отказа на работоспособные конечные точки
  * Повышение производительности хранилища данных SQL
  * Использование хранилища класса Premium
  * Перенос учетной записи хранения в Azure Resource Manager
* Стоимость
  * Приобретение зарезервированных экземпляров виртуальных машин для экономии денег с оплатой по мере использования
  * Удалить неподготавливающие каналы ExpressRoute
  * Удалите или перенастройте неактивные шлюзы виртуальной сети

Вычисление, используемое для того, чтобы в Azure для государственных организаций выбрать правильный размер или завершить работу недостаточных виртуальных машин, выглядит следующим образом:

Advisor отслеживает использование виртуальной машины в течение 7 дней и определяет виртуальные машины с низким уровнем использования. Виртуальные машины считаются низкими показателями использования, если загрузка ЦП составляет 5% или меньше, а их использование сети меньше 2% или если текущая рабочая нагрузка может быть рассчитана меньшим размером виртуальной машины. Если требуется более интенсивное определение неиспользуемых виртуальных машин, можно настроить правило использования ЦП для каждой подписки.

## <a name="automation"></a>Служба автоматизации
В Azure для государственных организаций используется общедоступная версия службы автоматизации.

Дополнительные сведения см. в [общедоступной документации по службе автоматизации](../automation/automation-intro.md).

## <a name="azure-migrate"></a>Служба "Миграция Azure"

Служба "миграция Azure" обычно доступна в Azure для государственных организаций.

Дополнительные сведения см. в [документации по службе "миграция Azure](../migrate/migrate-overview.md)".

### <a name="variations"></a>Варианты
Следующие функции миграции Azure в настоящее время недоступны в Azure для государственных организаций:

* Функция визуализации зависимостей недоступна в Azure для государственных организаций, так как служба "миграция Azure" зависит от Сопоставление служб для визуализации зависимостей, которая сейчас недоступна в Azure для государственных организаций.
* Оценки для Azure для государственных организаций можно создавать только в качестве целевых регионов и с помощью предложений Azure для государственных организаций.

## <a name="backup"></a>Резервное копирование
В Azure для государственных организаций используется общедоступная версия службы архивации.

Дополнительные сведения см. в статье [Служба архивации в Azure для государственных организаций](documentation-government-services-backup.md).

## <a name="policy"></a>Политика
Политика общедоступна в Azure для государственных организаций.

Дополнительные сведения см. в статье [Что такое служба "Политика Azure"?](../governance/policy/overview.md)

## <a name="site-recovery"></a>Site Recovery
В Azure для государственных организаций используется общедоступная версия Azure Site Recovery.

Дополнительные сведения см. в статье [Site Recovery коммерческая документация](../site-recovery/site-recovery-overview.md).

### <a name="variations"></a>Варианты
Приведенные ниже возможности Site Recovery сейчас недоступны в Azure для государственных организаций:
* Уведомление по электронной почте

| Site Recovery | Классический | Resource Manager |
| --- | --- | --- |
| VMware или физический  | GA | GA |
| Hyper-V | GA | GA |
| "Сеть — сеть" | GA | GA |

Приведенные ниже URL-адреса для Site Recovery отличаются в Azure для государственных организаций.

| Azure Public | Azure для государственных организаций | Примечания |
| --- | --- | --- |
| \*.hypervrecoverymanager.windowsazure.com | \*.hypervrecoverymanager.windowsazure.us | Доступ к службе Site Recovery. |
| \*.backup.windowsazure.com  | \*.backup.windowsazure.us | Доступ к Protection Service. |
| \*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net | Для хранения моментальных снимков виртуальных машин. |
| https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | Для скачивания MySQL. |

## <a name="monitor"></a>Монитор
Azure Monitor общедоступна в Azure для государственных организаций.

Дополнительные сведения см. в статье [мониторинг коммерческой документации](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview).

### <a name="variations"></a>Варианты
В следующих разделах подробно описаны различия и способы решения Azure Monitor в Azure для государственных организаций:

#### <a name="action-groups"></a>Группы действий
Группы действий общедоступны в Azure для государственных организаций и не отличаются от коммерческих Azure.   

#### <a name="activity-log-alerts"></a>Оповещения журнала действий
Оповещения журнала действий общедоступны в Azure для государственных организаций и не отличаются от коммерческих Azure.

#### <a name="alerts-experience"></a>Интерфейс оповещений
ИНТЕРФЕЙС Объединенных оповещений доступен для оповещений о метриках и журналов в Azure для государственных организаций.

#### <a name="autoscale"></a>Автомасштабирование
Автомасштабирование общедоступно в Azure для государственных организаций.

Если вы используете вызовы PowerShell/ARM/RESTFUL для указания параметров, задайте для параметра "расположение" автомасштабирования значение "государственных организаций США Виргиния" или "государственных организаций США Айова". Ресурс, целевой для автомасштабирования, может существовать в любом регионе. Ниже приведен пример параметра.

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "1"
$rule2 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "2"
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1, $rule2 -Name "MyProfile"
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri https://example.com?mytoken=mytokenvalue
$notification1= New-AzAutoscaleNotification -CustomEmails myname@company.com -SendEmailToSubscriptionAdministrator -SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
Add-AzAutoscaleSetting -Location "USGov Virginia" -Name "MyScaleVMSSSetting" -ResourceGroup sdubeys-usgv -TargetResourceId /subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Web/serverFarms/ServerFarm1 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Если вы заинтересованы в реализации автомасштабирования ресурсов, используйте вызовы PowerShell/ARM/RESTful, чтобы указать параметры.

Дополнительные сведения об использовании PowerShell см. в [общедоступной документации](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples#create-and-manage-autoscale-settings).

#### <a name="metrics"></a>Метрики
Метрики общедоступны в Azure для государственных организаций. Тем не менее многомерные метрики поддерживаются только с помощью REST API. Возможность [отображения многомерных метрик](../azure-monitor/platform/metrics-charts.md) доступна в предварительной версии на портале Azure для государственных организаций.

#### <a name="metric-alerts"></a>Оповещения о метриках
Первое поколение оповещений о метриках обычно доступно как в Azure для государственных организаций, так и в коммерческой среде Azure. Первое поколение называется *Alerts (классическая модель)* . Второе поколение оповещений метрик (также называемое [интерфейсом унифицированных предупреждений](../azure-monitor/platform/alerts-overview.md)) теперь также доступно, но с меньшим набором поставщиков ресурсов, [чем в общедоступном облаке](../azure-monitor/platform/alerts-metric-near-real-time.md). С течением времени будут добавляться дополнительные сведения. 

Ниже перечислены ресурсы, которые в настоящее время поддерживаются в предупреждениях второго поколения.
- Microsoft.ApiManagement/service
- Microsoft.Cache/redis
- Microsoft.Compute/virtualMachines
- Microsoft.DBforMySQL/servers
- Microsoft.DBforPostgreSQL/servers
- Microsoft.DBforMariaDB/servers
- Microsoft.Devices/IotHubs
- Microsoft. EventGrid/Domains
- Microsoft.EventGrid/topics
- Microsoft.EventHub/clusters
- Microsoft.EventHub/namespaces
- Microsoft. Insights/компоненты
- Microsoft.Network/dnsZones
- Microsoft.Network/loadBalancers
- Microsoft. Network/Натгатевайс
- Microsoft. Network/Приватиндпоинтс
- Microsoft. Network/Привателинксервицес
- Microsoft.Network/trafficManagerProfiles
- Microsoft.OperationalInsights/workspaces
- Microsoft.PowerBIDedicated/capacities
- Microsoft.Relay/namespaces
- Microsoft.ServiceBus/namespaces
- Microsoft.Sql/managedInstances
- Microsoft.Sql/servers/databases
- Microsoft.Sql/servers/elasticPools
- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft.Storage/storageAccounts/fileServices
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices
- Microsoft.Web/hostingEnvironments/multiRolePools
- Microsoft.Web/hostingEnvironments/workerPools
- Microsoft.Web/serverfarms
- Microsoft.Web/sites
- Microsoft.Web/sites/slots

Вы по-прежнему можете использовать [классические оповещения](../azure-monitor/platform/alerts-classic.overview.md) для ресурсов, которые пока не доступны во втором поколении оповещений. 

При использовании вызовов PowerShell/ARM/RESTful для создания оповещений метрик необходимо задать для параметра "расположение" оповещения метрики значение "государственных организаций США Виргиния" или "государственных организаций США Айова". Ниже приведен пример параметра.

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "USGov Virginia" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Дополнительные сведения об использовании PowerShell см. в [общедоступной документации](../azure-monitor/platform/powershell-quickstart-samples.md).

## <a name="application-insights"></a>Application Insights

> [!NOTE]
> Мониторинг на основе модуля и агента без кода для служб приложений Azure **сейчас не поддерживается**. Как только эта функция станет доступна, эта статья будет обновлена.

В этом разделе описывается дополнительная конфигурация, необходимая для использования Application Insights в Azure для государственных организаций. Дополнительные сведения о Azure Monitor и Application Insights извлечения [полной документации](https://docs.microsoft.com/azure/azure-monitor/overview).

### <a name="enable-application-insights-for-aspnet--aspnet-core-with-visual-studio"></a>Включение Application Insights для ASP.NET Core & ASP.NET с помощью Visual Studio

В настоящее время для клиентов Azure для государственных организаций единственным способом включения Application Insights с помощью традиционной кнопки **телеметрии "добавить Application Insights** " в Visual Studio требуется небольшое ручное решение. Клиенты, имеющие связанную с проблемой неполадку, могут увидеть сообщения об ошибках, например _"нет подписки Azure, связанной с этой учетной записью_ _", или "выбранная подписка не поддерживает Application Insights_ , хотя поставщик ресурсов `microsoft.insights` имеет состояние" зарегистрировано "для подписки. Чтобы устранить эту ошибку, необходимо выполнить следующие действия.

1. Перейдите в Visual Studio, чтобы [Выбрать целевое облако Azure для государственных организаций](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-vs).

2. Создайте (или если уже существующий набор) переменную среды пользователя для Азуреграфапиверсион следующим образом: (чтобы создать переменную среды пользователя, перейдите в **Панель управления** > **системная** > **Дополнительные параметры системы** > **Дополнительные** **переменные среды** > .)

    `Variable name: AzureGraphApiVersion` `Variable value: 2014-04-01`

3. Внесите соответствующие изменения в конечную точку Application Insights пакета SDK для [ASP.NET](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#net-with-applicationinsightsconfig) или [ASP.NET Core](#aspnet-core) в зависимости от типа проекта.

### <a name="snapshot-debugger"></a>Snapshot Debugger

Snapshot Debugger теперь доступны для клиентов Azure для государственных организаций. Чтобы использовать Snapshot Debugger единственным дополнительным компонентом, необходимо убедиться, что используется [snapshot Collector версии 1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5-pre-1906.403) или более поздней. Затем просто следуйте стандартной [документации по snapshot Debugger](https://docs.microsoft.com/azure/azure-monitor/app/snapshot-debugger).

### <a name="sdk-endpoint-modifications"></a>Изменения конечной точки пакета SDK

Чтобы отправить данные из Application Insights в регион Azure для государственных организаций, необходимо изменить адреса конечных точек по умолчанию, используемые пакетами SDK для Application Insights. Каждый пакет SDK требует немного других изменений.

### <a name="net-with-applicationinsightsconfig"></a>.NET с applicationinsights. config

> [!NOTE]
> Файл applicationinsights. config автоматически перезаписывается в любое время, когда выполняется обновление пакета SDK. После обновления пакета SDK необходимо повторно ввести значения конечной точки, относящиеся к региону.

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://quickpulse.applicationinsights.us/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.applicationinsights.us/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

### <a name="aspnet-core"></a>ASP.NET Core

Измените файл appSettings. JSON в проекте следующим образом, чтобы настроить основную конечную точку.

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "https://dc.applicationinsights.us/v2/track"
    }
  }
```

Значения динамических метрик и конечной точки запроса профиля можно задать только с помощью кода. Чтобы переопределить значения по умолчанию для всех значений конечных точек с помощью кода, внесите следующие изменения в метод `ConfigureServices` файла `Startup.cs`:

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel; //place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="https://quickpulse.applicationinsights.us/QuickPulseService.svc");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });

    //place in ConfigureServices method. If present, place this prior to   services.AddApplicationInsightsTelemetry("instrumentation key");
```

### <a name="azure-functions"></a>Функции Azure

Установите следующие пакеты в проект функции:

- Microsoft. ApplicationInsights версии 2.10.0
- Microsoft. ApplicationInsights. Перфкаунтерколлектор версия 2.10.0
- Microsoft. ApplicationInsights. WindowsServer. Телеметричаннел версии 2.10.0

Кроме того, добавьте (или измените) код запуска для приложения-функции:

```csharp
[assembly: FunctionsStartup(typeof(Example.Startup))]
namespace Example
{
  class Startup : FunctionsStartup
  {
      public override void Configure(IFunctionsHostBuilder builder)
      {
          var quickPulseFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryModule) && 
                                               sd.ImplementationType == typeof(QuickPulseTelemetryModule));
          if (quickPulseFactory != null)
          {
              builder.Services.Remove(quickPulseFactory);
          }

          var appIdFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(IApplicationIdProvider));
          if (appIdFactory != null)
          {
              builder.Services.Remove(appIdFactory);
          }

          var channelFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryChannel));
          if (channelFactory != null)
          {
              builder.Services.Remove(channelFactory);
          }

          builder.Services.AddSingleton<ITelemetryModule, QuickPulseTelemetryModule>(_ =>
              new QuickPulseTelemetryModule
              {
                  QuickPulseServiceEndpoint = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
              });

          builder.Services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

          builder.Services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });
      }
  }
}
```

### <a name="java"></a>Java

Измените файл applicationinsights. XML, чтобы изменить адрес конечной точки по умолчанию.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

Измените файл `application.properties` и добавьте:

```yaml
azure.application-insights.channel.in-process.endpoint-address= https://dc.applicationinsights.us/v2/track
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "https://dc.applicationinsights.us/v2/track"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"; //live metrics
appInsights.Configuration.start();
```

Конечные точки можно также настроить с помощью переменных среды:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "https://dc.applicationinsights.us/api/profiles/{0}/appId"
Live Metrics Endpoint: "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
```

### <a name="javascript"></a>JavaScript

```javascript
<script type="text/javascript">
   var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){
      function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/next/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t
   }({
      instrumentationKey:"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
      endpointUrl: "https://dc.applicationinsights.us/v2/track"
   });

   window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="firewall-exceptions"></a>Исключения брандмауэра

Служба Application Insights Azure использует несколько IP-адресов. Вам могут понадобиться эти адреса, если отслеживаемое приложение расположено за брандмауэром.

> [!NOTE]
> Эти адреса статические, но время от времени мы можем их менять. Весь Application Insights трафик представляет исходящий трафик за исключением наблюдения за доступностью и веб-перехватчиков, для которых требуются правила брандмауэра для входящего трафика.

### <a name="outgoing-ports"></a>Порты для исходящего трафика
Чтобы разрешить пакету SDK службы Application Insights и/или монитору состояния отправлять данные на портал, в брандмауэре сервера нужно открыть несколько портов для исходящего трафика.

| Назначение | URL-адрес | IP-адрес | порты; |
| --- | --- | --- | --- |
| Телеметрия | dc.applicationinsights.us | 23.97.4.113 | 443 |

## <a name="azure-monitor-logs"></a>Журналы Azure Monitor
Журналы Azure Monitor общедоступны в Azure для государственных организаций.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="variations"></a>Варианты

* Решения, доступные в Azure для государственных организаций, включают:
  * [Монитор производительности сети (NPM)](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/) — NPM — это облачное решение для мониторинга сети в общедоступных и гибридных облачных средах. Организации используют NPM для отслеживания доступности сети в локальных и облачных средах.  Монитор конечных точек — это подфункция NPM, которая наблюдает за сетевым подключением к приложениям.

Следующие Azure Monitor журналы в настоящее время недоступны в Azure для государственных организаций.

* Решения, которые находятся на этапе предварительной версии в Microsoft Azure, включая следующие:
  * Сопоставление служб
  * Решение для анализа обновлений Windows 10.
  * решение Application Insights;
  * Решение для анализа групп безопасности сети Azure
  * решение для анализа службы автоматизации Azure;
  * решение для анализа хранилища ключей.
* Решения и функции, требующие обновления локального программного обеспечения, включая следующие:
  * решение Surface Hub.
* Возможности, доступные в предварительной версии в глобальной Azure, включая:
  * экспорт данных в Power BI.
* Метрики и система диагностики Azure.

URL-адреса журналов Azure Monitor различаются в Azure для государственных организаций:

| Azure Public | Azure для государственных организаций | Примечания |
| --- | --- | --- |
| mms.microsoft.com |oms.microsoft.us |Портал рабочих областей Log Analytics |
| *workspaceId*.ods.opinsights.azure.com |*workspaceId*.ods.opinsights.azure.us |[API сборщика данных](../azure-monitor/platform/data-collector-api.md) |
| \*.ods.opinsights.azure.com |\*.ods.opinsights.azure.us |Обмен данными между агентами: [настройка параметров брандмауэра](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.oms.opinsights.azure.com |\*.oms.opinsights.azure.us |Обмен данными между агентами: [настройка параметров брандмауэра](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.blob.core.windows.net |\*.blob.core.usgovcloudapi.net |Обмен данными между агентами: [настройка параметров брандмауэра](../log-analytics/log-analytics-proxy-firewall.md) |
| portal.loganalytics.io |portal.loganalytics.us |Портал расширенной аналитики — [Настройка параметров брандмауэра](../azure-monitor/log-query/portals.md) |
| api.loganalytics.io |api.loganalytics.us |Портал расширенной аналитики — [Настройка параметров брандмауэра](../azure-monitor/log-query/portals.md) |
| docs.loganalytics.io |docs.loganalytics.us |Портал расширенной аналитики — [Настройка параметров брандмауэра](../azure-monitor/log-query/portals.md) |
| \*.azure-automation.net |\*. azure-automation.us |Служба автоматизации Azure. [Настройка параметров брандмауэра](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) |
| Недоступно | *. usgovtrafficmanager.net | Диспетчер трафика Azure — [Настройка параметров брандмауэра](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) |

Следующие Azure Monitor журналы ведут себя иначе в Azure для государственных организаций:

* Чтобы подключить группу управления System Center Operations Manager к Azure Monitor журналов, необходимо скачать и импортировать обновленные пакеты управления.
  + System Center Operations Manager 2016:
    1. Установите [накопительный пакет обновления 2 для System Center Operations Manager 2016](https://support.microsoft.com/help/3209591).
    2. Импортируйте пакеты управления, входящие в состав накопительного пакета обновления 2, в Operations Manager. Сведения о том, как импортировать пакет управления с диска, см. в статье [How to Import an Operations Manager Management Pack](https://technet.microsoft.com/library/hh212691.aspx) (Импорт пакета управления Operations Manager).
    3. Чтобы подключить Operations Manager к Azure Monitor журналам, выполните действия, описанные в статье [подключение Operations Manager к Azure Monitor журналам](../azure-monitor/platform/om-agents.md).
  + System Center Operations Manager 2012 R2 UR3 (или более поздней версии) или System Center Operations Manager 2012 SP1 UR7 (или более поздней версии):
    1. Скачайте и сохраните [обновленные пакеты управления](https://go.microsoft.com/fwlink/?LinkId=828749).
    2. Распакуйте скачанный файл.
    3. Импортируйте пакеты управления в Operations Manager. Сведения о том, как импортировать пакет управления с диска, см. в статье [How to Import an Operations Manager Management Pack](https://technet.microsoft.com/library/hh212691.aspx) (Импорт пакета управления Operations Manager).
    4. Чтобы подключить Operations Manager к Azure Monitor журналам, выполните действия, описанные в статье [подключение Operations Manager к Azure Monitor журналам](../azure-monitor/platform/om-agents.md).

* Дополнительные сведения об использовании групп компьютеров из Configuration Manager см. [в статье подключение Configuration Manager к Azure Monitor](../azure-monitor/platform/collect-sccm.md).

### <a name="frequently-asked-questions"></a>Часто задаваемые вопросы
* Можно ли перенести данные из журналов Azure Monitor в Microsoft Azure в Azure для государственных организаций?
  * Нет. Невозможно переместить данные или рабочую область из Microsoft Azure в Azure для государственных организаций.
* Можно ли переключаться между рабочими областями Microsoft Azure и Azure для государственных организаций с портала Operations Management Suite?
  * Нет. Для Microsoft Azure и Azure для государственных организаций используются отдельные порталы, не предусматривающие взаимный обмен данными.

Дополнительные сведения см. в разделе [Azure Monitor журналов общедоступная документация](../log-analytics/log-analytics-overview.md).

## <a name="scheduler"></a>Планировщик
Сведения об этой службе и ее использовании см. в [документации по планировщику Azure](../scheduler/index.md).

## <a name="azure-portal"></a>Портал Azure
Доступ к порталу Azure для государственных организаций можно получить [здесь](https://portal.azure.us).

## <a name="azure-resource-manager"></a>Azure Resource Manager
Сведения об этой службе и ее использовании см. в [документации по Azure Resource Manager](../azure-resource-manager/management/overview.md).

## <a name="next-steps"></a>Дальнейшие действия
* Подписка на [блог по Azure для государственных организаций](https://blogs.msdn.microsoft.com/azuregov/)
* Получение справки по Stack Overflow с помощью [Azure-gov](https://stackoverflow.com/questions/tagged/azure-gov)
* Отправьте отзыв или запросите новые функции с помощью [форума обратной связи Azure для государственных организаций](https://feedback.azure.com/forums/558487-azure-government)
