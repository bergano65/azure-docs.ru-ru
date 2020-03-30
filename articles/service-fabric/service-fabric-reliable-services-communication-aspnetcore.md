---
title: Сервисное сообщение с ASP.NET Ядром
description: Узнайте, как использовать ASP.NET Core в приложениях для защиты надежных служб служб Azure, не в состоянии состояния и статистом Службы безопасности.
author: vturecek
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 0d432bd19d0689ef508fca0bf24eed4406929f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639638"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET ядро в надежных сервисах Azure

ASP.NET Core является открытым исходным кодом и кросс-платформенной платформой. Эта платформа предназначена для создания облачных приложений, подключенных к Интернету, таких как веб-приложения, IoT-приложения и мобильные задние концы.

Эта статья представляет собой углубленное руководство по хостингу ASP.NET основных услуг в сервисных услугах Fabric Reliable С помощью **Microsoft.ServiceFabric.AspNetCore.** набор пакетов NuGet.

Для вводного учебника по ASP.NET Core in Service Fabric и инструкций по настройке среды разработки [ASP.NET](service-fabric-tutorial-create-dotnet-app.md)см.

Остальная часть этой статьи предполагает, что вы уже знакомы с ASP.NET Core. Если нет, пожалуйста, прочитайте [основы ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core в среде Service Fabric

Приложения Core и Service Fabric ASP.NET-сервисные приложения могут работать на основе .NET Core или полной рамочной системы .NET. Вы можете использовать ASP.NET Core двумя различными способами в Service Fabric:
 - **Разместить в виде гостевого исполняемого файла**. Этот способ в первую очередь используется для запуска существующих ASP.NET основных приложений на Service Fabric без каких-либо изменений кода.
 - **Запуск внутри надежного сервиса**. Таким образом, позволяет лучше интегрироваться с службой Fabric время выполнения и позволяет состояние ASP.NET основных услуг.

В остальной части этой статьи объясняется, как использовать ASP.NET Core внутри надежной службы, через ASP.NET компоненты интеграции Core, которые помещают с Сервисом Ткань SDK.

## <a name="service-fabric-service-hosting"></a>Размещение службы Service Fabric

В Service Fabric один или несколько экземпляров и/или реплики службы работают в *процессе узла службы:* исполняемый файл, который запускает ваш сервисный код. Вы, как автор услуг, владеете процессом размещения услуг, и Service Fabric активирует и контролирует его для вас.

Обычно ASP.NET (до MVC 5) тесно связан с IIS через System.Web.dll. ASP.NET Core разделяет веб-сервер и веб-приложение. Это разделение позволяет веб-приложений быть портативными между различными веб-серверов. Он также позволяет веб-серверов для *самостоятельного хостинга*. Это означает, что вы можете запустить веб-сервер в своем собственном процессе, в отличие от процесса, который принадлежит специальному программному обеспечению веб-сервера, например IIS.

Чтобы объединить сервис Service Fabric и ASP.NET, как в качестве гостя, так и надежного, вы должны быть в состоянии начать ASP.NET внутри процесса вашего поставщика услуг. Автономное размещение ASP.NET Core позволяет это сделать.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Хостинг ASP.NET Core в надежном сервисе
Как правило, автономно размещаемые приложения ASP.NET Core создают WebHost в точке входа приложения, например метод `static void Main()` в `Program.cs`. В этом случае жизненный цикл WebHost связан с жизненным циклом процесса.

![Размещение ASP.NET Core в процессе][0]

Но точка входа приложения не является правильным местом для создания WebHost в надежном сервисе. Это связано с тем, что точка входа приложения используется только для регистрации типа службы с помощью времени выполнения Service Fabric, чтобы можно было создавать экземпляры этого типа службы. WebHost должен быть создан в надежной службе себя. В процессе узла службы экземпляры и/или реплики могут проходить через несколько жизненных циклов. 

Экземпляр Reliable Service представлен с помощью класса службы, производного от `StatelessService` или `StatefulService`. Стек связи для службы содержится в реализации `ICommunicationListener` класса службы. Пакеты `Microsoft.ServiceFabric.AspNetCore.*` NuGet содержат реализации `ICommunicationListener` этого запуска и управления ASP.NET Core WebHost для надежного сервиса Kestrel или HTTP.sys.

![Диаграмма для хостинга ASP.NET Core в надежном сервисе][1]

## <a name="aspnet-core-icommunicationlisteners"></a>Объекты ICommunicationListener в ASP.NET Core
Реализации `ICommunicationListener` для Kestrel и HTTP.ss `Microsoft.ServiceFabric.AspNetCore.*` в пакетах NuGet имеют схожие шаблоны использования. Но они выполняют несколько различные действия, характерные для каждого веб-сервера. 

Оба прослушивателя связи предоставляют конструктор, который принимает следующие аргументы:
 - **`ServiceContext serviceContext`**: Это `ServiceContext` объект, содержащий информацию о запущенной службе.
 - **`string endpointName`**: Это название `Endpoint` конфигурации в ServiceManifest.xml. Это в первую очередь, где два слушателя связи отличаются. HTTP.sys *requires* требует `Endpoint` конфигурации, в то время как Kestrel не делает.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: Это lambda, что вы реализуете, в `IWebHost`котором вы создаете и возвращаете . Это позволяет настроить `IWebHost` так, как обычно в ASP.NET приложение Core. Lambda предоставляет URL-адрес, который создан для вас, в зависимости от `Endpoint` вариантов интеграции Service Fabric, которые вы используете, и конфигурации, которую вы предоставляете. Затем можно изменить или использовать этот URL для запуска веб-сервера.

## <a name="service-fabric-integration-middleware"></a>ПО промежуточного уровня для интеграции Service Fabric
Пакет `Microsoft.ServiceFabric.AspNetCore` NuGet включает `UseServiceFabricIntegration` в `IWebHostBuilder` себя метод расширения, который добавляет сервис Fabric-знать промежуточного посуды. Это промежуточное программное обеспечение настраивает `ICommunicationListener` Kestrel или HTTP.sys для регистрации уникального URL-адреса службы обслуживания с службой именования тканей службы. Затем он проверяет запросы клиентов, чтобы убедиться, что клиенты подключаются к нужной службе. 

Этот шаг необходим для предотвращения ошибочного подключения клиентов к неправильному сервису. Это потому, что в среде общего размещения, такой как Service Fabric, несколько веб-приложений могут работать на одной физической или виртуальной машине, но не используют уникальные имена хостеров. Данный сценарий описан более подробно в следующем разделе.

### <a name="a-case-of-mistaken-identity"></a>Причина ошибочный идентификации
Реплики службы, независимо от протокола, прослушивают уникальное сочетание "IP-адрес:порт". После того, как реплика службы начала прослушивать на конечных точках IP:port, она сообщает, что адрес конечных точек службы именования ткани службы. Там, клиенты или другие услуги могут обнаружить его. Если службы используют динамически назначенные порты приложений, реплика службы может случайно использовать ту же конечную точку IP:port другой службы ранее на той же физической или виртуальной машине. Это может привести к ошибочному подключению клиента к неверной службе, Этот сценарий может привести, если следующая последовательность событий происходит:

 1. Служба А прослушивает 10.0.0.1:30000 по протоколу HTTP. 
 2. Клиент решает службу А и получает адрес 10.0.0.1:30000.
 3. Служба А перемещается на другой узел.
 4. Служба Б помещается в 10.0.0.1 и случайно использует тот же порт 30000.
 5. Клиент пытается подключиться к службе А с помощью кэшированного адреса 10.0.0.1:30000.
 6. Клиент теперь успешно подключен к службе B, не понимая, что он подключен к неправильному сервису.

Это может периодически вызывать ошибки, которые трудно диагностировать.

### <a name="using-unique-service-urls"></a>Использование уникальных URL-адресов службы
Чтобы предотвратить эти ошибки, службы могут разместить конечную точку в службе именования с помощью уникального идентификатора, а затем проверить этот уникальный идентификатор во время запросов клиентов. Это совместное действие между службами в доверенной среде дружественного клиента. Он не обеспечивает безопасную аутентификацию службы в среде враждебного арендатора.

В надежной среде промежуточное программное обеспечение, добавленное `UseServiceFabricIntegration` методом, автоматически прикладывает уникальный идентификатор к адресу, размещенному в Службе именования. Он проверяет этот идентификатор по каждому запросу. Если идентификатор не совпадает, посредник немедленно возвращает ответ HTTP 410 Gone.

Службы, использующие динамически назначаемый порт, должны использовать это ПО промежуточного слоя.

Услуги, которые используют фиксированный уникальный порт, не имеют этой проблемы в кооперативной среде. Постоянный уникальный порт обычно используется для внешних служб, которым необходим известный порт для подключения клиентских приложений. Например, большинство веб-приложений, обнажтерных в Интернете, будут использовать порт 80 или 443 для подключения к веб-браузеру. В этом случае уникальный идентификатор не должен быть включен.

На схеме ниже показан поток запроса с включенным ПО промежуточного слоя.

![Интеграция Service Fabric ASP.NET Core][2]

И реализации Kestrel и `ICommunicationListener` HTTP.sys используют этот механизм точно так же. Хотя HTTP.sys может внутренне дифференцировать запросы на основе уникальных путей URL, используя *not* базовую функцию совместного `ICommunicationListener` использования портов **HTTP.sys,** эта функциональность не используется в реализации HTTP.sys. Это потому, что это приводит к HTTP 503 и HTTP 404 коды статуса ошибки в сценарии, описанном ранее. Это, в свою очередь, затрудняет для клиентов определение намерения ошибки, так как HTTP 503 и HTTP 404 обычно используются для обозначения других ошибок. 

Таким образом, как Kestrel, `ICommunicationListener` так и http.sys `UseServiceFabricIntegration` стандартизируют промежуточное программное обеспечение, предоставляемое методом расширения. Таким образом, клиентам нужно только выполнить действие по повторному разрешению на конечную точку службы в ответах HTTP 410.

## <a name="httpsys-in-reliable-services"></a>HTTP.sys в надежных услугах
Вы можете использовать HTTP.sys в надежных службах, импортируя пакет **Microsoft.ServiceFabric.AspNetCore.HttpSys** NuGet. Этот пакет `HttpSysCommunicationListener`содержит, `ICommunicationListener`осуществление . `HttpSysCommunicationListener`позволяет создать ASP.NET Core WebHost внутри надежной службы с помощью HTTP.sys в качестве веб-сервера.

HTTP.sys построен на [API сервера Windows HTTP.](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx) Этот API использует драйвер ядра **HTTP.sys** для обработки запросов HTTP и их маршрутизации в процессы, запускающие веб-приложения. Это позволяет нескольким процессам на одной и той же физической или виртуальной машине размещать веб-приложения в одном порту, в котором не разрешается использовать либо уникальный путь URL, либо имя хоста. Эти функции в Service Fabric полезны для размещения нескольких веб-сайтов в одном кластере.

>[!NOTE]
>Реализация HTTP.sys работает только на платформе Windows.

Следующая диаграмма иллюстрирует, как HTTP.sys использует драйвер ядра **HTTP.sys** в Windows для совместного использования портов:

![Диаграмма HTTP.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP.sys в службе без гражданства
Для использования `HttpSys` в службе без отслеживания состояния переопределите метод `CreateServiceInstanceListeners` и верните экземпляр `HttpSysCommunicationListener`:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>HTTP.sys в службе состояния

`HttpSysCommunicationListener`в настоящее время не предназначен для использования в государственных услуг из-за осложнений с основной функцией обмена **портом HTTP.sys.** Для получения дополнительной информации смотрите следующий раздел о динамическом распределении портов с помощью HTTP.sys. Для государственных служб Kestrel является предлагаемым веб-сервером.

### <a name="endpoint-configuration"></a>Настройка конечной точки

Для `Endpoint` веб-серверов, которые используют API сервера Windows HTTP, включая HTTP.sys, требуется конфигурация. Веб-серверы, которые используют Windows HTTP Server API, должны сначала зарезервировать свой URL-адрес с помощью HTTP.sys (обычно это выполняется с помощью инструмента [netsh).](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) 

Это действие требует повышенных привилегий, которых у служб по умолчанию нет. Параметры "http" или "https" `Protocol` для `Endpoint` свойства конфигурации в ServiceManifest.xml используются специально для того, чтобы поручить времени выполнения Service Fabric зарегистрировать URL-адрес http.sys от вашего имени. Он делает это с помощью сильного приставки [*URL-адреса подстановочных знаков.*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx)

Например, чтобы `http://+:80` зарезервировать для службы, используйте следующую конфигурацию в ServiceManifest.xml:

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

Также необходимо передать имя конечной точки в конструктор `HttpSysCommunicationListener`.

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>Используйте HTTP.sys со статическим портом
Чтобы использовать статический порт с HTTP.sys, `Endpoint` укажите номер порта в конфигурации:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Используйте HTTP.sys с динамическим портом
Чтобы использовать динамически назначенный порт с HTTP.sys, опустите свойство `Port` в конфигурации: `Endpoint`

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Динамический порт, `Endpoint` выделенный конфигурацией, предоставляет только один порт *на процесс узла.* Текущая модель хостинга Service Fabric позволяет размещать несколько экземпляров службы и/или реплики в одном и том же процессе. Это означает, что каждый из них `Endpoint` будет совместно распределяться в одном порту при выделении в конфигурацию. Несколько экземпляров **HTTP.sys** могут совместно использовать порт, используя базовую функцию совместного использования портов **HTTP.sys.** Но это не поддерживается `HttpSysCommunicationListener` из-за осложнений он вводит для запросов клиентов. Для динамического использования порта Kestrel является предлагаемым веб-сервером.

## <a name="kestrel-in-reliable-services"></a>Kestrel в Reliable Services
Вы можете использовать Kestrel в надежных службах, импортируя пакет **Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet. Этот пакет `KestrelCommunicationListener`содержит, `ICommunicationListener`осуществление . `KestrelCommunicationListener`позволяет создать ASP.NET Core WebHost внутри надежной службы, используя Kestrel в качестве веб-сервера.

Kestrel — это кроссплатформенный веб-сервер для ASP.NET Core. В отличие от HTTP.sys, Kestrel не использует централизованный конечный менеджер. Кроме того, в отличие от HTTP.sys, Kestrel не поддерживает совместное использование портов между несколькими процессами. Каждый экземпляр Kestrel должен использовать уникальный порт. Для получения дополнительной информации о Kestrel, [см.](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2)

![Схема Кестрел][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel в службе без отслеживания состояния
Для использования `Kestrel` в службе без отслеживания состояния переопределите метод `CreateServiceInstanceListeners` и верните экземпляр `KestrelCommunicationListener`:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>Kestrel в службе с отслеживанием состояния
Для использования `Kestrel` в службе с отслеживанием состояния переопределите метод `CreateServiceReplicaListeners` и верните экземпляр `KestrelCommunicationListener`:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

В этом примере одноэлементный экземпляр `IReliableStateManager` предоставляется контейнеру внедрения зависимостей WebHost. Это не является строго необходимым, но это `IReliableStateManager` позволяет использовать и надежные коллекции в ваших методах действия контроллера MVC.

Имя конфигурации `Endpoint`*не* предоставляется `KestrelCommunicationListener` в службе с отслеживанием состояния. Это объясняется более подробно в следующем разделе.

### <a name="configure-kestrel-to-use-https"></a>Настройка Kestrel для использования HTTPS
При включении HTTPS с Kestrel в ваш сервис, вам нужно установить несколько вариантов прослушивания. Обновление, `ServiceInstanceListener` чтобы использовать конечную точку *EndpointHttps* и слушать на определенном порту (например, порт 443). При настройке веб-хостинга для использования веб-сервера Kestrel необходимо настроить Kestrel для прослушивания адресов IPv6 во всех сетевых интерфейсах: 

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Для полного примера в учебнике [см. Наконфигурировать Kestrel для использования HTTPS.](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https)


### <a name="endpoint-configuration"></a>Настройка конечной точки
Конфигурация `Endpoint` не требуется для использования Kestrel. 

Kestrel является простым автономным веб-сервером. В отличие от HTTP.sys (или HttpListener), ему не нужна конфигурация `Endpoint` в ServiceManifest.xml, поскольку она не требует регистрации URL-адреса перед запуском. 

#### <a name="use-kestrel-with-a-static-port"></a>Использование Kestrel и статического порта
Вы можете настроить статический `Endpoint` порт в конфигурации ServiceManifest.xml для использования с Kestrel. Хотя это не является строго необходимым, он предлагает два потенциальных преимущества:
 - Если порт не попадает в диапазон прикладного порта, он открывается через брандмауэр OS сервисной тканью.
 - URL-адрес, предоставленный с помощью `KestrelCommunicationListener`, будет использовать этот порт.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Если `Endpoint` настроена, ее имя необходимо передать в конструктор `KestrelCommunicationListener`. 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Если ServiceManifest.xml не использует `Endpoint` конфигурацию, опустите `KestrelCommunicationListener` имя в конструкторе. В этом случае он будет использовать динамический порт. Более подробную информацию об этом можно узнать в следующем разделе.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Использование Kestrel и динамического порта
Kestrel не может использовать автоматическое `Endpoint` назначение порта из конфигурации в ServiceManifest.xml. Это связано с тем, `Endpoint` что автоматическое назначение порта из конфигурации назначает уникальный порт на *процесс узла,* а один процесс узла может содержать несколько экземпляров Kestrel. Это не работает с Kestrel, потому что он не поддерживает совместное использование портов. Таким образом, каждый экземпляр Kestrel должен быть открыт на уникальном порту.

Чтобы использовать динамическое назначение порта `Endpoint` с Kestrel, полностью опустите конфигурацию в ServiceManifest.xml и не передайте имя конечных точек `KestrelCommunicationListener` конструктору следующим образом:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

В этой конфигурации `KestrelCommunicationListener` автоматически выберет неиспользуемый порт из диапазона портов приложения.

Для HTTPS она должна иметь конечную точку, настроенную с протоколом HTTPS без порта, указанного в ServiceManifest.xml, и передавать имя конечных точек конструктору KestrelCommunicationListener.


## <a name="service-fabric-configuration-provider"></a>Поставщик конфигурации Service Fabric
Конфигурация приложения в ASP.NET Core основана на парах с ключевымзначениеми, установленных поставщиком конфигурации. Прочитайте [Configuration в ASP.NET Core,](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) чтобы понять больше об общей поддержке конфигурации ASP.NET Core.

В этом разделе описывается, как поставщик конфигурации Service `Microsoft.ServiceFabric.AspNetCore.Configuration` Fabric интегрируется с конфигурацией ASP.NET Core путем импорта пакета NuGet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>Расширения запуска AddServiceFabric
После импорта `Microsoft.ServiceFabric.AspNetCore.Configuration` пакета NuGet необходимо зарегистрировать источник конфигурации service Fabric с помощью API конфигурации ASP.NET Core. Вы делаете это, проверяя **addServiceFabric** расширения в пространстве `Microsoft.ServiceFabric.AspNetCore.Configuration` имен против `IConfigurationBuilder`.

```csharp
using Microsoft.ServiceFabric.AspNetCore.Configuration;

public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
        .AddServiceFabricConfiguration() // Add Service Fabric configuration settings.
        .AddEnvironmentVariables();
    Configuration = builder.Build();
}

public IConfigurationRoot Configuration { get; }
```

Теперь служба ASP.NET Core может получить доступ к настройкам конфигурации Service Fabric, как и любые другие настройки приложения. Например, можно использовать шаблон опций для загрузки настроек в сильно набранные объекты.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Отображение ключей по умолчанию
По умолчанию поставщик конфигурации Service Fabric включает имя пакета, имя раздела и имя свойства. Вместе они образуют ASP.NET ключ конфигурации Core:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Например, если у вас `MyConfigPackage` есть пакет конфигурации с названием со следующим `IConfiguration` содержанием, то значение конфигурации будет доступно на ASP.NET Core через *MyConfigPackage:MyConfigSection:MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Параметры конфигурации сервисной ткани
Поставщик конфигурации Service `ServiceFabricConfigurationOptions` Fabric также поддерживает изменение поведения ключей по умолчанию.

#### <a name="encrypted-settings"></a>Зашифрованные настройки
Service Fabric поддерживает зашифрованные настройки, как и поставщик конфигураций Service Fabric. Зашифрованные настройки не расшифроваются для ASP.NET Core `IConfiguration` по умолчанию. Вместо этого там хранятся зашифрованные значения. Но если вы хотите расшифровать значение для хранения в ASP.NET Core IConfiguration, `AddServiceFabricConfiguration` вы можете установить флаг *DecryptValue* на ложный в расширении, следующим образом:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = false); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Несколько пакетов конфигурации
Service Fabric поддерживает несколько пакетов конфигураций. По умолчанию имя пакета включено в ключ конфигурации. Но вы можете `IncludePackageName` установить флаг на ложные, следующим образом:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        // exclude package name from key.
        .AddServiceFabricConfiguration(activationContext, (options) => options.IncludePackageName = false); 
    Configuration = builder.Build();
}
```
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Пользовательские отображения ключей, извлечение стоимости и популяция данных
Поставщик конфигурации Service Fabric также поддерживает более продвинутые `ExtractKeyFunc` сценарии для настройки `ExtractValueFunc`отображения ключей и пользовательских извлечения значений с помощью. Вы даже можете изменить весь процесс заполнения данных из конфигурации `ConfigAction`Service Fabric в ASP.NET конфигурации Core с помощью.

Следующие примеры иллюстрируют, как использовать `ConfigAction` для настройки популяции данных:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    
    this.valueCount = 0;
    this.sectionCount = 0;
    var builder = new ConfigurationBuilder();
    builder.AddServiceFabricConfiguration(activationContext, (options) =>
        {
            options.ConfigAction = (package, configData) =>
            {
                ILogger logger = new ConsoleLogger("Test", null, false);
                logger.LogInformation($"Config Update for package {package.Path} started");

                foreach (var section in package.Settings.Sections)
                {
                    this.sectionCount++;

                    foreach (var param in section.Parameters)
                    {
                        configData[options.ExtractKeyFunc(section, param)] = options.ExtractValueFunc(section, param);
                        this.valueCount++;
                    }
                }

                logger.LogInformation($"Config Update for package {package.Path} finished");
            };
        });
  Configuration = builder.Build();
}
```

### <a name="configuration-updates"></a>Обновления конфигурации
Поставщик конфигурации Service Fabric также поддерживает обновления конфигурации. Можно использовать ASP.NET `IOptionsMonitor` Core для получения уведомлений об изменении, а затем перезагружать `IOptionsSnapshot` данные конфигурации. Для получения дополнительной информации смотрите [ASP.NET основные варианты](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

Эти параметры поддерживаются по умолчанию. Для включения обновлений конфигурации не требуется дальнейшее кодирование.

## <a name="scenarios-and-configurations"></a>Сценарии и конфигурации
В этом разделе представлена комбинация веб-сервера, конфигурации порта, вариантов интеграции Service Fabric и различных настроек, которые мы рекомендуем для устранения неполадок в следующих сценариях:
 - Доступные извне службы ASP.NET Core без отслеживания состояния
 - Внутренние ASP.NET основные услуги без гражданства
 - Внутренние услуги ASP.NET основных основных состояния

**Внешне экспонированная служба** — это служба, которая предоставляет конечную точку, вызванную из-за пределов кластера, обычно через балансера нагрузки.

**Служба только для внутренних** органов — это служба, конечная точка которой вызывается только из кластера.

> [!NOTE]
> Государственные конечные точки обслуживания, как правило, не должны подвергаться воздействию Интернета. Кластеры, стоящие за балансиваторами нагрузки, не знающих разрешения службы Service Fabric, такие как Azure Load Balancer, не смогут выявлять службы состояния. Это связано с тем, что балансизатор нагрузки не сможет найти и направить трафик на соответствующую реплику службы состояния. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Доступные извне службы ASP.NET Core без отслеживания состояния
Kestrel является предлагаемым веб-сервером для фронтовых служб, которые разоблачают внешние, интернет-точки http. На Windows HTTP.sys может предоставить возможность совместного использования портов, что позволяет размещать несколько веб-служб на одном и том же наборе узлов с использованием одного и того же порта. В этом сценарии веб-сервисы дифференцированы по имени или пути хоста, не полагаясь на прокси-сервер или шлюз для предоставления http маршрутизации.
 
При воздействии на Интернет служба без гражданства должна использовать хорошо известную и стабильную конечную точку, которая достижима через балансера нагрузки. Вы предоставите этот URL пользователям приложения. Используйте следующие конфигурации:

|  |  | **Примечания** |
| --- | --- | --- |
| Веб-сервер | Kestrel | Kestrel является предпочтительным веб-сервером, так как он поддерживается в Windows и Linux. |
| Конфигурация порта | static | Хорошо известный статический порт необходимо настроить в конфигурации `Endpoints` файла ServiceManifest.xml, например 80 для HTTP и 443 для HTTPS. |
| ServiceFabricIntegrationOptions | None | Используйте `ServiceFabricIntegrationOptions.None` опцию при настройке промежуточного программного обеспечения интеграции Service Fabric, чтобы служба не пыталась проверить входящие запросы на уникальный идентификатор. Внешние пользователи вашего приложения не будут знать уникальную идентификационную информацию, которую использует посредник. |
| Число экземпляров | -1 | В типичных случаях использования параметр количества экземпляров должен быть установлен до *-1.* Это делается для того, чтобы экземпляр был доступен на всех узлах, которые получают трафик от балансиватель нагрузки. |

Если несколько внешне открытых служб имеют один и тот же набор узлов, можно использовать HTTP.sys с уникальным, но стабильным траекторией URL. Вы можете достичь этого путем изменения URL при настройке IWebHost. Обратите внимание, что это относится только к HTTP.sys.

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>Служба ASP.NET Core без отслеживания состояния только для внутреннего использования
Службы без отслеживания состояния, которые вызываются только из кластера, должны использовать уникальные URL-адреса и динамически назначаемые порты для обеспечения взаимодействия между несколькими службами. Используйте следующие конфигурации:

|  |  | **Примечания** |
| --- | --- | --- |
| Веб-сервер | Kestrel | Хотя для внутренних служб без состояния можно использовать HTTP.sys, Kestrel является лучшим сервером, позволяющим нескольким экземплярам службы совместно использовать хост.  |
| Конфигурация порта | динамическое назначение | Несколько копий службы stateful могут совместно управлять процессом хоста или операционной системой хоста и, таким образом, потребуются уникальные порты. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | При динамическом назначении порта этот параметр предотвращает ошибочную идентификацию, описанную ранее. |
| InstanceCount | any | Для параметра количества экземпляров может быть присвоено любое значение, необходимое для работы службы. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Служба ASP.NET Core с отслеживанием состояния только для внутреннего использования
Службы с отслеживанием состояния, которые вызываются только из кластера, должны использовать динамически назначаемые порты для обеспечения взаимодействия между несколькими службами. Используйте следующие конфигурации:

|  |  | **Примечания** |
| --- | --- | --- |
| Веб-сервер | Kestrel | Не `HttpSysCommunicationListener` предназначен для использования службами состояния, в которых реплики совместно используют процесс хоста. |
| Конфигурация порта | динамическое назначение | Несколько копий службы stateful могут совместно управлять процессом хоста или операционной системой хоста и, таким образом, потребуются уникальные порты. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | При динамическом назначении порта этот параметр предотвращает ошибочную идентификацию, описанную ранее. |

## <a name="next-steps"></a>Дальнейшие действия
[Отладка приложения Service Fabric с помощью Visual Studio](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
