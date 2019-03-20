---
title: Гибридные локальные и облачные приложения ретранслятора WCF Azure (.NET) | Документация Майкрософт
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
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 145960db27247a8535eb96640000b86d810619c0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57838414"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>Предоставление локальной службы WCF для веб-приложения в облаке с помощью Azure Relay 
В этой статье показано, как создать гибридное облачное приложение с помощью Microsoft Azure и Visual Studio. Вы создадите приложение, которое использует несколько ресурсов Azure и выполняется в облаке.

* Как создать или адаптировать существующую веб-службу для использования веб-решением.
* Как использовать службу ретранслятора WCF Azure для обмена данными между приложением Azure и веб-службой, которая размещается в другом месте.

При работе с этим руководством вы выполните следующие задачи:

> [!div class="checklist"]
> * Просмотр сценария.
> * Создание пространства имен.
> * Создание локального сервера
> * Создание приложения ASP .NET.
> * Запустите приложение на локальном компьютере.
> * Развертывание веб-приложения в Azure.
> * Запуск приложения в Azure.

## <a name="prerequisites"></a>Технические условия

Для работы с данным руководством вам потребуется:

- Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начать работу.
- [Visual Studio 2015 или более поздней версии](https://www.visualstudio.com). В описанных в этом руководстве примерах используется Visual Studio 2017.
- Пакет Azure SDK для .NET. Установите его со [страницы загрузки SDK](https://azure.microsoft.com/downloads/).

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Как ретранслятор Azure помогает работать с гибридными решениями
Бизнес-решения обычно состоят из сочетания пользовательского кода, написанного для удовлетворения новых и уникальных бизнес-требований, с существующими функциональными возможностями, предоставляемыми уже используемыми решениями и системами.

Архитекторы решений начинают использовать облако для упрощения реализации масштабирования и снижения эксплуатационных расходов. При этом они обнаруживают,что существующие активы служб, которые они хотели бы использовать в качестве стандартных блоков для своих решений, находятся за корпоративным брандмауэром, поэтому обращение к ним из облачного решения затруднено. Многие внутренние службы построены или размещены таким образом, который не позволяет легко предоставлять их на границе корпоративной сети.

[Ретранслятор Azure](https://azure.microsoft.com/services/service-bus/) предназначен для случаев, когда существующие веб-службы Windows Communication Foundation (WCF) безопасно предоставляются для решений, находящихся вне периметра корпоративной сети, без внесения существенных изменений в инфраструктуру корпоративной сети. Такие службы ретрансляции по-прежнему размещаются внутри существующей среды, однако они делегируют функции прослушивания входящих сеансов и запросов размещенной в облаке службе ретрансляции. Ретранслятор Azure также защищает эти службы от несанкционированного доступа, применяя аутентификацию на основе [подписанного URL-адреса](../service-bus-messaging/service-bus-sas.md) (SAS).

## <a name="review-the-scenario"></a>Просмотр сценария
В этом руководстве вы создадите веб-сайт ASP.NET, который позволит просматривать список продуктов на странице складских запасов.

![Сценарий][0]

В учебнике предполагается, что у вас есть сведения о продуктах в существующей локальной системе, для доступа к которой используется ретранслятор Azure. Это имитируется веб-службой, запущенной в простом консольном приложении и поддерживаемой набором продуктов в памяти. Можно запустить это консольное приложение на своем компьютере и развернуть веб-роль в Azure. Таким образом вы увидите, как веб-роль, выполняемая в центре обработки данных Azure, действительно вызывает ваш компьютер, даже если он (почти наверняка) будет находиться хотя бы за одним брандмауэром и слоем преобразования сетевых адресов (NAT).

## <a name="set-up-the-development-environment"></a>Настройка среды разработки

Прежде чем начать разработку приложения для Azure, скачайте нужные инструменты и настройте среду разработки.

1. Установите пакет SDK Azure для .NET, скачав его с [этой страницы](https://azure.microsoft.com/downloads/).
2. В столбце **.NET** щелкните ссылку, соответствующую используемой версии [Visual Studio](https://www.visualstudio.com). На описанных в этом руководстве шагах используется Visual Studio 2017.
3. При появлении запроса на выполнение или сохранение файла установки щелкните **Выполнить**.
4. В **установщике веб-платформы** щелкните **Установить**, чтобы продолжить.
5. После завершения установки у вас будут все компоненты, необходимые для начала разработки приложения. В состав пакета SDK входят инструменты для эффективной разработки приложений Azure в Visual Studio.

## <a name="create-a-namespace"></a>Создание пространства имен
Сначала необходимо создать пространство имен и получить ключ [подписанного URL-адреса (SAS)](../service-bus-messaging/service-bus-sas.md). Пространство имен определяет границы каждого приложения, предоставляемого через службу ретрансляции. Ключ SAS автоматически создается системой при создании пространства имен службы. Сочетание пространства имен и ключа SAS образует учетные данные, на основе которых Azure осуществляет аутентификацию доступа к приложению.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Создание локального сервера
Во-первых, вы создадите имитированную локальную систему каталогов продукции.  Этот проект запускается как консольное приложение Visual Studio, используя [пакет NuGet служебной шины Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) для включения библиотек и параметров конфигурации служебной шины.

### <a name="create-the-project"></a>Создание проекта
1. Запустите Microsoft Visual Studio, используя привилегии администратора. Для этого щелкните правой кнопкой мыши значок программы Visual Studio и выберите **Запустить от имени администратора**.
2. В меню **Файл** Visual Studio выберите **Создать**, а затем — **Проект**.
3. В разделе **Visual C#** области **Установленные шаблоны** щелкните **Консольное приложение (.NET Framework)**. В поле **Имя** введите **ProductsServer**.

   ![Диалоговое окно "Новый проект"][11]
4. Нажмите кнопку **ОК**, чтобы создать проект **ProductsServer**.
5. Если диспетчер пакетов NuGet для Visual Studio уже установлен, пропустите следующий шаг. В противном случае посетите сайт [NuGet][NuGet] и щелкните [Install NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) (Установить NuGet). Следуйте инструкциям на экране для установки диспетчера пакетов NuGet, а затем перезапустите Visual Studio.
6. В обозревателе решений щелкните правой кнопкой мыши проект **ProductsServer** и выберите пункт **Управление пакетами NuGet**.
7. Щелкните вкладку **Обзор**, а затем найдите **WindowsAzure.ServiceBus**. Выберите пакет **WindowsAzure.ServiceBus**.
8. Щелкните **Установить**и примите условия использования.

   ![Установка пакета NuGet][13]

   На необходимые клиентские сборки теперь имеется ссылка.
8. Добавьте новый класс для контракта на свою продукцию. В обозревателе решений щелкните правой кнопкой проект **ProductsServer**, а затем выберите **Добавить** и **Класс**.
9. В поле **Имя** введите **ProductsContract.cs**. Нажмите кнопку **Добавить**.
10. В **ProductsContract.cs** замените определение пространства имен на приведенный ниже код, определяющий контракт для службы.

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
11. В Program.cs замените определение пространства имен на следующий код, добавляющий службу профилей и узел для нее.

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
12. В обозревателе решений дважды щелкните файл **App.config**, чтобы открыть его в редакторе Visual Studio. В нижней части `<system.ServiceModel>` элемента (но не выходя за рамки `<system.ServiceModel>`), добавьте следующий код XML: Не забудьте заменить yourServiceNamespace именем пространства имен, а *yourKey* — ключом SAS, полученным ранее на портале.

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
    Ошибка, вызванная transportClientEndpointBehavior, является только предупреждением и не блокирует выполнение этого примера.
    
13. В файле App.config в элементе `<appSettings>` замените значение строки подключения значением, полученным ранее на портале.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```
14. Нажмите клавиши **CTRL+SHIFT+B** или в меню **Сборка** щелкните **Собрать решение**, чтобы создать приложение и проверить результат.

## <a name="create-an-aspnet-application"></a>Создание приложения ASP.NET

В этом разделе вы создадите простое приложение ASP.NET, которое будет отображать данные, полученные из службы продукции.

### <a name="create-the-project"></a>Создание проекта

1. Убедитесь, что система Visual Studio запущена с правами администратора.
2. В меню **Файл** Visual Studio выберите **Создать**, а затем — **Проект**.
3. В разделе **Visual C#** области **Установленные шаблоны** щелкните **Веб-приложение ASP.NET (.NET Framework)**. Присвойте проекту имя **ProductsPortal**. Нажмите кнопку **ОК**.

   ![Диалоговое окно "Новый проект"][15]

4. В списке **шаблонов ASP.NET** диалогового окна **Создание веб-приложения ASP.NET** выберите **MVC**.

   ![Выбор веб-приложения ASP.NET][16]

6. Нажмите кнопку **Изменить способ проверки подлинности**. В диалоговом окне **Изменить способ проверки подлинности** щелкните **Без проверки подлинности** и нажмите кнопку **ОК**. В этом руководстве развертывается приложение, для которого не требуется имя пользователя.

    ![Указание аутентификации][18]

7. Вернувшись к диалоговому окну **Создание веб-приложения ASP.NET**, нажмите кнопку **ОК**, чтобы создать приложение MVC.
8. Теперь необходимо настроить ресурсы Azure для нового веб-приложения. Следуйте указаниям в разделе [Публикация в Azure](../app-service/app-service-web-get-started-dotnet-framework.md#launch-the-publish-wizard). Затем вернитесь к этому учебнику и перейдите к следующему шагу.
10. В обозревателе решений щелкните правой кнопкой мыши **Модели**, а затем выберите **Добавить** и **Класс**. В поле **Имя** введите **Product.cs**. Нажмите кнопку **Добавить**.

    ![Создание модели продукта][17]

### <a name="modify-the-web-application"></a>Изменение веб-приложения

1. В файле Product.cs в Visual Studio замените существующее определение пространства имен на следующий код:

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
2. В обозревателе решений разверните папку **Контроллеры** и дважды щелкните файл **HomeController.cs**, чтобы открыть его в Visual Studio.
3. В файле **HomeController.cs** замените имеющееся определение пространства имен следующим кодом:

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
4. В обозревателе решений разверните папку Views\Shared, а затем дважды щелкните файл **_Layout.cshtml**, чтобы открыть его в редакторе Visual Studio.
5. Замените все вхождения элемента **My ASP.NET Application** (Мое приложение ASP.NET) текстом **Northwind Traders Products** (Продукты Northwind Traders).
6. Удалите ссылки **Главная**, **О программе** и **Контакт**. В следующем примере удалите выделенный код.

    ![Удаление созданных элементов списка][41]

7. В обозревателе решений разверните папку Views\Home, а затем дважды щелкните файл **Index.cshtml**, чтобы открыть его в редакторе Visual Studio. Замените все содержимое файла следующим кодом:

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
8. Чтобы проверить текущий результат, нажмите клавиши **CTRL+SHIFT+B** для сборки проекта.

### <a name="run-the-app-locally"></a>Локальный запуск приложения

Запустите приложение, чтобы убедиться, что оно работает.

1. **ProductsPortal** должен быть активным проектом. В обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите пункт **Назначить запускаемым проектом**.
2. В Visual Studio нажмите клавишу **F5**.
3. Приложение должно начать выполняться в браузере.

   ![Веб-приложение][21]

## <a name="put-the-pieces-together"></a>Объединение элементов

Следующим шагом является подключение локального сервера продуктов к приложению ASP.NET.

1. В Visual Studio повторно откройте проект **ProductsPortal**, созданный в разделе [Создание приложения ASP.NET](#create-an-aspnet-application).
2. Аналогично шагу в разделе "Создание локального сервера" добавьте пакет NuGet в ссылки проекта. В обозревателе решений щелкните правой кнопкой мыши проект **ProductsPortal** и выберите пункт **Управление пакетами NuGet**.
3. Выполните поиск по фразе **WindowsAzure.ServiceBus** и выберите элемент **WindowsAzure.ServiceBus**. Завершите установку и закройте это диалоговое окно.
4. В обозревателе решений щелкните правой кнопкой мыши проект **ProductsPortal** и выберите пункт **Добавить**, а затем — **Существующий элемент**.
5. Перейдите к файлу **ProductsContract.cs** в консольном проекте **ProductsServer**. Щелкните, чтобы выделить ProductsContract.cs. Щелкните стрелку вниз рядом с пунктом **Добавить**, затем нажмите кнопку **Добавить как связь**.

   ![Добавление в виде ссылки][24]

6. Теперь откройте **HomeController.cs** в редакторе Visual Studio и замените определение пространства имен следующим кодом: Замените yourServiceNamespace на имя пространства имен вашей службы, а *yourKey* — на ключ SAS. Это позволит клиенту вызывать локальную службу, возвращая результат вызова.

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
7. В обозревателе решений щелкните правой кнопкой мыши решение **ProductsPortal** (не проект). Щелкните **Добавить**, а затем — **Существующий проект**.
8. Перейдите к проекту **ProductsServer**, а затем дважды щелкните файл решения **ProductsServer.csproj**, чтобы добавить его.
9. Чтобы данные отобразились в решении **ProductsPortal**, нужно запустить проект **ProductsServer**. В обозревателе решений щелкните правой кнопкой мыши решение **ProductsPortal** и выберите пункт **Свойства**. Откроется диалоговое окно **Страницы свойств**.
10. В левой части окна выберите **Запускаемый проект**. В правой части окна выберите **Несколько запускаемых проектов**. **ProductsServer** и **ProductsPortal** должны отображаться в указанном порядке с назначенным действием **Запустить**.

      ![Несколько запускаемых проектов][25]

11. В левой части диалогового окна **Свойства** щелкните **Зависимости проектов**.
12. В списке **Проекты** выберите **ProductsServer**. Снимите флажок **ProductsPortal**.
13. В списке **Проекты** выберите **ProductsPortal**. Установите флажок **ProductsServer**.

    ![Зависимости проектов][26]

14. В диалоговом окне **Страницы свойств** нажмите кнопку **ОК**.

## <a name="run-the-project-locally"></a>Локальный запуск проекта

Чтобы проверить приложение локально, в окне Visual Studio нажмите клавишу **F5**. Первым должен запуститься локальный сервер (**ProductsServer**), а затем в окне браузера должно запуститься приложение **ProductsPortal**. На этот раз вы сможете проследить получение данных списка складских запасов продукции из локальной системы службы продукции.

![Веб-приложение][10]

На странице **ProductsPortal** нажмите кнопку **Обновить**. При каждом обновлении страницы после вызова `GetProducts()` из **ProductsServer** в серверном приложении будет отображаться сообщение.

Прежде чем перейти к следующему шагу, закройте оба приложения.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Развертывание проекта ProductsPortal в веб-приложении Azure

Следующий шаг — повторная публикация внешнего интерфейса **ProductsPortal** веб-приложения Azure. Выполните следующее:

1. В обозревателе решений щелкните правой кнопкой мыши проект **ProductsPortal** и выберите **Опубликовать**. Затем на странице **Публикация** щелкните **Опубликовать**.

   > [!NOTE]
   > Если веб-проект **ProductsPortal** запустится после развертывания автоматически, в окне браузера может появиться сообщение об ошибке. Это ожидаемое поведение, и означает, что приложение **ProductsServer** еще не запущено.
   >
   >

2. Скопируйте URL-адрес развернутого веб-приложения, так как он понадобится для выполнения следующего шага. Кроме того, этот URL-адрес можно найти в окне "Действие службы приложений Azure" в Visual Studio.

   ![URL-адрес развернутого приложения][9]

3. Закройте окно браузера, чтобы остановить выполняющееся приложение.

### <a name="set-productsportal-as-web-app"></a>Настройка ProductsPortal в качестве веб-приложения

Перед запуском приложения в облаке необходимо, чтобы проект **ProductsPortal** в Visual Studio запускался как веб-приложение.

1. В Visual Studio щелкните правой кнопкой мыши проект **ProductsPortal** и выберите пункт **Свойства**.
2. В столбце слева щелкните **Веб**.
3. В разделе **Действие при запуске** нажмите кнопку **Начальный URL-адрес** и введите в текстовое поле URL-адрес развернутого ранее веб-приложения, например `http://productsportal1234567890.azurewebsites.net/`.

    ![Начальный URL-адрес][27]

4. В меню **Файл** Visual Studio выберите **Сохранить все**.
5. В Visual Studio в меню "Сборка" выберите **Пересобрать решение**.

## <a name="run-the-application"></a>Выполнение приложения

1. Нажмите клавишу F5, чтобы создать и запустить приложение. На локальном сервере ( **ProductsServer** консольное приложение) должен начинаться во-первых, а затем **ProductsPortal** должно запуститься приложение в окне браузера, как показано на следующем снимке экрана: Обратите внимание, что в списке складских запасов продукции содержатся данные, полученные из локальной системы службы продукции, и они отображаются в веб-приложении. Проверьте URL-адрес. Приложение **ProductsPortal** должно запускаться в облаке как веб-приложение Azure.

   ![Запуск веб-приложения в Azure][1]

   > [!IMPORTANT]
   > Консольное приложение **ProductsServer** должно запускаться с возможностью передачи данных в приложение **ProductsPortal**. Если в браузере появляется сообщение об ошибке, подождите несколько секунд, пока **ProductsServer** загрузит и отобразит следующее сообщение. Затем нажмите кнопку **Обновить** в браузере.
   >
   >

   ![Выходные данные с сервера][37]
2. В браузере на странице **ProductsPortal** нажмите кнопку **Обновить**. При каждом обновлении страницы после вызова `GetProducts()` из **ProductsServer** в серверном приложении будет отображаться сообщение.

    ![Обновленные выходные данные][38]

## <a name="next-steps"></a>Дальнейшие действия
Перейдите к следующему руководству: 

> [!div class="nextstepaction"]
>[Предоставление локальной службы WCF для клиента WCF за пределами сети](service-bus-relay-tutorial.md)

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[NuGet]: https://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
