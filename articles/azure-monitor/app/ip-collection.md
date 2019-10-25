---
title: Коллекция IP-адресов Application Insights Azure | Документация Майкрософт
description: Основные сведения об обработке IP-адресов и географического расположения с помощью Azure Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/11/2019
ms.openlocfilehash: bee3e24739aa560a43960143d1a18c30ec1ce160
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819445"
---
# <a name="geolocation-and-ip-address-handling"></a>Обработка географического расположения и IP-адресов

В этой статье объясняется, как выполняется обработка поиска и IP-адресов географического расположения в Application Insights вместе с изменением поведения по умолчанию.

## <a name="default-behavior"></a>Поведение по умолчанию

По умолчанию IP-адреса временно собираются, но не хранятся в Application Insights. Базовый процесс выглядит следующим образом:

IP-адреса отправляются в Application Insights в составе данных телеметрии. При достижении конечной точки приема в Azure IP-адрес используется для выполнения поиска по географическому положению с помощью [GeoLite2 из максминд](https://dev.maxmind.com/geoip/geoip2/geolite2/). Результаты этого поиска используются для заполнения следующих полей `client_City`, `client_StateOrProvince`, `client_CountryOrRegion`. На этом этапе IP-адрес отклоняется, а `0.0.0.0` записывается в поле `client_IP`.

* Данные телеметрии браузера. мы временно соберем IP-адрес отправителя. IP-адрес вычисляется конечной точкой приема.
* Данные телеметрии сервера. модуль Application Insights временно собирает IP-адрес клиента. Эти сведения не собираются, если задан заголовок `X-Forwarded-For`.

Это поведение предназначено для предотвращения ненужной коллекции персональных данных. По возможности рекомендуется избегать сбора персональных данных. 

## <a name="overriding-default-behavior"></a>Переопределение поведения по умолчанию

Хотя поведение по умолчанию сводится к уменьшению количества персональных данных, мы по-прежнему предоставляем гибкость для сбора и хранения данных IP-адресов. Прежде чем сохранять персональные данные, такие как IP-адреса, настоятельно рекомендуется проверить, что это не нарушает требования соответствия или местные нормы, на которые вы можете столкнуться. Дополнительные сведения об обработке персональных данных в Application Insights см. в [руководстве по личным данным](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt).

## <a name="storing-ip-address-data"></a>Хранение данных IP-адреса

Чтобы включить сбор и хранение IP-адресов, свойству `DisableIpMasking` компонента Application Insights должно быть присвоено значение `true`. Это свойство можно задать либо с помощью шаблонов Azure Resource Manager, либо путем вызова REST API. 

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

### <a name="portal"></a>Microsoft Azure 

Если вам нужно изменить поведение только для одного Application Insights ресурса, проще всего это сделать с помощью портал Azure.  

1. Application Insights **параметры** > ресурсов > **Экспорт шаблона** 

    ![Экспорт шаблона](media/ip-collection/export-template.png)

2. Выбор **развертывания**

    ![Кнопка развертывания, выделенная красным цветом](media/ip-collection/deploy.png)

3. Выберите **изменить шаблон**. (Если шаблон содержит дополнительные свойства или ресурсы, которые не отображаются в этом примере шаблона, продолжайте соблюдать осторожность, чтобы все ресурсы приняли развертывание шаблона как добавочное изменение или обновление.)

    ![Изменение шаблона](media/ip-collection/edit-template.png)

4. Внесите следующие изменения в JSON для ресурса и нажмите кнопку **сохранить**:

    ![Снимок экрана добавляет запятую после "Ибизааиекстенсион" и добавляет новую строку ниже с "Дисаблеипмаскинг": true](media/ip-collection/save.png)

    > [!WARNING]
    > Если возникнет сообщение об ошибке:  **_Группа ресурсов находится в расположении, которое не поддерживается одним или несколькими ресурсами в шаблоне. Выберите другую группу ресурсов._** Временно выберите из раскрывающегося списка другую группу ресурсов, а затем повторно выберите исходную группу ресурсов, чтобы устранить эту ошибку.

5. Установите флажок **я принимаю** > **покупки**. 

    ![Изменение шаблона](media/ip-collection/purchase.png)

    В этом случае не будет приобретено ничего нового, мы просто обновляем конфигурацию существующего ресурса Application Insights.

6. После завершения развертывания будут записаны новые данные телеметрии.

    Если вы снова выберете и измените шаблон, вы увидите только шаблон по умолчанию и не увидите вновь добавленное свойство и связанное с ним значение. Если вы не видите данные IP-адреса и хотите подтвердить, что `"DisableIpMasking": true` установлен. Выполните следующую команду PowerShell: (замените `Fabrikam-dev` именем соответствующего ресурса и группы ресурсов.)
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    В результате будет возвращен список свойств. Одно из свойств должно считывать `DisableIpMasking: true`. Если запустить PowerShell до развертывания нового свойства с Azure Resource Manager, это свойство не будет существовать.

### <a name="rest-api"></a>Rest API

Полезная нагрузка [API-интерфейса](https://docs.microsoft.com/rest/api/azure/) для внесения одних и тех же изменений выглядит следующим образом:

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

Если вам требуется более гибкая альтернатива, чем `DisableIpMasking` для записи всех IP-адресов или их части, можно использовать [инициализатор телеметрии](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) , чтобы скопировать весь IP-адрес или его часть в настраиваемое поле. 

### <a name="aspnet--aspnet-core"></a>ASP.NET/ASP.NET Core

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
> Если вам не удается получить доступ к `ISupportProperties`, проверьте и убедитесь, что вы используете последний стабильный выпуск пакета SDK для Application Insights. `ISupportProperties` предназначены для больших значений кратности, тогда как `GlobalProperties` более подходят для значений низкого уровня кратности, таких как имя региона, имя среды и т. д. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>Включите инициализатор телеметрии для. ASP.NET

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

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>Включение инициализатора телеметрии для ASP.NET Core

Вы можете создать свой инициализатор телеметрии так же, как для ASP.NET Core как ASP.NET, но чтобы включить инициализатор, используйте следующий пример для справки:

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

В отличие от пакетов SDK на стороне сервера, клиентский пакет JavaScript на стороне клиента не вычисляет IP-адрес. По умолчанию вычисление IP-адресов для телеметрии на стороне клиента выполняется в конечной точке приема в Azure при получении данных телеметрии. Это означает, что если вы отправите клиентские данные на прокси-сервер и затем перейдете в конечную точку приема, в вычислении IP-адресов может отображаться IP-адрес прокси-сервера, а не клиент. Если прокси-сервер не используется, это не должно быть проблемой.

Если вы хотите вычислить IP-адрес непосредственно на стороне клиента, необходимо добавить собственную пользовательскую логику для выполнения этого вычисления и использовать результат для установки тега `ai.location.ip`. Если задан параметр `ai.location.ip`, то вычисление IP-адресов не выполняется конечной точкой приема, а предоставленный IP-адрес учитывается и используется для выполнения поиска в географическом виде. В этом случае IP-адрес по-прежнему будет обнулен. 

Чтобы хранить весь IP-адрес, вычисленный на основе пользовательской логики, можно использовать инициализатор телеметрии, который скопирует данные IP-адреса, указанные в `ai.location.ip`, в отдельное настраиваемое поле. Но опять же, в отличие от серверных пакетов SDK, не полагаться на сторонние библиотеки или собственную логику коллекции IP-адресов на стороне клиента, клиентский пакет SDK не будет рассчитывать этот IP-адрес.    


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

### <a name="view-the-results-of-your-telemetry-initializer"></a>Просмотр результатов инициализатора телеметрии

Если затем активировать новый трафик для сайта и подождать примерно 2-5 минут, чтобы убедиться в том, что время его приема истекло, можно выполнить запрос Kusto, чтобы узнать, работает ли коллекция IP-адресов:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Новые собранные IP-адреса должны появиться в столбце `customDimensions_client-ip`. По умолчанию столбец `client-ip` по-прежнему будет иметь все 4 октета либо обнуляться, либо отобразить первые три октета в зависимости от того, как настроена коллекция IP-адресов на уровне компонента. Если вы тестируете локально после реализации инициализатора телеметрии и значение, отображаемое для `customDimensions_client-ip`, имеет `::1` это ожидаемое поведение. `::1` представляет петлевой адрес в IPv6. Он эквивалентен `127.0.01` в IPv4 и является результатом, который будет отображаться при тестировании из localhost.

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о [сборе персональных данных](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt) в Application Insights.

* Узнайте больше о том, как работает [Коллекция IP-адресов](https://apmtips.com/blog/2016/07/05/client-ip-address/) в Application Insights. (Это старая внешняя запись блога, написанная одним из наших инженеров. Он выполняет текущее поведение по умолчанию, когда IP-адрес записывается как `0.0.0.0`, но в нем подробно рассматривается механизм встроенных `ClientIpHeaderTelemetryInitializer`.)