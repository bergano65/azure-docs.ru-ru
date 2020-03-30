---
title: Коллекция IP-адресов Azure Application Insights Документы Майкрософт
description: Понимание того, как IP-адреса и геолокация обрабатываются с помощью Azure Application Insights
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 969061ec89ddd0f13caa675bc324207c6c5d8843
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656523"
---
# <a name="geolocation-and-ip-address-handling"></a>Обработка геолокации и IP-адресов

В этой статье объясняется, как поиск геолокации и обработка IP-адресов происходят в Application Insights вместе с тем, как изменить поведение по умолчанию.

## <a name="default-behavior"></a>Поведение по умолчанию

По умолчанию IP-адреса временно собираются, но не хранятся в Application Insights. Базовый процесс выглядит следующим образом:

IP-адреса отправляются в Application Insights как часть телеметрических данных. При достижении точки приема в Azure IP-адрес используется для выполнения поиска геолокации с помощью [GeoLite2 от MaxMind.](https://dev.maxmind.com/geoip/geoip2/geolite2/) Результаты этого поиска используются для заполнения `client_City`следующих `client_CountryOrRegion`полей , `client_StateOrProvince`. На этом этапе IP-адрес `0.0.0.0` отбрасывается `client_IP` и пишется в поле.

* Телеметрия браузера: Мы временно собираем IP-адрес отправителя. IP-адрес рассчитывается по конечной точке приема.
* Телеметрия сервера: Модуль Application Insights временно собирает IP-адрес клиента. Эти сведения не собираются, если задан заголовок `X-Forwarded-For`.

Такое поведение по замыслу помогает избежать ненужного сбора персональных данных. Когда это возможно, мы рекомендуем избегать сбора персональных данных. 

## <a name="overriding-default-behavior"></a>Переопределение поведения по умолчанию

В то время как поведение по умолчанию заключается в том, чтобы свести к минимуму сбор персональных данных, мы по-прежнему предлагаем гибкость для сбора и хранения данных IP-адресов. Прежде чем выбрать для хранения любых персональных данных, таких как IP-адреса, мы настоятельно рекомендуем проверить, что это не нарушает каких-либо требований соответствия или местных правил, которые вы можете быть предметом. Чтобы узнать больше об обработке персональных данных в Application Insights, обратитесь к [руководству для получения персональных данных.](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt)

## <a name="storing-ip-address-data"></a>Хранение данных IP-адресов

Для того, чтобы обеспечить `DisableIpMasking` сбор и хранение IP, необходимо `true`установить свойство компонента Application Insights. Это свойство можно установить либо с помощью шаблонов Azure Resource Manager, либо по вызову REST API. 

### <a name="azure-resource-manager-template"></a>Шаблон Azure Resource Manager

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>Портал 

Если вам нужно изменить поведение для одного ресурса Application Insights, проще всего сделать это через портал Azure.  

1. Перейти на ресурс Application Insights > **настройки шаблона** > **экспорта** 

    ![Шаблон экспорта](media/ip-collection/export-template.png)

2. Выберите **Развертывание**

    ![Кнопка развертывания выделена красным цветом](media/ip-collection/deploy.png)

3. Выберите **шаблон Edit**. (Если ваш шаблон имеет дополнительные свойства или ресурсы, которые не отображаются в этом шаблоне примера, будьте осторожны, чтобы гарантировать, что все ресурсы будут принимать развертывание шаблона в качестве постепенного изменения/обновления.)

    ![Изменение шаблона](media/ip-collection/edit-template.png)

4. Внести следующие изменения в json для вашего ресурса, а затем нажмите **Сохранить:**

    ![Скриншот добавляет запятую после "IbizaAIExtension" и добавить новую строку ниже с "DisableIpMasking": правда](media/ip-collection/save.png)

    > [!WARNING]
    > Если вы испытываете ошибку, в спомневую: ** _группа ресурсов находится в месте, которое не поддерживается одним или несколько ресурсами в шаблоне. Пожалуйста, выберите другую группу ресурсов._** Временно выберите другую группу ресурсов из выпадения, а затем повторно выберите исходную группу ресурсов для устранения ошибки.

5. Выберите **Я согласен** > **Покупка**. 

    ![Изменение шаблона](media/ip-collection/purchase.png)

    В этом случае ничего нового не покупается, мы просто обновляем конфигурацию существующего ресурса Application Insights.

6. После завершения развертывания будут записаны новые данные телеметрии.

    Если вы снова выберете и отсеиваете шаблон, вы увидите только шаблон по умолчанию и не увидите новое свойство и связанное с ним значение. Если вы не видите данные IP-адреса и хотите подтвердить, что `"DisableIpMasking": true` это установлено. Выполнить следующее название `Fabrikam-dev` PowerShell: (Заменить соответствующее название ресурса и группы ресурсов.)
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    В результате будет возвращен список объектов недвижимости. Один из свойств `DisableIpMasking: true`следует прочитать . Если вы запустите PowerShell до развертывания нового свойства с помощью менеджера ресурсов Azure, свойство не будет существовать.

### <a name="rest-api"></a>Rest API

Полезная нагрузка [Rest API](https://docs.microsoft.com/rest/api/azure/) для внесения тех же модификаций заключается в следующем:

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>Инициализатор телеметрии

Если вам нужна более `DisableIpMasking` гибкая альтернатива, чем записывать все или часть IP-адресов, вы можете использовать [инициализатор телеметрии,](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) чтобы скопировать все или часть IP в пользовательское поле. 

### <a name="aspnet--aspnet-core"></a>ASP.NET / ASP.NET ядро

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> Если вы не `ISupportProperties`можете получить доступ, проверьте и убедитесь, что вы работаете последний стабильный релиз приложения Исследования SDK. `ISupportProperties`предназначены для высоких значений `GlobalProperties` кардинальности, в то время как более подходящие для значений низкой степени кардинальности, таких как название региона, название среды и т.д. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>Включить инициализатор телеметрии для ASP.NET

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>Включить инициализатор телеметрии для ASP.NET Core

Вы можете создать свой инициализатор телеметрии так же, как для ASP.NET Core, как ASP.NET но для включения инициализатора, использовать следующий пример для справки:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```

### <a name="client-side-javascript"></a>Клиентская часть JavaScript

В отличие от SDK на стороне сервера, javascript SDK на стороне клиента не вычисляет IP-адрес. По умолчанию расчет IP-адреса для телеметрии на стороне клиента выполняется в конечном пункте приема в Azure по прибытии телеметрии. Это означает, что если вы отправляли данные на стороне клиента на прокси, а затем пересылали в конечную точку приема, расчет IP-адреса может отображать IP-адрес прокси, а не клиента. Если прокси не используется, это не должно быть проблемой.

Если вы хотите вычислить IP-адрес непосредственно на стороне клиента, вам нужно будет добавить `ai.location.ip` свою собственную пользовательскую логику для выполнения этого расчета и использовать результат для установки тега. При `ai.location.ip` установке расчет IP-адреса не выполняется конечным пунктом приема, а предоставленный IP-адрес соблюдается и используется для выполнения геопоиска. В этом случае IP-адрес по-прежнему будет обнуляться по умолчанию. 

Чтобы сохранить весь IP-адрес, рассчитанный на основе пользовательской логики, можно использовать инициатор телеметрии, который будет копировать данные IP-адреса, предоставленные вами в `ai.location.ip` отдельное пользовательское поле. Но опять же в отличие от сервера стороне SDKs, не полагаясь на библиотеки стороны третьей стороны или свой пользовательский клиент стороне логики IP сбора клиент клиент клиент стороне клиент-стороны SDK не будет вычислять IP для вас.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

### <a name="view-the-results-of-your-telemetry-initializer"></a>Просмотр результатов инициалии телеметрии

Если вы запустите новый трафик на ваш сайт и подождите около 2-5 минут, чтобы убедиться, что у него есть время для попастанивания в нее, вы можете запустить запрос Kusto, чтобы увидеть, работает ли сбор IP-адресов:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

В колонке должны отображаться недавно собранные `customDimensions_client-ip` IP-адреса. Столбец `client-ip` по умолчанию будет по-прежнему иметь все 4 октеты либо обнуляется или отображать только первые три октета в зависимости от того, как вы настроили сбор IP-адресов на уровне компонентов. Если вы тестируете локально после реализации инициализатора `customDimensions_client-ip` `::1` телеметрии и значение, на который вы видите, это ожидаемое поведение. `::1`представляет адрес loopback в IPv6. Это эквивалентно `127.0.01` iPv4 и является результатом, который вы увидите при тестировании от localhost.

## <a name="next-steps"></a>Next Steps

* Подробнее о [сборе персональных данных](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt) читайте в фотообращении.

* Узнайте больше о том, как работает [сбор IP-адресов](https://apmtips.com/blog/2016/07/05/client-ip-address/) в Application Insights. (Это старый внешний блог, написанный одним из наших инженеров. Это предшествует текущему поведению `0.0.0.0`по умолчанию, где IP-адрес записывается `ClientIpHeaderTelemetryInitializer`как, но он идет в большую глубину на механике встроенного дюйма)
