---
title: Взаимодействие со службами через ASP.NET Core | Документация Майкрософт
description: Сведения об использовании ASP.NET Core в службах Reliable Services c отслеживанием и без отслеживания состояния.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 638c06e1854504dcb7ff34b1d9df56694556c421
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939797"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Core в Azure Service Fabric Reliable Services

ASP.NET Core — фреймворк с открытым исходным кодом и платформах. Эта инфраструктура предназначена для создания приложений, облачных, подключенной к Интернету, таких как веб-приложения, приложения Интернета вещей и серверной части мобильных.

В этой статье, — это подробное руководство по размещению служб ASP.NET Core в Service Fabric Reliable Services с помощью **Microsoft.ServiceFabric.AspNetCore.** набор пакетов NuGet.

Вводное руководство по ASP.NET Core в Service Fabric и инструкции по настройке среды разработки, см. в разделе [руководства: Создание и развертывание приложения с интерфейсной службой веб-API ASP.NET Core и серверной службы с отслеживанием состояния](service-fabric-tutorial-create-dotnet-app.md).

В оставшейся части этой статьи предполагается, что вы уже знакомы с ASP.NET Core. Если это не так, прочитайте [основы ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core в среде Service Fabric

Приложения ASP.NET Core и Service Fabric можно запускать на .NET Core или полной версии .NET Framework. ASP.NET Core можно использовать двумя различными способами в Service Fabric:
 - **Разместить в виде гостевого исполняемого файла**. Таким образом используется главным образом для запуска существующих приложений ASP.NET Core в Service Fabric без изменения кода.
 - **Запустите внутри reliable service**. Таким образом, обеспечивает более эффективную интеграцию со средой выполнения Service Fabric и позволяет службам с отслеживанием состояния ASP.NET Core.

В оставшейся части этой статьи объясняется, как использовать ASP.NET Core внутри reliable service, через компоненты интеграции ASP.NET Core, входящие в состав пакета SDK Service Fabric.

## <a name="service-fabric-service-hosting"></a>Размещение службы Service Fabric

В Service Fabric, один или несколько экземпляров и (или) реплик службы выполняются в *хост-процессе службы*: исполняемый файл, который выполняет код службы. Автор службы приобретенных хост-процесса службы и Service Fabric активирует и отслеживает.

Обычно ASP.NET (до MVC 5) тесно связан с IIS через System.Web.dll. ASP.NET Core разделяет веб-сервер и веб-приложение. Это разделение позволяет веб-приложений, которую можно переносить между различных веб-серверах. Она также позволяет веб-серверы *резидентным*. Это означает, что веб-сервера можно запустить в собственном процессе, в отличие от процесса, владельцем которого является по выделенный веб-сервера, такими как службы IIS.

Объединить службу Service Fabric и ASP.NET, в виде гостевого исполняемого файла или в reliable service, должен иметь возможность запустить ASP.NET в хост-процесса службы. Автономное размещение ASP.NET Core позволяет это сделать.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Размещение ASP.NET Core в reliable service
Как правило, автономно размещаемые приложения ASP.NET Core создают WebHost в точке входа приложения, например метод `static void Main()` в `Program.cs`. В этом случае жизненный цикл WebHost привязан к жизненный цикл процесса.

![Размещение ASP.NET Core в процессе][0]

Но точку входа приложения не подходит для создания WebHost в reliable service. Том, что точка входа приложения используется только для регистрации типа службы в среде выполнения Service Fabric, таким образом, чтобы его можно создавать экземпляры этого типа службы. WebHost должен будет создан в reliable service сам. В хост-процессе экземпляры службы и (или) реплик можно выполнить несколько жизненных циклов. 

Экземпляр Reliable Service представлен с помощью класса службы, производного от `StatelessService` или `StatefulService`. Стек связи для службы содержится в реализации `ICommunicationListener` класса службы. `Microsoft.ServiceFabric.AspNetCore.*` Пакеты NuGet содержат реализации `ICommunicationListener` , которые запускают и администрируют ASP.NET Core WebHost для Kestrel или HTTP.sys в reliable service.

![Схема для размещения ASP.NET Core в reliable service][1]

## <a name="aspnet-core-icommunicationlisteners"></a>Объекты ICommunicationListener в ASP.NET Core
`ICommunicationListener` Реализации для Kestrel и HTTP.sys в `Microsoft.ServiceFabric.AspNetCore.*` пакетами NuGet имеют похожие шаблоны использования. Но они выполняют несколько различные действия для каждого веб-сервера. 

Оба прослушивателя связи предоставляют конструктор, который принимает следующие аргументы:
 - **`ServiceContext serviceContext`**. Это `ServiceContext` , содержащий сведения о запущенной службы.
 - **`string endpointName`**. Это имя `Endpoint` конфигурации в файле ServiceManifest.xml. В первую очередь это отличие прослушивателей связи. HTTP.sys *требует* `Endpoint` конфигурации, пока не Kestrel.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**. Это лямбда-выражение, реализуемые, в котором создается и возвращается `IWebHost`. Он позволяет настроить `IWebHost` способом, обычно в приложении ASP.NET Core. Лямбда-выражение содержит URL-адрес, который создается автоматически, в зависимости от параметров интеграции Service Fabric можно использовать и `Endpoint` вами конфигурации. Затем можно изменить или использовать этот URL-адрес для запуска веб-сервера.

## <a name="service-fabric-integration-middleware"></a>ПО промежуточного уровня для интеграции Service Fabric
`Microsoft.ServiceFabric.AspNetCore` Пакет NuGet включает `UseServiceFabricIntegration` метод расширения в `IWebHostBuilder` , добавляющий поддерживающей Service Fabric по промежуточного слоя. Это по промежуточного слоя настраивает Kestrel и HTTP.sys `ICommunicationListener` для регистрации службы уникальный URL-адрес в службе именования Service Fabric. Затем проверяет запросы клиентов, чтобы убедиться, что клиенты подключались к правильной службе. 

Этот шаг необходим предотвратить ошибочные подключения к неверной службе клиентов. Это необходимо, поскольку в среде общего узла, например Service Fabric, нескольких веб-приложений могут работать на одном физическом компьютере или виртуальной машины, но не используют уникальные имена узлов. Данный сценарий описан более подробно в следующем разделе.

### <a name="a-case-of-mistaken-identity"></a>Причина ошибочный идентификации
Реплики службы, независимо от протокола, прослушивают уникальное сочетание "IP-адрес:порт". После запуска ожидает передачи данных через конечную точку IP: Port реплика службы он сообщает адрес этой конечной службе именования Service Fabric. Клиенты или другие службы можно узнать. Если службы используют приложение динамически назначаемые порты, реплика службы может случайно использовать той же конечной точке IP: Port, другой службы, ранее на одном физическом или виртуальной машины. Это может привести к ошибочному подключению клиента к неверной службе, Если происходит следующая последовательность событий, это может привести к этот сценарий:

 1. Служба А прослушивает 10.0.0.1:30000 по протоколу HTTP. 
 2. Клиент разрешает службу и возвращает адрес 10.0.0.1:30000.
 3. Служба А перемещается на другой узел.
 4. Служба Б помещается в 10.0.0.1 и случайно использует тот же порт 30000.
 5. Клиент пытается подключиться к службе А с помощью кэшированного адреса 10.0.0.1:30000.
 6. Клиент успешно подключен к службе Б, не зная, что подключен к неверной службе.

Это может периодически вызывать ошибки, которые трудно диагностировать.

### <a name="using-unique-service-urls"></a>Использование уникальных URL-адресов службы
Чтобы избежать этих ошибок, службы можно разместить конечную точку в службе имен с уникальным идентификатором и затем проверять его во время клиентских запросов. Это совместное действие между службами в доверенной среде дружественного клиента. Она не предоставляет безопасную проверку подлинности службы в среде дружественного клиента.

В доверенной среде, по промежуточного слоя, который добавляется с `UseServiceFabricIntegration` метод автоматически добавляет уникальный идентификатор к адресу учтена в службе именования. Он проверяет этот идентификатор при каждом запросе. Если идентификатор не совпадает, по промежуточного слоя немедленно возвращает ответ HTTP 410 — потеряно.

Службы, использующие динамически назначаемый порт, должны использовать это ПО промежуточного слоя.

Службы, использующие постоянный уникальный порт не имеют такой проблемы в совместной среде. Постоянный уникальный порт обычно используется для внешних служб, которым необходим известный порт для подключения клиентских приложений. Например большинство веб-приложений с выходом в Интернет будет использовать порт 80 или 443 для подключений веб-браузера. В этом случае не следует включать уникальный идентификатор.

На схеме ниже показан поток запроса с включенным ПО промежуточного слоя.

![Интеграция Service Fabric ASP.NET Core][2]

Kestrel и HTTP.sys `ICommunicationListener` реализации используют этот механизм точно таким же образом. Несмотря на то, что HTTP.sys может внутренне различать запросы, на основе уникальных URL-адрес с помощью базового **HTTP.sys** функции, эта функция совместного использования портов *не* используемые HTTP.sys `ICommunicationListener`реализации. Это за счет HTTP 503 и HTTP 404 коды состояния ошибок в сценарии, описанном выше. В свою очередь затрудняет для клиентов с целью определения причины ошибки, так как HTTP 503 и HTTP 404 часто используются для указания других ошибок. 

Таким образом, Kestrel и HTTP.sys `ICommunicationListener` реализаций стандартизировать промежуточного слоя, предоставляемое `UseServiceFabricIntegration` метода расширения. Таким образом клиенты достаточно выполнить действие повторного разрешения конечной точки службы в ответах HTTP 410.

## <a name="httpsys-in-reliable-services"></a>HTTP.sys в Reliable Services
HTTP.sys можно использовать в Reliable Services, импортировав **Microsoft.ServiceFabric.AspNetCore.HttpSys** пакет NuGet. Этот пакет содержит `HttpSysCommunicationListener`, реализация `ICommunicationListener`. `HttpSysCommunicationListener` позволяет создавать WebHost ASP.NET Core внутри reliable service с помощью веб-сервер HTTP.sys.

HTTP.sys основан на [API сервера HTTP Windows](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Этот API использует **HTTP.sys** драйвер ядра для обработки HTTP-запросов и их перенаправления в процессы, использующие веб-приложений. Это позволяет нескольким процессам на одном физическом компьютере или виртуальной машины для размещения веб-приложений на один и тот же порт, которые отличаются либо уникальный URL-адрес, путь или имя узла. Эти функции в Service Fabric полезны для размещения нескольких веб-сайтов в одном кластере.

>[!NOTE]
>Реализация HTTP.sys работает только на платформе Windows.

На следующей схеме показано, как HTTP.sys использует **HTTP.sys** драйвер ядра в Windows для совместного использования портов:

![Схема HTTP.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP.sys в службы без отслеживания состояния
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

### <a name="httpsys-in-a-stateful-service"></a>HTTP.sys в службе с отслеживанием состояния

`HttpSysCommunicationListener` не сейчас предназначена для использования в службах с отслеживанием состояния из-за сложности с базовой **HTTP.sys** функции совместного использования портов. Дополнительные сведения см. в разделе на динамического выделения портов в HTTP.sys. Для служб с отслеживанием состояния предлагаемые веб-сервером является Kestrel.

### <a name="endpoint-configuration"></a>Настройка конечной точки

`Endpoint` Настройка не требуется для веб-серверов, использующих API сервера HTTP Windows, включая HTTP.sys. Веб-серверов, использующих API сервера HTTP Windows, сначала необходимо зарезервировать URL-адрес в HTTP.sys (обычно это осуществляется с помощью [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) средство). 

Это действие требует повышенные привилегии, не имеющие служб по умолчанию. Параметры «http» или «https» `Protocol` свойство `Endpoint` конфигурации в файле ServiceManifest.xml используются специально для того, чтобы заставить среду выполнения Service Fabric, чтобы зарегистрировать URL-адрес в HTTP.sys от вашего имени. Это делается с помощью [ *строгий шаблон* ](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) префикс URL-адреса.

Например, чтобы зарезервировать `http://+:80` для службы, используйте следующую конфигурацию в файле ServiceManifest.xml:

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

#### <a name="use-httpsys-with-a-static-port"></a>Использование статического порта с HTTP.sys
Чтобы использовать статический порт с HTTP.sys, укажите номер порта в `Endpoint` конфигурации:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Использование динамического порта с HTTP.sys
Чтобы использовать динамически назначаемый порт с HTTP.sys, опустите `Port` свойство в `Endpoint` конфигурации:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Динамический порт, выделенной с помощью `Endpoint` конфигурации имеется только один порт *каждому хост-процессу*. Текущий модель размещения Service Fabric позволяет несколько экземпляров службы или реплик, которые размещены в одном процессе. Это означает, что каждый из них будут совместно использовать один и тот же порт при выделении с помощью `Endpoint` конфигурации. Несколько **HTTP.sys** экземпляров могут совместно использовать порт с помощью базового **HTTP.sys** функции совместного использования портов. Но не поддерживаются `HttpSysCommunicationListener` из-за сложности обработки клиентских запросов. Для использования динамических портов предлагаемые веб-сервером является Kestrel.

## <a name="kestrel-in-reliable-services"></a>Kestrel в Reliable Services
Можно использовать Kestrel в Reliable Services, импортировав **Microsoft.ServiceFabric.AspNetCore.Kestrel** пакет NuGet. Этот пакет содержит `KestrelCommunicationListener`, реализация `ICommunicationListener`. `KestrelCommunicationListener` позволяет создавать WebHost ASP.NET Core внутри reliable service с помощью Kestrel в качестве веб-сервера.

Kestrel — это кроссплатформенный веб-сервер для ASP.NET Core на основе libuv, кроссплатформенной библиотеки асинхронных операций ввода-вывода. В отличие от HTTP.sys Kestrel не использует централизованный диспетчер конечных точек. Также в отличие от HTTP.sys, Kestrel не поддерживает совместное использование порта между несколькими процессами. Каждый экземпляр Kestrel должен использовать уникальный порт.

![Схема kestrel][4]

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

В этом примере одноэлементный экземпляр `IReliableStateManager` предоставляется контейнеру внедрения зависимостей WebHost. Это не является обязательным, но он позволяет использовать `IReliableStateManager` и надежных коллекций в метод действия контроллера MVC.

Имя конфигурации `Endpoint` *не* предоставляется `KestrelCommunicationListener` в службе с отслеживанием состояния. Это объясняется более подробно в следующем разделе.

### <a name="configure-kestrel-to-use-https"></a>Настройка Kestrel для использования HTTPS
При включении HTTPS с Kestrel в службе, вам потребуется задать несколько параметров для прослушивания. Обновление `ServiceInstanceListener` использовать *EndpointHttps* конечной точки "и" прослушивание определенного порта (например, порт 443). При настройке веб-узла для использования веб-сервер Kestrel, необходимо настроить Kestrel для прослушивания для IPv6-адресов через все сетевые интерфейсы: 

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

Полный пример, в руководстве, см. в разделе [Настройка Kestrel для использования протокола HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Настройка конечной точки
`Endpoint` Конфигурации не обязательно использовать Kestrel. 

Kestrel — это автономная веб-сервера. В отличие от HTTP.sys (или HttpListener), ему не нужны `Endpoint` конфигурации в файле ServiceManifest.xml, так как он не требует регистрации URL-адрес перед запуском. 

#### <a name="use-kestrel-with-a-static-port"></a>Использование Kestrel и статического порта
Можно настроить статический порт в `Endpoint` конфигурации файла servicemanifest.XML для использования с Kestrel. Несмотря на то, что это не является обязательной, она предоставляет два потенциальных преимущества:
 - Если порт не попадает ни в диапазоне портов приложения, он открывается в брандмауэре ОС с Service Fabric.
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

Если не использовать ServiceManifest.xml `Endpoint` конфигурации, не указывать имя в `KestrelCommunicationListener` конструктор. В этом случае он будет использовать динамический порт. Дополнительные сведения об этом см.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Использование Kestrel и динамического порта
Kestrel не может использовать автоматическое назначение порта из `Endpoint` конфигурации в файле ServiceManifest.xml. Это потому, что автоматическое назначение порта `Endpoint` конфигурация назначает уникальный порт каждому *хост-процесса*, и единственный размещающий процесс может содержать несколько экземпляров Kestrel. Это не работает с Kestrel, так как он не поддерживает совместное использование порта. Таким образом каждый экземпляр Kestrel должен быть открыт на уникальном порте.

Чтобы использовать динамическое назначение порта с Kestrel, опустите `Endpoint` конфигурации в файле ServiceManifest.xml полностью и не передавайте имя конечной точки для `KestrelCommunicationListener` конструктора, как показано ниже:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

В этой конфигурации `KestrelCommunicationListener` автоматически выберет неиспользуемый порт из диапазона портов приложения.

## <a name="service-fabric-configuration-provider"></a>Поставщик конфигурации Service Fabric
Конфигурация приложения в ASP.NET Core основан на пары ключ значение, установить поставщик конфигурации. Чтение [конфигурация в ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) для понимания дополнительные на общие поддержка ASP.NET Core конфигурации.

В этом разделе описывается, как поставщик конфигурации Service Fabric интегрируется с конфигурации ASP.NET Core, импортировав `Microsoft.ServiceFabric.AspNetCore.Configuration` пакет NuGet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration автоматически запускаемых расширений
После импорта `Microsoft.ServiceFabric.AspNetCore.Configuration` пакета NuGet, необходимо зарегистрировать источник конфигурации Service Fabric с помощью API конфигурации ASP.NET Core. Это делается путем проверки **AddServiceFabricConfiguration** расширений в `Microsoft.ServiceFabric.AspNetCore.Configuration` пространстве имен для `IConfigurationBuilder`.

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

Теперь служба ASP.NET Core может использовать параметры конфигурации Service Fabric, так же, как и любые другие параметры приложения. Например можно использовать шаблон параметров для загрузки параметров в строго типизированных объектов.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Сопоставления ключей по умолчанию
По умолчанию поставщик конфигурации Service Fabric включает в себя имя пакета, имя раздела и имя свойства. Вместе они формируют ключ конфигурации ASP.NET Core, следующим образом:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Например, если у вас есть пакет конфигурации с именем `MyConfigPackage` со следующим содержимым, затем значение конфигурации доступны на ASP.NET Core `IConfiguration` через *MyConfigPackage:MyConfigSection:MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Параметры конфигурации Service Fabric
Поставщик конфигурации Service Fabric также поддерживает `ServiceFabricConfigurationOptions` для изменения поведения по умолчанию для сопоставления ключей.

#### <a name="encrypted-settings"></a>С помощью зашифрованных параметров
Service Fabric поддерживает зашифрованные параметры, как поставщик конфигурации Service Fabric. Расшифровать зашифрованные параметры для ASP.NET Core `IConfiguration` по умолчанию. Зашифрованные значения хранятся в существует вместо этого. Но если вы хотите расшифровки значения для сохранения в ASP.NET Core IConfiguration, можно задать *DecryptValue* флаг в значение false в `AddServiceFabricConfiguration` расширения, как показано ниже:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = true); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Несколько пакетов конфигурации
Service Fabric поддерживает несколько пакетов конфигурации. По умолчанию имя пакета включается в ключ конфигурации. Однако можно задать `IncludePackageName` флаг имеет значение false, следующим образом:
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
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Пользовательские сопоставления ключей, значение извлечения и заполнения данных
Поставщик конфигурации Service Fabric также поддерживает более сложные сценарии для настройки сопоставления ключей с `ExtractKeyFunc` и custom извлечение значений с помощью `ExtractValueFunc`. Можно даже изменить весь процесс заполнения данных из конфигурации Service Fabric конфигурации ASP.NET Core с помощью `ConfigAction`.

В следующих примерах показано использование `ConfigAction` осуществить заполнение данными:
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
Поставщик конфигурации Service Fabric также поддерживает обновления конфигурации. ASP.NET Core можно использовать `IOptionsMonitor` получать уведомления об изменениях, а затем использовать `IOptionsSnapshot` перезагрузить данные конфигурации. Дополнительные сведения см. в разделе [параметры ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

Эти параметры поддерживаются по умолчанию. Для включения обновлений конфигурации требуется дальнейшая кодирования.

## <a name="scenarios-and-configurations"></a>Сценарии и конфигурации
Этот раздел содержит сочетание веб-сервера, конфигурации порта, параметров интеграции Service Fabric и прочие параметры, мы рекомендуем для устранения неполадок в следующих сценариях:
 - Доступные извне службы ASP.NET Core без отслеживания состояния
 - Только для внутреннего использования служб без отслеживания состояния ASP.NET Core
 - Только для внутреннего использования служб с отслеживанием состояния ASP.NET Core

**Извне службы** — это приложения, предоставляет конечную точку, из которого был вызван вне кластера, обычно через балансировщик нагрузки.

**Только для внутреннего** — это служба, конечная точка которого вызывается только из кластера.

> [!NOTE]
> Конечные точки службы с отслеживанием состояния обычно не должны быть доступны в Интернете. Кластеры за балансировщиками нагрузки, которые не поддерживают разрешение служб Service Fabric, таких как Azure Load Balancer, не сможет предоставлять службы с отслеживанием состояния. Том, что Подсистема балансировки нагрузки не сможет обнаруживать и направлять трафик к реплике соответствующие службы с отслеживанием состояния. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Доступные извне службы ASP.NET Core без отслеживания состояния
Kestrel — это предлагаемое веб-сервера для внешних служб, которые предоставляют внешние Интернета конечные точки HTTP. В Windows HTTP.sys можно предоставить возможность общего доступа порт, который позволяет размещать несколько веб-служб на тот же набор узлов, используя тот же порт. В этом случае веб-служб различаются по имени узла или пути, не полагаясь на интерфейсный прокси-сервер или шлюз для маршрутизации HTTP.
 
Когда через Интернет, служба без отслеживания состояния следует использовать хорошо известную и стабильную конечную точку, доступную через подсистему балансировки нагрузки. Этот URL-адрес будет предоставлять приложения пользователям. Используйте следующие конфигурации:

|  |  | **Примечания** |
| --- | --- | --- |
| Веб-сервер | Kestrel | Kestrel является предпочтительным веб-сервером, так как он поддерживается в Windows и Linux. |
| Конфигурация порта | static | Хорошо известный статический порт необходимо настроить в конфигурации `Endpoints` файла ServiceManifest.xml, например 80 для HTTP и 443 для HTTPS. |
| ServiceFabricIntegrationOptions | Нет | Используйте `ServiceFabricIntegrationOptions.None` , при настройке по промежуточного слоя интеграции Service Fabric, чтобы службы не пытается проверять входящие запросы для уникального идентификатора. Внешние пользователи приложения не будет знать уникальных идентификационных данных, который использует по промежуточного слоя. |
| Число экземпляров | -1 | При стандартных вариантах использования, для параметра количества экземпляров должно быть присвоено *-1*. Это делается, чтобы экземпляр становится доступным на всех узлах, получающих трафик от подсистемы балансировки нагрузки. |

Если несколько служб, предоставляемых извне, совместно используют тот же набор узлов, можно использовать HTTP.sys с уникальным, но стабильным URL-путь. Это можно сделать, изменив URL-адрес, указанный при настройке IWebHost. Обратите внимание на то, что это относится к HTTP.sys только.

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
| Веб-сервер | Kestrel | Несмотря на то, что можно использовать HTTP.sys для внутренних служб без отслеживания состояния, Kestrel является лучшим сервера, чтобы разрешить несколько экземпляров службы совместного использования узла.  |
| Конфигурация порта | динамическое назначение | Несколько реплик службы с отслеживанием состояния могут совместно использовать хост-процесса или операционной системы и поэтому для них требуются уникальные порты. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | При динамическом назначении порта этот параметр предотвращает ошибочную идентификацию, описанную ранее. |
| InstanceCount | любой | Для параметра количества экземпляров может быть присвоено любое значение, необходимое для работы службы. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Служба ASP.NET Core с отслеживанием состояния только для внутреннего использования
Службы с отслеживанием состояния, которые вызываются только из кластера, должны использовать динамически назначаемые порты для обеспечения взаимодействия между несколькими службами. Используйте следующие конфигурации:

|  |  | **Примечания** |
| --- | --- | --- |
| Веб-сервер | Kestrel | `HttpSysCommunicationListener` Не предназначена для использования служб с отслеживанием состояния, в которых реплики совместно хост-процесс. |
| Конфигурация порта | динамическое назначение | Несколько реплик службы с отслеживанием состояния могут совместно использовать хост-процесса или операционной системы и поэтому для них требуются уникальные порты. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | При динамическом назначении порта этот параметр предотвращает ошибочную идентификацию, описанную ранее. |

## <a name="next-steps"></a>Дальнейшие действия
[Отладка приложения Service Fabric с помощью Visual Studio](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
