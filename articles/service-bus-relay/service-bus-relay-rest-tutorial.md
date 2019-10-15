---
title: Руководство по REST с использованием ретранслятора Azure | Документация Майкрософт
description: Создайте ведущее приложение ретранслятора служебной шины Azure, которое предоставляет интерфейс на основе интерфейса RESTFUL.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 1312b2db-94c4-4a48-b815-c5deb5b77a6a
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: spelluru
ms.openlocfilehash: a3daa7847ef037f0276792bf8173ad55aba0a944
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212925"
---
# <a name="azure-wcf-relay-rest-tutorial"></a>Руководство по REST для ретранслятора WCF Azure

В этом учебнике описано, как создать ведущее приложение Azure Relay, предоставляющее интерфейс на основе интерфейса RESTFUL. REST позволяет веб-клиенту, например веб-браузеру, получить доступ к интерфейсам API служебной шины с помощью HTTP-запросов.

В этом руководстве для создания службы REST в ретрансляторе Azure используется модель программирования REST Windows Communication Foundation (WCF). Дополнительные сведения см. в разделе [модель программирования WCF RESTful](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) и [проектирование и реализация служб](/dotnet/framework/wcf/designing-and-implementing-services).

В этом руководстве рассматриваются следующие задачи.

> [!div class="checklist"]
>
> * Установите необходимые компоненты для работы с этим руководством.
> * Создание пространства имен ретранслятора.
> * Определите контракт службы WCF на основе RESTFUL.
> * Реализуйте контракт WCF на основе RESTFUL.
> * Размещение и запуск службы WCF на основе остальных компонентов.
> * Запустите и протестируйте службу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с данным руководством вам потребуется:

* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начать работу.
* [Visual Studio 2015 или более поздней версии](https://www.visualstudio.com). В примерах этого руководства используется Visual Studio 2019.
* Пакет Azure SDK для .NET. Установите его со [страницы загрузки SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Создание пространства имен ретранслятора

Чтобы начать использовать функции ретранслятора Azure, необходимо сначала создать пространство имен службы. Пространство имен предоставляет контейнер для адресации ресурсов Azure в вашем приложении. Выполните [эти инструкции](relay-create-namespace-portal.md), чтобы создать пространство имен ретранслятора.

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>Определение контракта службы WCF на основе REST, используемого в Azure Relay

При создании службы WCF на основе REST необходимо определить контракт. Контракт определяет, какие операции поддерживает узел. Операция службы похожа на метод веб-службы. Определите контракт с помощью C++интерфейса, C#или Visual Basic. Каждый метод в интерфейсе соответствует определенной операции службы. Примените атрибут [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) к каждому интерфейсу и примените к каждой операции атрибут [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) . 

> [!TIP]
> Если метод в интерфейсе с [атрибутом](/dotnet/api/system.servicemodel.operationcontractattribute)ServiceContractAttribute не имеет [атрибута](/dotnet/api/system.servicemodel.servicecontractattribute) OperationContractAttribute, этот метод не предоставляется. Код, используемый для этих задач, отображается в примере, описанном ниже.

Основное различие между WCF и контрактом REST заключается в добавлении свойства к атрибуту [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute): [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute). Это свойство позволяет сопоставить метод в интерфейсе с методом на другой стороне интерфейса. В этом примере атрибут [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) используется для связывания метода с `HTTP GET`. Такой подход позволяет служебной шине точно получать и интерпретировать команды, отправленные в интерфейс.

### <a name="to-create-a-contract-with-an-interface"></a>Создание контракта с помощью интерфейса

1. Запустите Microsoft Visual Studio от имени администратора. Для этого щелкните правой кнопкой мыши значок программы Visual Studio и выберите **Запуск от имени администратора**.
1. В Visual Studio выберите **создать новый проект**.
1. В окне **Создание нового проекта**выберите **консольное приложение (.NET Framework)** для C# и нажмите кнопку **Далее**.
1. Назовите проект *ImageListener*. Используйте **Расположение**по умолчанию и нажмите кнопку **создать**.

   Для C# проекта Visual Studio создает файл *Program.CS* . Этот класс содержит пустой метод `Main()`, необходимый для правильной сборки проекта консольного приложения.

1. В **Обозреватель решений**щелкните правой кнопкой мыши проект **ImageListener** , а затем выберите **Управление пакетами NuGet**.
1. Нажмите кнопку **Обзор**, найдите и выберите **WindowsAzure. servicebus**. Выберите **установить**и примите условия использования.

    На этом шаге добавляются ссылки на служебную шину и *файл System. ServiceModel. dll*. Этот пакет автоматически добавляет ссылки на библиотеки служебной шины и WCF `System.ServiceModel`.

1. Явно добавьте ссылку `System.ServiceModel.Web.dll` на проект. В **Обозреватель решений**щелкните правой кнопкой мыши элемент **ссылки** в папке проекта и выберите команду **Добавить ссылку**.
1. В окне **Добавить ссылку**выберите **платформа** и введите *System. ServiceModel. Web* в окне **поиска**. Установите флажок **System.ServiceModel.Web**, а затем нажмите кнопку **ОК**.

Затем внесите следующие изменения в код проекта:

1. Добавьте следующие `using` инструкции в начало файла *Program.CS* .

    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    * [System.ServiceModel](/dotnet/api/system.servicemodel) — это пространство имен, которое предоставляет программный доступ к основным функциям WCF. Ретранслятор WCF использует множество объектов и атрибутов WCF для определения контрактов службы. Это пространство имен будет использоваться в большинстве приложений ретранслятора.
    * [System. ServiceModel. Channels](/dotnet/api/system.servicemodel.channels) помогает определить канал, который является объектом, через который осуществляется обмен данными с Azure Relay и веб-браузером клиента.
    * [System. ServiceModel. Web](/dotnet/api/system.servicemodel.web) содержит типы, позволяющие создавать веб-приложения.

1. `Microsoft.ServiceBus.Samples`Переименуйте `ImageListener` пространство имен в.

    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Сразу после открывающей фигурной скобки объявления пространства имен определите новый интерфейс с именем `IImageContract` и `ServiceContractAttribute` примените к `https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1`интерфейсу атрибут со значением. 

    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

    Значение пространства имен отличается от пространства имен, которое используется во всей области кода. Значение namespace является уникальным идентификатором для этого контракта и должно иметь сведения о версии. Дополнительные сведения см. в статье [Управление версиями службы](/dotnet/framework/wcf/service-versioning). Явное указание пространства позволяет предотвратить добавление значение пространства имен по умолчанию к имени контракта.

1. В интерфейсе объявите метод для отдельной операции `IImageContract` , которую контракт предоставляет в интерфейсе, и примените `OperationContract` атрибут к методу, который требуется предоставить в рамках контракта общедоступной служебной шины. `IImageContract`

    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

1. В атрибуте добавьте `WebGet`значение. `OperationContract`

    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

   Добавление значения позволяет службе ретранслятора маршрутизировать запросы HTTP GET к `GetImage`и `GetImage` преобразует `HTTP GETRESPONSE` возвращаемые значения в ответ. `WebGet` Далее в этом руководстве вы будете использовать веб-браузер для доступа к этому методу и для вывода изображения в браузере.

1. Сразу после определения `IImageContract` объявите канал, наследующий от интерфейсов `IImageContract` и `IClientChannel`.

    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

   Канал представляет собой объект WCF, посредством которого служба и клиент обмениваются информацией. Канал в своем хост-приложении вы создадите позже. Затем Azure Relay использует этот канал для передачи в `GetImage` реализацию HTTP-запросов GET от браузера. Ретранслятор также использует канал для получения `GetImage` возвращаемого значения и его перевода `HTTP GETRESPONSE` в для клиентского браузера.

1. Выберите **Build** > **Build Solution** (сборка сборки), чтобы подтвердить точность работы на данный момент.

### <a name="example-that-defines-a-wcf-relay-contract"></a>Пример, определяющий контракт Ретранслятор WCF

Ниже приведен код базового интерфейса, определяющего контракт ретранслятора WCF.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="implement-the-rest-based-wcf-service-contract"></a>Реализация контракта службы WCF на основе REST

Чтобы создать службу Ретранслятор WCF в стиле "ОСТАВШИЙся", сначала создайте контракт с помощью интерфейса. Следующим шагом является реализация интерфейса. Эта процедура включает создание класса с именем `ImageService` , реализующего пользовательский `IImageContract` интерфейс. После реализации контракта необходимо настроить интерфейс с помощью файла *app. config* . Файл конфигурации содержит необходимые сведения для приложения. Эти сведения включают имя службы, имя контракта и тип протокола, используемого для связи со службой ретрансляции. Код, используемый для этих задач, отображается в примере, описанном ниже.

Как и в предыдущих шагах, существует небольшая разница между реализацией контракта в стиле RESTFUL и Ретранслятор WCFным контрактом.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Реализация контракта REST служебной шины

1. Создайте новый класс с именем `ImageService` сразу после определения интерфейса `IImageContract`. Класс `ImageService` реализует интерфейс `IImageContract`.

    ```csharp
    class ImageService : IImageContract
    {
    }
    ```

    Как и в случае с другими реализациями интерфейсов, определение можно реализовать в другом файле. Но в этом руководстве реализация осуществляется в том же файле, что и определение интерфейса и метод `Main()`.

1. Примените к `IImageService` классу атрибут [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute), чтобы указать, что класс является реализацией контракта WCF.

    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Как упоминалось ранее, это пространство имен не является традиционным пространством имен. Он является частью архитектуры WCF, определяющей контракт. Дополнительные сведения см. в разделе [имена контрактов данных](/dotnet/framework/wcf/feature-details/data-contract-names/).

1. Добавьте в проект изображение *. jpg* . Этот файл представляет собой рисунок, отображаемый службой в принимающем браузере.

   1. Щелкните проект правой кнопкой мыши и выберите **Добавить**.
   1. Выберите **существующий элемент**.
   1. Используйте команду **Добавить существующий элемент** , чтобы перейти к соответствующему JPG, а затем нажмите кнопку **Добавить**. При добавлении файла выберите **все файлы** из раскрывающегося списка рядом с **именем файла**.

   В оставшейся части этого руководства предполагается, что имя изображения — *Image. jpg*. При использовании другого файла необходимо переименовать изображение или изменить код соответствующим образом.

1. Чтобы убедиться, что выполняющаяся служба может найти файл образа, в **Обозреватель решений** щелкните правой кнопкой мыши файл образа и выберите пункт **свойства**. В окне **Свойства**установите для параметра **Копировать в выходной каталог** значение **Копировать, если новее**.

1. Используйте процедуру в, [чтобы создать контракт с интерфейсом](#to-create-a-contract-with-an-interface) для добавления ссылки на сборку *System. Drawing. dll* в проект.

1. Добавьте следующие связанные `using` инструкции:

    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

1. `ImageService` В классе добавьте следующий конструктор, который загружает точечный рисунок и готовит его к отправке в браузер клиента:

    ```csharp
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";
   
        Image bitmap;
   
        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

1. Сразу после предыдущего кода добавьте `GetImage` `ImageService` в класс следующий метод для возврата HTTP-сообщения, содержащего изображение.

    ```csharp
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```

    Эта реализация использует `MemoryStream` для получения образа и подготовки его для потоковой передачи в браузер. Он запускает нулевую точку в потоке, объявляет содержимое потока в формате *JPG*и передает данные в поток.

1. Выберите **Сборка** > сборка**решения**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Определение конфигурации для запуска веб-службы в служебной шине

1. В **Обозреватель решений**дважды щелкните файл **app. config** , чтобы открыть его в редакторе Visual Studio.

    Файл *app. config* содержит имя службы, конечную точку и привязку. Конечная точка — это расположение, Azure Relay которое предоставляет клиентам и узлам возможность взаимодействия друг с другом. Привязка — это тип протокола, который используется для обмена данными. Основное различие состоит в том, что настроенная конечная точка службы ссылается на привязку [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding).

1. Элемент XML `<system.serviceModel>` представляет собой элемент WCF, определяющий одну или несколько служб. Здесь он используется для определения имени службы и конечной точки. В нижней `<system.serviceModel>` части элемента, но по-прежнему в `<system.serviceModel>`добавьте `<bindings>` элемент со следующим содержимым:

    ```xml
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    Это содержимое определяет привязки, используемые в приложении. Можно определить несколько привязок, но в этом руководстве вы определяете только один.

    Предыдущий код определяет привязку ретранслятор WCF [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) с `relayClientAuthenticationType` `None`параметром. Этот параметр указывает, что для конечной точки, использующей эту привязку, не требуются учетные данные клиента.

1. После элемента `<bindings>` добавьте элемент `<services>`. Как и в случае с привязками, в одном файле конфигурации можно определить несколько служб. Но в этом учебнике определяется только одна.

    ```xml
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    Это содержимое настраивает службу, которая использует ранее определенное значение по `webHttpRelayBinding`умолчанию. Он также использует значение по `sbTokenProvider`умолчанию, которое определяется на следующем шаге.

1. После элемента создайте элемент со следующим содержимым, заменив `SAS_KEY` его на ключ подписи общего доступа (SAS). `<behaviors>` `<services>` Чтобы получить ключ SAS из [портал Azure][Azure portal], см. раздел [Получение учетных данных управления](service-bus-relay-tutorial.md#get-management-credentials).

    ```xml
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

1. По-прежнему в *файле App. config* `<appSettings>` в элементе замените все значение строки подключения на строку подключения, полученную ранее с портала.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```

1. Выберите **Сборка** > построить**решение** , чтобы построить все решение.

### <a name="example-that-implements-the-rest-based-wcf-service-contract"></a>Пример, реализующий контракт службы WCF на основе RESTFUL

В следующем коде показана реализация контракта и службы для службы на основе интерфейса RESTful, которая выполняется в служебной шине с `WebHttpRelayBinding` помощью привязки.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

В следующем примере показан файл *app. config* , связанный со службой.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app settings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY>"/>
    </appSettings>
</configuration>
```

## <a name="host-the-rest-based-wcf-service-to-use-azure-relay"></a>Размещение службы WCF на основе REST для использования Azure Relay

В этом разделе описывается запуск веб-службы с помощью консольного приложения с Ретранслятор WCF. Полный список кода, написанного в этом разделе, представлен в примере, приведенном ниже.

### <a name="to-create-a-base-address-for-the-service"></a>Создание базового адреса для службы

1. В объявлении функции `Main()` создайте переменную для хранения пространства имен своего проекта. Не забудьте заменить `yourNamespace` именем созданного ранее пространства имен ретранслятора.

    ```csharp
    string serviceNamespace = "yourNamespace";
    ```

    Пространство имен используется в служебной шине для создания уникального URI.

1. Создайте экземпляр `Uri` для базового адреса службы на основе пространства имен.

    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Создание и настройка узла веб-службы

По- `Main()`прежнему в создайте узел веб-службы, используя адрес URI, созданный ранее в этом разделе.
  
```csharp
WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
```

Узел службы представляет собой объект WCF, который создает экземпляры хост-приложения. Этот пример передает ему тип создаваемого узла, который является `ImageService`, а также адрес, по которому необходимо предоставить ведущее приложение.

### <a name="to-run-the-web-service-host"></a>Запуск узла веб-службы

1. По-прежнему добавьте следующую строку ,чтобыоткрытьслужбу.`Main()`

    ```csharp
    host.Open();
    ```

    Теперь служба запущена.

1. Отобразите сообщение о том, что служба запущена, и о том, как остановить ее.

    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. После завершения закройте узел службы.

    ```csharp
    host.Close();
    ```

### <a name="example-of-the-service-contract-and-implementation"></a>Пример контракта службы и реализации

В следующем примере показаны контракт и реализация службы, созданные на предыдущих шагах, а также размещение службы в консольном приложении. Скомпилируйте следующий код в исполняемый файл с именем *ImageListener. exe*.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

## <a name="run-and-test-the-service"></a>Запуск и тестирование службы

После построения решения выполните следующие действия, чтобы запустить приложение:

1. Выберите F5 или перейдите к расположению исполняемого файла *имажелистенер\бин\дебуг\имажелистенер.ЕКСЕ*, чтобы запустить службу. Не Заработайте приложение, так как оно требуется для следующего шага.
1. Скопируйте из командной строки адрес и вставьте его в браузере, чтобы увидеть изображение.
1. По завершении нажмите клавишу ВВОД в окне командной строки, чтобы закрыть приложение.

## <a name="next-steps"></a>Следующие шаги

Вы научились создавать приложение, которое использует службу ретранслятора Azure. Дополнительные сведения можно найти в следующих статьях:

* [Что такое ретранслятор Azure?](relay-what-is-it.md)
* [Предоставление доступа к локальной службе WCF RESTFUL внешнему клиенту с помощью Azure Ретранслятор WCF](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
