---
title: Фонд связи Windows Azure (WCF) Ретранслирует гибридное приложение на территории/облаке (.NET) Документы Майкрософт
description: Здесь вы узнаете, как предоставлять локальную службу WCF для веб-приложения в облаке с помощью Azure Relay
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 9ed02f7c-ebfb-4f39-9c97-b7dc15bcb4c1
ms.service: service-bus-relay
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: spelluru
ms.openlocfilehash: b86d535e4cbc275b3ee777d7c70146f7711c502c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212953"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>Предоставление локальной службы WCF для веб-приложения в облаке с помощью Azure Relay

В этой статье показано, как создать гибридное облачное приложение с помощью Microsoft Azure и Visual Studio. Вы создаете приложение, использующее несколько ресурсов Azure в облаке. Этот учебник поможет вам узнать:

* Как создать или адаптировать существующую веб-службу для использования веб-решением.
* Как использовать службу ретрансляции Фонда коммуникаций Windows (WCF) для обмена данными между приложением Azure и веб-службой, размещенным в другом месте.

В этом учебнике вы выполните следующие задачи:

> [!div class="checklist"]
>
> * Установка обязательных компонентов для работы с этим учебником.
> * Просмотр сценария.
> * Создание пространства имен.
> * Создайте штатный сервер.
> * Создайте приложение ASP .NET.
> * Запустите приложение на локальном компьютере.
> * Развертывание веб-приложения в Azure.
> * Запустите приложение на Azure.

## <a name="prerequisites"></a>Предварительные требования

Для работы с данным руководством вам потребуется:

* Подписка Azure. Если у вас его нет, [создайте бесплатную учетную запись,](https://azure.microsoft.com/free/) прежде чем начать.
* [Визуальная студия 2015 или позже](https://www.visualstudio.com). В примерах для этого учебника используется Visual Studio 2019.
* Пакет Azure SDK для .NET. Установите его со [страницы загрузки SDK](https://azure.microsoft.com/downloads/).

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Как ретранслятор Azure помогает работать с гибридными решениями

Бизнес-решения обычно состоят из комбинации пользовательского кода и существующей функциональности. Пользовательский код удовлетворяет новые и уникальные бизнес-требования. Решения и системы, которые уже существуют, обеспечивают существующую функциональность.

Архитекторы решений начинают использовать облако для упрощения реализации масштабирования и снижения эксплуатационных расходов. При этом они обнаруживают, что существующие сервисные активы, которые они хотели бы использовать в качестве строительных блоков для своих решений, находятся внутри корпоративного брандмауэра и находятся вне досягаемости облачного решения. Многие внутренние службы не построены или размещены таким образом, что они могут быть легко выставлены на краю корпоративной сети.

[Azure Relay](https://azure.microsoft.com/services/service-bus/) использует существующие веб-сервисы WCF и делает эти службы безопасными для решений, которые находятся за пределами корпоративного периметра, не требуя навязчивых изменений в корпоративной сетевой инфраструктуре. Такие службы ретрансляции по-прежнему размещаются внутри существующей среды, однако они делегируют функции прослушивания входящих сеансов и запросов размещенной в облаке службе ретрансляции. Ретранслятор Azure также защищает эти службы от несанкционированного доступа, применяя аутентификацию на основе [подписанного URL-адреса](../service-bus-messaging/service-bus-sas.md) (SAS).

## <a name="review-the-scenario"></a>Просмотр сценария

В этом руководстве вы создадите веб-сайт ASP.NET, который позволит просматривать список продуктов на странице складских запасов.

![Сценарий][0]

В учебнике предполагается, что у вас есть сведения о продуктах в существующей локальной системе, для доступа к которой используется ретранслятор Azure. Веб-сервис, который работает в простом приложении консоли, имитирует эту ситуацию. Он содержит набор продуктов в памяти. Вы можете запустить это приложение консоли на собственном компьютере и развернуть роль в Интернете в Azure. Поступая таким образом, вы увидите, как веб-роль, работая в центре обработки данных Azure, вызывается на ваш компьютер. Этот вызов происходит, даже если ваш компьютер почти наверняка будет позади по крайней мере один брандмауэр и сетевой адрес перевода (NAT) слой.

## <a name="set-up-the-development-environment"></a>Настройка среды разработки

Прежде чем начать разработку приложения для Azure, скачайте нужные инструменты и настройте среду разработки.

1. Установите пакет SDK Azure для .NET, скачав его с [этой страницы](https://azure.microsoft.com/downloads/).
1. В столбце **.NET** выберите используемую версию [Visual Studio.](https://www.visualstudio.com) Этот учебник использует Visual Studio 2019.
1. При попросении выполнения или сохранения установщика выберите **Run.**
1. В поле диалога **Web Platform Installer** выберите **установку** и продолжайте установку.

Как только установка закончена, у вас есть все необходимое, чтобы начать разработку приложения. В состав пакета SDK входят инструменты для эффективной разработки приложений Azure в Visual Studio.

## <a name="create-a-namespace"></a>Создание пространства имен

Первым шагом является создание пространства имен и получение ключа [общей подписи доступа (SAS).](../service-bus-messaging/service-bus-sas.md) Пространство имен определяет границы каждого приложения, предоставляемого через службу ретрансляции. Ключ SAS автоматически создается системой при создании пространства имен службы. Сочетание пространства имен и ключа SAS образует учетные данные, на основе которых Azure осуществляет аутентификацию доступа к приложению.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Создание локального сервера

Во-первых, вы создадите имитированную локальную систему каталогов продукции.  Этот проект запускается как консольное приложение Visual Studio, используя [пакет NuGet служебной шины Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) для включения библиотек и параметров конфигурации служебной шины. <a name="create-the-project"></a>

1. Запустите Microsoft Visual Studio от имени администратора. Для этого щелкните правой кнопкой мыши значок программы Visual Studio и выберите **Запустить от имени администратора**.
1. В Visual Studio выберите **Создать проект**.
1. При **создании нового проекта**выберите **консольное приложение (.NET Framework)** для C и выберите **Следующий**.
1. Назовите проект *ProductsServer* и выберите **Create**.

   ![Настройка нового проекта][11]

1. В **Solution Explorer**, правой кнопкой мыши проекта **ProductsServer,** а затем выберите Управление **NuGet пакеты**.
1. Нажмите **Обзор**, найдите и выберите **WindowsAzure.ServiceBus**. Нажмите **Установить** и примите условия использования.

   ![Установка пакета NuGet][13]

   На необходимые клиентские сборки теперь имеется ссылка.

1. Добавьте новый класс для контракта на свою продукцию. В **Solution Explorer**, правой кнопкой мыши проекта **ProductsServer** и выберите **Добавить** > **класс**.
1. В **имя,** введите имя *ProductsContract.cs* и выберите **Добавить**.

Внести следующие изменения в код в решение:

1. В *ProductsContract.cs* замените определение пространства имен на приведенный ниже код, определяющий контракт для службы.

    ```csharp
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;

        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }

        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();

        }

        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

1. В *Program.cs*заменить определение пространства имен следующим кодом, который добавляет службу профиля и хост для него.

    ```csharp
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;

        // Implement the IProducts interface.
        class ProductsService : IProducts
        {

            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };

            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }

        }

        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();

                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();

                sh.Close();
            }
        }
    }
    ```

1. В **обозревателе решений** дважды щелкните файл **App.config**, чтобы открыть его в редакторе Visual Studio. В нижней `<system.ServiceModel>` части элемента, `<system.ServiceModel>`но все еще в пределах, добавить следующий код XML. Не забудьте `yourServiceNamespace` заменить имя вашего `yourKey` пространства имен, и с ключом SAS вы получили ранее с портала:

    ```xml
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```

    > [!NOTE]
    > Ошибка, вызванная `transportClientEndpointBehavior` просто предупреждением и не является проблемой блокировки для этого примера.

1. Тем не менее в *App.config*, в элементе, `<appSettings>` заменить значение строки соединения с строкой соединения вы ранее получили от портала.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

1. Выберите ctrl-Shift-B или выберите решение **Build** > **Build,** чтобы построить приложение и проверить точность вашей работы до сих пор.

## <a name="create-an-aspnet-application"></a>Создание приложения ASP.NET

В этом разделе вы создадите простое приложение ASP.NET, которое будет отображать данные, полученные из службы продукции.

### <a name="create-the-project"></a>Создание проекта

1. Убедитесь, что Visual Studio работает в качестве администратора.
1. В Visual Studio выберите **Создать проект**.
1. При **создании нового проекта**выберите ASP.NET **веб-приложений (.NET Framework)** для C и выберите **Следующий**.
1. Назовите проект *ProductsPortal* и выберите **Создать**.
1. При **создании нового веб-приложения ASP.NET**выберите **MVC** и выберите **Изменение** под **аутентификацией.**

   ![Выбор веб-приложения ASP.NET][16]

1. В **изменении аутентификации**, выберите **Нет аутентификации,** то выберите **OK**. Для этого учебника вы развертываете приложение, которое не нуждается в вхески пользователя.

    ![Указание аутентификации][18]

1. Вернуться в **создание нового ASP.NET веб-приложений,** выберите **Создать** для создания приложения MVC.
1. Нанастройка ресурсов Azure для нового веб-приложения. Выполните последующие действия в [веб-приложении Publish.](../app-service/app-service-web-get-started-dotnet-framework.md#launch-the-publish-wizard) Затем вернитесь к этому учебнику и перейдите к следующему шагу.
1. В **Solution Explorer**, право-нажмите **Модели,** а затем выберите **Добавить** > **класс**.
1. Назовите класс *Product.cs,* затем выберите **Добавить**.

    ![Создание модели продукта][17]

### <a name="modify-the-web-application"></a>Изменение веб-приложения

1. В *Product.cs* файле visual Studio замените существующее определение пространства имен следующим кодом:

   ```csharp
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
       public class Product
       {
           public string Id { get; set; }
           public string Name { get; set; }
           public string Quantity { get; set; }
       }
    }
    ```

1. В **Solution Explorer**, расширить **контроллеры**, а затем дважды нажмите **HomeController.cs,** чтобы открыть файл в Visual Studio.
1. В файле *HomeController.cs* замените имеющееся определение пространства имен следующим кодом:

    ```csharp
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;

        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

1. В **Solution Explorer**, расширить **просмотры** > **Общие**, а затем дважды нажмите **_Layout.cshtml,** чтобы открыть файл в редакторе Visual Studio.
1. Измените все `My ASP.NET Application` случаи продуктов *Northwind Traders.*
1. `Home`Удалите, `About` `Contact` и ссылки. В следующем примере удалите выделенный код.

    ![Удаление созданных элементов списка][41]

1. В **Solution Explorer**, расширить **просмотры** > **Главная**, а затем дважды щелкните **Index.cshtml,** чтобы открыть файл в редакторе Visual Studio. Замените все содержимое файла следующим кодом:

   ```html
   @model IEnumerable<ProductsWeb.Models.Product>

   @{
            ViewBag.Title = "Index";
   }

   <h2>Prod Inventory</h2>

   <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.Name)
                 </th>
                 <th></th>
                 <th>
                     @Html.DisplayNameFor(model => model.Quantity)
                 </th>
             </tr>

   @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.Name)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Quantity)
                 </td>
             </tr>
   }

   </table>
   ```

1. Чтобы проверить точность вашей работы до сих пор, вы можете выбрать Ctrl-Shift-B для создания проекта.

### <a name="run-the-app-locally"></a>Локальный запуск приложения

Запустите приложение, чтобы убедиться, что оно работает.

1. **ProductsPortal** должен быть активным проектом. Нажмите правой кнопкой мыши название проекта в **Solution Explorer** и выберите **Set As Startup Project.**
1. В Visual Studio выберите F5.

Приложение должно начать выполняться в браузере.

![Веб-приложение][21]

## <a name="put-the-pieces-together"></a>Объединение элементов

Следующим шагом является подключение локального сервера продуктов к приложению ASP.NET.

1. Если он еще не открыт, в Visual Studio откройте созданный **проект ProductsPortal** в разделе [«Создание ASP.NET приложения».](#create-an-aspnet-application)
1. Подобно шагу в разделе [«Создание внутреннего сервера»,](#create-an-on-premises-server) добавьте пакет NuGet в ссылки проекта. В **Solution Explorer**, правой кнопкой мыши проекта **ProductsPortal,** а затем выберите Управление **NuGet пакеты**.
1. Выполните поиск по фразе *WindowsAzure.ServiceBus* и выберите элемент **WindowsAzure.ServiceBus**. Затем закончите установку и закройте этот диалоговый ящик.
1. В **Solution Explorer**, правой кнопкой мыши проекта **ProductsPortal,** а затем выберите **Добавить** > **существующий пункт**.
1. Перейдите к файлу *ProductsContract.cs* в консольном проекте **ProductsServer**. Выделите *ProductsContract.cs*. Выберите вниз стрелка рядом, чтобы **добавить,** а затем выбрать **Добавить в качестве ссылки.**

   ![Добавление в виде ссылки][24]

1. Теперь откройте файл *HomeController.cs* в редакторе Visual Studio и замените существующее определение пространства имен приведенным ниже кодом. Не забудьте `yourServiceNamespace` заменить имя вашего сервиса `yourKey` и ключом SAS. Этот код позволяет клиенту позвонить в дежурную службу, вернув результат вызова.

   ```csharp
   namespace ProductsWeb.Controllers
   {
       using System.Linq;
       using System.ServiceModel;
       using System.Web.Mvc;
       using Microsoft.ServiceBus;
       using Models;
       using ProductsServer;

       public class HomeController : Controller
       {
           // Declare the channel factory.
           static ChannelFactory<IProductsChannel> channelFactory;

           static HomeController()
           {
               // Create shared access signature token credentials for authentication.
               channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                   "sb://yourServiceNamespace.servicebus.windows.net/products");
               channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                   TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                       "RootManageSharedAccessKey", "yourKey") });
           }

           public ActionResult Index()
           {
               using (IProductsChannel channel = channelFactory.CreateChannel())
               {
                   // Return a view of the products inventory.
                   return this.View(from prod in channel.GetProducts()
                                    select
                                        new Product { Id = prod.Id, Name = prod.Name,
                                            Quantity = prod.Quantity });
               }
           }
       }
   }
   ```

1. В **Solution Explorer**, правой кнопкой мыши решения **ProductsPortal.** Убедитесь в том, чтобы право нажмите на решение, а не проекта. Выберите **Добавить** > **существующий проект**.
1. Перейдите к проекту **ProductsServer**, а затем дважды щелкните файл решения *ProductsServer.csproj*, чтобы добавить его.
1. **ProductsServer** должен быть запущен для отображения данных на **ProductsPortal.** В **Solution Explorer**, правой кнопкой мыши решение **ProductsPortal** и выберите **Свойства** для отображения **страниц свойств.**
1. Выберите**проект запуска** **Common Properties** > и выберите **несколько проектов запуска.** Убедитесь, что **ProductsServer** и **ProductsPortal** отображаются в этом порядке, и что **действие** для обоих **является запуском.**

      ![Несколько запускаемых проектов][25]

1. Выберите**зависимости проекта** **Общие свойства** > на левой стороне.
1. Для **проектов**выберите **ProductsPortal**. Установите флажок **ProductsServer**.

    ![Зависимости проектов][26]

1. Для **проектов**выберите **ProductsServer**. Убедитесь, что **ProductsPortal** не выбран, а затем выберите **OK,** чтобы сохранить изменения.

## <a name="run-the-project-locally"></a>Локальный запуск проекта

Чтобы протестировать приложение локально, в Visual Studio выберите F5. Сначала должен начаться наемный сервер **ProductsServer,** затем приложение **ProductsPortal** должно начаться в окне браузера. На этот раз вы сможете проследить получение данных списка складских запасов продукции из локальной системы службы продукции.

![Веб-приложение][10]

Выберите **Обновление** на странице **ProductsPortal.** При каждом обновлении страницы после вызова `GetProducts()` из **ProductsServer** в серверном приложении будет отображаться сообщение.

Закройте оба приложения, прежде чем перейти к следующему разделу.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Развертывание проекта ProductsPortal в веб-приложении Azure

Следующим шагом является переиздание Azure web-приложения **ProductsPortal** переднего конца:

1. В **Solution Explorer**, правой кнопкой мыши проекта **ProductsPortal,** и выберите **Опубликовать**. На странице **Publish** выберите **Publish**.

   > [!NOTE]
   > Если веб-проект **ProductsPortal** запустится после развертывания автоматически, в окне браузера может появиться сообщение об ошибке. Это ожидаемое поведение, и означает, что приложение **ProductsServer** еще не запущено.
   >

1. Копируйте URL развернутого веб-приложения. URL-адрес позже понадобится. Вы также можете получить этот URL-адрес из окна **активности службы приложений Azure** в Visual Studio:

   ![URL-адрес развернутого приложения][9]

1. Закройте окно браузера, чтобы остановить выполняющееся приложение.

<a name="set-productsportal-as-web-app"></a>Перед запуском приложения в облаке необходимо убедиться, что **ProductsPortal** запускается из Visual Studio в виде веб-приложения.

1. В Visual Studio, право йнюн нажатие проекта **ProductsPortal** и выберите **Свойства**.
1. Выберите **Интернет**. В **start Action**выберите **URL-адрес Start.** Введите URL-адрес для ранее развернутого `https://productsportal20190906122808.azurewebsites.net/`веб-приложения в этом примере.

    ![Начальный URL-адрес][27]

1. Выберите **файл** > **сохранить все**.
1. Выберите решение **Build** > **Rebuild.**

## <a name="run-the-application"></a>Выполнение приложения

Выберите F5 для создания и запуска приложения. Сначала должен начаться наемный сервер, который является консольным приложением **ProductsServer,** затем приложение **ProductsPortal** должно начаться в окне браузера, как показано здесь:

   ![Запуск веб-приложения в Azure][1]

В инвентаризации продукта перечислены данные, извлеченные из службы продуктов в предприимжении, и отображаются эти данные в веб-приложении. Проверьте URL-адрес. Приложение **ProductsPortal** должно запускаться в облаке как веб-приложение Azure.

   > [!IMPORTANT]
   > Консольное приложение **ProductsServer** должно запускаться с возможностью передачи данных в приложение **ProductsPortal**. Если браузер отображает ошибку, подождите еще несколько секунд, чтобы **ProductsServer** загрузил и отобразила следующее сообщение, а затем обновите браузер.
   >

В браузере освежите страницу **ProductsPortal.** При каждом обновлении страницы после вызова `GetProducts()` из **ProductsServer** в серверном приложении будет отображаться сообщение.

![Обновленные выходные данные][38]

## <a name="next-steps"></a>Дальнейшие действия

Перейдите к следующему руководству:

> [!div class="nextstepaction"]
>[Предоставление локальной службы WCF для клиента WCF за пределами сети](service-bus-relay-tutorial.md)

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app.png
[NuGet]: https://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/configure-productsserver.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/install-nuget-service-bus-productsserver.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/choose-web-application-template.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-class-productsportal.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/change-authentication.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/web-publish-activity.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally-no-content.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-existing-item-link.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/launch-app-as-web-app.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
