---
title: Предоставление локальной службы WCF на базе REST для внешнего клиента с помощью Ретранслятора WCF Azure | Документация Майкрософт
description: Создание клиентского приложения и приложения службы с помощью ретранслятора WCF.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 53dfd236-97f1-4778-b376-be91aa14b842
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: spelluru
ms.openlocfilehash: 4707e56a09c257c9e03e6db070083c81ffde07b6
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212901"
---
# <a name="expose-an-on-premises-wcf-rest-service-to-external-client-by-using-azure-wcf-relay"></a>Предоставление локальной службы WCF на базе REST для внешнего клиента с помощью Ретранслятора WCF Azure

В этом учебнике описано, как создать клиентское приложение Ретранслятор WCF и службу с помощью Azure Relay. Аналогичное руководство по [обмену сообщениями в служебной шине](../service-bus-messaging/service-bus-messaging-overview.md) представлено в статье [Начало работы с очередями служебной шины](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Работа с этим руководством позволяет понять, как создать клиентское приложение Ретранслятор WCF и службу. Как и исходные аналоги WCF, служба представляет собой конструкцию, которая предоставляет одну или несколько конечных точек. Каждая конечная точка предоставляет одну или несколько операций службы. Конечная точка службы задает адрес, по которому можно найти службу, привязку, содержащую сведения, которые клиент должен передавать службе, а также контракт, определяющий функциональность, предоставляемую службой клиентам. Основное отличие между WCF и ретранслятором WCF состоит в том, что в последнем конечная точка размещается в облаке, а не на локальном компьютере.

После работы с последовательностью разделов в этом руководстве у вас будет работающая служба. Кроме того, у вас есть клиент, который может вызывать операции службы. 

В этом руководстве рассматриваются следующие задачи.

> [!div class="checklist"]
>
> * Установите необходимые компоненты для работы с этим руководством.
> * Создание пространства имен ретранслятора.
> * Создайте контракт службы WCF.
> * Реализуйте контракт WCF.
> * Разместите и запустите службу WCF для регистрации в службе ретрансляции.
> * Создайте клиент WCF для контракта службы.
> * Настройте клиент WCF.
> * Реализуйте клиент WCF.
> * Запуск приложений.

## <a name="prerequisites"></a>Предварительные требования

Для работы с данным руководством вам потребуется:

* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начать работу.
* [Visual Studio 2015 или более поздней версии](https://www.visualstudio.com). В примерах этого руководства используется Visual Studio 2019.
* Пакет Azure SDK для .NET. Установите его со [страницы загрузки SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Создание пространства имен ретранслятора

Сначала необходимо создать пространство имен и получить ключ [подписанного URL-адреса (SAS)](../service-bus-messaging/service-bus-sas.md). Пространство имен определяет границы каждого приложения, предоставляемого через службу ретрансляции. Ключ SAS автоматически создается системой при создании пространства имен службы. Сочетание пространства имен и ключа SAS образует учетные данные, на основе которых Azure осуществляет аутентификацию доступа к приложению.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract"></a>Определение контракта службы WCF

В контракте службы указывается, какие операции поддерживает служба. Операции — это методы или функции веб-службы. Контракты создаются путем определения интерфейса C++, C# или Visual Basic. Каждый метод в интерфейсе соответствует определенной операции службы. К каждому интерфейсу должен быть применен атрибут [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute), а к каждой операции— атрибут [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute). Если метод в интерфейсе с атрибутом [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) не имеет атрибута [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) , этот метод не предоставляется. Код для выполнения задач приведен в примерах после описания последовательности действий. Более подробное обсуждение контрактов и служб см. в разделе [проектирование и реализация служб](/dotnet/framework/wcf/designing-and-implementing-services).

### <a name="create-a-relay-contract-with-an-interface"></a>Создание контракта ретранслятора с помощью интерфейса

1. Запустите Microsoft Visual Studio от имени администратора. Для этого щелкните правой кнопкой мыши значок программы Visual Studio и выберите **Запуск от имени администратора**.
1. В Visual Studio выберите **создать новый проект**.
1. В окне **Создание нового проекта**выберите **консольное приложение (.NET Framework)** для C# и нажмите кнопку **Далее**.
1. Присвойте проекту имя *EchoService* и выберите **создать**.

   ![Создание консольного приложения][2]

1. В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите **Управление пакетами NuGet**. В **диспетчере пакетов NuGet**выберите **Обзор**, а затем найдите и выберите **WindowsAzure. servicebus**. Выберите **установить**и примите условия использования.

    ![Пакет служебной шины][3]

   Этот пакет автоматически добавляет ссылки на библиотеки служебной шины и WCF `System.ServiceModel`. [System.ServiceModel](/dotnet/api/system.servicemodel) — это пространство имен, которое предоставляет программный доступ к основным функциям WCF. Служебная шина использует множество объектов и атрибутов WCF для определения контрактов службы.

1. Добавьте следующие `using` инструкции в начало *Program.CS*:

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Измените имя пространства имен с `EchoService` (имя по умолчанию) на `Microsoft.ServiceBus.Samples`.

   > [!IMPORTANT]
   > В этом руководстве используется C# пространство `Microsoft.ServiceBus.Samples` имен, которое является пространством имен управляемого типа на основе контракта, который используется в файле конфигурации в разделе [Настройка клиента WCF](#configure-the-wcf-client) . При построении этого образца можно указать любое требуемое пространство имен. Тем не менее, руководство не будет работать, если вы не измените пространства имен контракта и службы соответствующим образом в файле конфигурации приложения. Пространство имен, указанное в файле *app. config* , должно совпадать с пространством имен, указанным в C# файлах.
   >

1. Сразу после объявления пространства имен `Microsoft.ServiceBus.Samples`, но внутри этого пространства, определите новый интерфейс `IEchoContract` и примените к нему атрибут `ServiceContractAttribute` со значением `https://samples.microsoft.com/ServiceModel/Relay/`. Вставьте приведенный ниже фрагмент кода после объявления пространства имен.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    Значение пространства имен отличается от пространства имен, которое используется во всей области кода. Оно используется в качестве уникального идентификатора данного контракта. Явное указание пространства позволяет предотвратить добавление значение пространства имен по умолчанию к имени контракта.

   > [!NOTE]
   > Как правило, пространство имен контракта службы содержит схему именования, которая включает сведения о версии. Включение сведений о версии в пространство имен контракта службы позволяет службам выносить значительные изменения в новый контракт службы с новым пространством имен и предоставлять к нему доступ через новую конечную точку. Таким образом, клиенты могут продолжать использовать старый контракт службы без обновлений. Сведения о версии могут включать дату и номер сборки. Дополнительные сведения см. в статье [Управление версиями службы](/dotnet/framework/wcf/service-versioning). В этом руководстве схема именования пространства имен контракта службы не содержит сведений о версии.
   >

1. В интерфейсе `IEchoContract` объявите метод для отдельной операции, которую контракт `IEchoContract` предоставляет в интерфейсе. Затем примените атрибут `OperationContractAttribute` к методу, который требуется предоставить как часть общедоступного контракта ретранслятора WCF, следующим образом:

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```

1. Сразу после определения интерфейса `IEchoContract` объявите канал, наследующий от интерфейсов `IEchoContract` и `IClientChannel`, как показано здесь:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Канал представляет собой объект WCF, посредством которого служба и клиент обмениваются информацией. Позже вы напишете код по каналу, чтобы вывести информацию между двумя приложениями.

1. Выберите **Сборка** > **построить решение** или нажмите клавиши CTRL + SHIFT + B, чтобы убедиться в точности работы на данный момент.

### <a name="example-of-a-wcf-contract"></a>Пример контракта WCF

Ниже приведен код базового интерфейса, определяющего контракт ретранслятора WCF.

```csharp
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Теперь, когда интерфейс создан, можно его реализовать.

## <a name="implement-the-wcf-contract"></a>Реализация контракта WCF

Для создания ретранслятора Azure необходимо сначала создать контракт с помощью интерфейса. Дополнительные сведения о создании интерфейса см. в предыдущем разделе. Следующая процедура реализует интерфейс. Эта задача включает создание класса с именем `EchoService` , реализующего пользовательский `IEchoContract` интерфейс. После реализации интерфейса вы настроите интерфейс с помощью файла конфигурации *app. config* . Файл конфигурации содержит необходимые сведения для приложения. Эти сведения включают имя службы, имя контракта и тип протокола, используемого для связи со службой ретрансляции. Код, используемый для этих задач, приведен в примере, который следует процедуре. Более общее обсуждение реализации контракта службы см. в разделе [реализация контрактов служб](/dotnet/framework/wcf/implementing-service-contracts).

1. Создайте новый класс с именем `EchoService` сразу после определения интерфейса `IEchoContract`. Класс `EchoService` реализует интерфейс `IEchoContract`.

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    Как и в случае с другими реализациями интерфейсов, определение можно реализовать в другом файле. В этом учебнике реализация включается в тот же файл, что и определение интерфейса и метод `Main()`.

1. Примените атрибут [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) к интерфейсу `IEchoContract`. Атрибут определяет имя службы и пространство имен. После этого класс `EchoService` будет выглядеть так:

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. Реализуйте метод `Echo`, определенный в интерфейсе `IEchoContract`, в классе `EchoService`.

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. Выберите **Сборка** > **построить решение** или нажмите клавиши CTRL + SHIFT + B.

### <a name="define-the-configuration-for-the-service-host"></a>Определение конфигурации узла службы

Файл конфигурации аналогичен файлу конфигурации WCF. Он включает имя службы, конечную точку и привязку. Конечная точка — это расположение, Azure Relay которое предоставляет клиентам и узлам возможность взаимодействия друг с другом. Привязка — это тип протокола, который используется для обмена данными. Основное отличие заключается в том, что эта настроенная конечная точка службы ссылается на привязку [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) , которая не является частью .NET Framework. [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) — это одна из привязок, определяемых службой.

1. В **Обозреватель решений**дважды щелкните файл **app. config** , чтобы открыть его в редакторе Visual Studio.
1. В элементе `<appSettings>` замените местозаполнители именем пространства имен службы и ключом SAS, скопированным на предыдущем шаге.
1. Добавьте элемент `<services>` внутри тегов `<system.serviceModel>`. В отдельном файле конфигурации можно определить несколько приложений ретранслятора. Однако в данном учебнике определяется только одно приложение.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. Добавьте элемент `<service>` в элемент `<services>` для определения имени службы.

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. Внутри элемента `<service>` определите расположение контракта конечной точки, а также тип привязки для нее.

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    Конечная точка определяет расположение, в котором клиент будет искать хост-приложение. Результаты этого шага потребуются позднее для создания универсального кода ресурса (URI), который предоставляет полный доступ к узлу через ретранслятор Azure. Привязка объявляет, что мы используем TCP в качестве протокола для взаимодействия со службой ретрансляции.

1. Выберите **Сборка** > **построить решение** или нажмите клавиши CTRL + SHIFT + B, чтобы убедиться в точности работы на данный момент.

### <a name="example-of-implementation-of-a-service-contract"></a>Пример реализации контракта службы

Следующий код показывает реализацию контракта службы.

```csharp
[ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

В следующем коде показан базовый формат файла *app. config* , связанного с узлом службы.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-the-wcf-service-to-register-with-the-relay-service"></a>Размещение и запуск службы WCF для регистрации в службе ретрансляции

В этом разделе описывается запуск службы ретрансляции Azure.

### <a name="create-the-relay-credentials"></a>Создание учетных данных ретранслятора

1. Создайте в `Main()` две переменные для хранения пространства имен и ключа SAS, которые считываются из окна консоли.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    Ключ SAS потребуется позже для доступа к проекту. Пространство имен передается в качестве параметра в `CreateServiceUri` для создания универсального кода ресурса (URI) службы.

1. С помощью объекта [TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior) объявите, что вы будете использовать ключ SAS в качестве типа учетных данных. Добавьте следующий код сразу после кода, добавленного на предыдущем шаге:

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>Создание базового адреса для службы

После кода, добавленного в предыдущем разделе, создайте `Uri` экземпляр для базового адреса службы. Этот универсальный код ресурса (URI) указывает схему, пространство имен и путь к интерфейсу службы служебной шины.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

Значение «SB» — это аббревиатура для схемы служебной шины. Он указывает, что в качестве протокола используется протокол TCP. Эта схема также была ранее указана в файле конфигурации, когда [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) был указан в качестве привязки.

В этом учебнике используется универсальный код ресурса (URI) `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="create-and-configure-the-service-host"></a>Создание и настройка узла службы

1. По-прежнему `Main()`работаете в, задайте для `AutoDetect`режима подключения значение.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    Режим подключения описывает протокол, используемый службой для связи со службой ретрансляции (HTTP или TCP). При использовании параметра `AutoDetect`по умолчанию служба пытается подключиться к Azure Relay через TCP, если она доступна, и HTTP, если протокол TCP недоступен. Этот результат отличается от протокола, который служба указывает для связи с клиентом. Этот протокол определяется используемой привязкой. Например, служба может использовать привязку [BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding), которая указывает, что ее конечная точка связывается с клиентами по протоколу HTTP. Эта же служба может указать `ConnectivityMode.AutoDetect` , чтобы служба взаимодействовала с Azure Relay по протоколу TCP.

1. Создайте хост службы с помощью универсального кода ресурса (URI), созданного на предыдущем шаге.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    Хост службы представляет собой объект WCF, который создает экземпляры службы. Здесь вы передаете ему тип службы, которую вы хотите создать, `EchoService` тип, а также адрес, по которому будет предоставляться служба.

1. В верхней части файла *Program.CS* добавьте ссылки на [System. ServiceModel. Description](/dotnet/api/system.servicemodel.description) и [Microsoft. servicebus. Description](/dotnet/api/microsoft.servicebus.description).

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. Включите в `Main()` общий доступ к конечной точке.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Этот шаг информирует службу ретранслятора о том, что ваше приложение можно найти в общедоступном виде, изучив веб-канал Atom для своего проекта. Если задано `DiscoveryType` значение `private`, клиент по-прежнему может получить доступ к службе. Однако служба не будет отображаться при поиске в `Relay` пространстве имен. Клиенту потребуется знать путь к конечной точке.

1. Примените учетные данные службы к конечным точкам службы, определенным в файле *app. config* :

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Как упоминалось ранее, в файле конфигурации может быть объявлено несколько служб и конечных точек. Если вы это сделали, код пройдет по всему файлу конфигурации и найдет все конечные точки, к которым следует применить учетные данные. В этом руководстве файл конфигурации содержит только одну конечную точку.

### <a name="open-the-service-host"></a>Открытие узла службы

1. По-прежнему добавьте следующую строку ,чтобыоткрытьслужбу.`Main()`

    ```csharp
    host.Open();
    ```

1. Сообщите пользователю о том, что служба работает, и объясните, как ее отключить.

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. После завершения закройте узел службы.

    ```csharp
    host.Close();
    ```

1. Нажмите клавиши CTRL + SHIFT + B, чтобы выполнить сборку проекта.

### <a name="example-that-hosts-a-service-in-a-console-application"></a>Пример размещения службы в консольном приложении

Готовый код службы должен выглядеть так, как показано ниже. Он включает в себя контракт и реализацию службы, созданные в предыдущих шагах учебника. Служба размещается в консольном приложении.

```csharp
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         

            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Relay credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }

            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>Создание клиента WCF для контракта службы

Следующая задача — создать клиентское приложение и определить контракт службы, который будет реализован позже. Эти шаги похожи на шаги, используемые для создания службы: определение контракта, изменение файла *app. config* , использование учетных данных для подключения к службе ретранслятора и т. д. Код для выполнения этих задач приведен в примере после описания последовательности выполнения действий.

1. Создайте новый проект в текущем решении Visual Studio для клиента:

   1. В **Обозреватель решений**щелкните правой кнопкой мыши текущее решение (а не проект) и выберите команду **Добавить** > **Новый проект**.
   1. В окне **Добавление нового проекта**выберите **консольное приложение (.NET Framework)** для C#и нажмите кнопку **Далее**.
   1. Присвойте проекту имя *EchoClient* и выберите **создать**.

1. В **Обозреватель решений**в проекте **EchoClient** дважды щелкните **Program.CS** , чтобы открыть файл в редакторе, если он еще не открыт.
1. Измените имя пространства имен с `EchoClient` (имя по умолчанию) на `Microsoft.ServiceBus.Samples`.
1. Установите [пакет NuGet служебной шины](https://www.nuget.org/packages/WindowsAzure.ServiceBus):

   1. В **Обозреватель решений**щелкните правой кнопкой мыши **EchoClient** и выберите пункт **Управление пакетами NuGet**.
   1. Нажмите кнопку **Обзор**, найдите и выберите **WindowsAzure. servicebus**. Выберите **установить**и примите условия использования.

      ![Установка пакета служебной шины][4]

1. Добавьте инструкцию для пространства имен [System. ServiceModel](/dotnet/api/system.servicemodel) в файл *Program.cs.* `using`

    ```csharp
    using System.ServiceModel;
    ```

1. Добавьте определение контракта службы в пространство имен, как показано в следующем примере. Это определение идентично определению, используемому в проекте **службы** . Добавьте этот код в начало `Microsoft.ServiceBus.Samples` пространства имен.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Нажмите клавиши CTRL + SHIFT + B, чтобы создать клиент.

### <a name="example-of-the-echoclient-project"></a>Пример проекта EchoClient

В следующем коде показано текущее состояние файла *Program.CS* в проекте **EchoClient** .

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>Настройка клиента WCF

На этом шаге вы создадите файл *app. config* для базового клиентского приложения, которое обращается к службе, созданной ранее в этом учебнике. Этот файл *app. config* определяет контракт, привязку и имя конечной точки. Код для выполнения этих задач приведен в примере после описания последовательности выполнения действий.

1. В **Обозреватель решений**в проекте **EchoClient** дважды щелкните файл **app. config** , чтобы открыть его в редакторе Visual Studio.
1. В элементе `<appSettings>` замените местозаполнители именем пространства имен службы и ключом SAS, скопированным на предыдущем шаге.
1. Внутри элемента `system.serviceModel` добавьте элемент `<client>`.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Этот код объявляет, что вы определяете клиентское приложение в стиле WCF.

1. Определите имя, контракт и тип привязки для конечной точки в элементе `client`.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Этот код определяет имя конечной точки. Он также определяет контракт, определенный в службе, и тот факт, что клиентское приложение использует протокол TCP для взаимодействия с Azure Relay. На следующем шаге имя конечной точки используется для связывания ее конфигурации с универсальным кодом ресурса (URI) службы.

1. Выберите **файл** > **сохранить все**.

### <a name="example-of-the-appconfig-file"></a>Пример файла App. config

В следующем коде показан файл *app. config* для клиента Echo.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client"></a>Реализация клиента WCF

В этом разделе вы реализуете базовое клиентское приложение, которое обращается к службе, созданной ранее в этом учебнике. Как и в случае со службой, клиент выполняет множество тех же операций для доступа к Azure Relay:

* Задает режим подключения.
* Создает универсальный код ресурса (URI) расположения хоста службы.
* Определяет учетные данные для безопасного доступа.
* Применяет учетные данные к подключению.
* Открывает подключение.
* Выполняет специфические задачи приложения.
* Закрывает подключение.

Однако одно из основных отличий заключается в том, что клиентское приложение использует канал для подключения к службе ретрансляции. Служба использует вызов **ServiceHost**. Код для выполнения этих задач приведен в примере после описания последовательности выполнения действий.

### <a name="implement-a-client-application"></a>Реализация клиентского приложения

1. Установите режим подключения `AutoDetect`. Добавьте следующий код в метод `Main()` приложения **EchoClient**.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Определите переменные для хранения значений пространства имен службы и ключа SAS, которые считываются из консоли.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Создайте универсальный код ресурса (URI), определяющий расположение узла в проекте ретранслятора.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Создайте объект учетных данных для конечной точки пространства имен службы.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Создайте фабрику каналов, которая загружает конфигурацию, описанную в файле *app. config* .

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Фабрика каналов — это объект WCF, который создает канал взаимодействия между службой и клиентскими приложениями.

1. Примените учетные данные.

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Создайте и откройте канал к службе.

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Напишите простой пользовательский интерфейс и функциональность для Echo.

    ```csharp
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Код использует экземпляр объекта Channel в качестве прокси-сервера для службы.

1. Закройте канал и фабрику.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

### <a name="example-code-for-this-tutorial"></a>Пример кода для этого руководства

Готовый код должен выглядеть следующим образом. В этом коде показано, как создать клиентское приложение, как вызывать операции службы и как закрыть клиент после завершения вызова операции.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;


            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="run-the-applications"></a>Запуск приложений

1. Нажмите клавиши CTRL + SHIFT + B, чтобы построить решение. Это действие создает клиентский проект и проект службы, созданные на предыдущих шагах.
1. Прежде чем запустить клиентское приложение, убедитесь, что приложение службы работает. В **Обозреватель решений**щелкните правой кнопкой мыши решение **EchoService** и выберите пункт **Свойства**.
1. На **страницах свойств**, запустите**проект**" **Общие свойства** > " и выберите **Несколько запускаемых проектов**. Убедитесь, что проект **EchoService** отображается первым в списке.
1. В поле **Действие** для проектов **EchoService** и **EchoClient** выберите значение **Запуск**.

    ![Страницы свойств проекта][5]

1. Выберите **зависимости проекта**. В списке **проекты**выберите **EchoClient**. Для **зависит от**убедитесь, что выбран параметр **EchoService** .

    ![Зависимости проектов][6]

1. Нажмите кнопку **ОК** , чтобы закрыть **страницы свойств**.
1. Нажмите клавишу F5, чтобы запустить оба проекта.
1. Откроются два окна консоли с предложением указать пространство имен. Сначала необходимо запустить службу, поэтому в окне консоли **EchoService** введите пространство имен, а затем нажмите клавишу ВВОД.
1. Затем консоль запросит у вас ключ SAS. Введите ключ SAS и нажмите клавишу ВВОД.

    В окне консоли вы увидите примерно следующее. Эти значения являются просто примерами.

    `Your Service Namespace: myNamespace`

    `Your SAS Key: <SAS key value>`

    Приложение службы выведет в окне консоли адрес, который оно прослушивает (см. следующий пример).

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`

    `Press [Enter] to exit`

1. В окне консоли **EchoClient** введите данные, которые использовались для приложения службы. Введите одно и то же пространство имен службы и значения ключа SAS для клиентского приложения.
1. После ввода значений клиент открывает канал к службе и предлагает ввести некоторый текст, как показано в следующем примере.

    `Enter text to echo (or [Enter] to exit):`

    Введите текст для отправки в приложение службы и нажмите клавишу ВВОД. Этот текст отправляется службе посредством операции службы Echo и отображается в окне консоли службы, как показано ниже.

    `Echoing: My sample text`

    Результат операции `Echo` (исходный текст) возвращается в клиентское приложение и отображается в окне консоли. Следующий текст — пример выходных данных из окна консоли клиента.

    `Server echoed: My sample text`

1. Вы можете отправить еще несколько текстовых сообщений от клиента к службе. Когда все будет готово, нажмите кнопку Ввод в окне консоли клиента и службы, чтобы завершить работу обоих приложений.

## <a name="next-steps"></a>Следующие шаги

Перейдите к следующему руководству:

> [!div class="nextstepaction"]
>[Предоставление локальной службы WCF REST для клиента за пределами сети](service-bus-relay-rest-tutorial.md)

[2]: ./media/service-bus-relay-tutorial/configure-echoservice-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget-service-bus.png
[4]: ./media/service-bus-relay-tutorial/install-nuget-service-bus-client.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
