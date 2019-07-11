---
title: Руководство по созданию приложения на C# для службы "Поиск Azure"
description: В руководстве даны пошаговые инструкции по созданию приложения для службы "Поиск Azure". В нем описывается весь процесс создания приложения, а также приводится ссылка на готовое приложение в репозитории GitHub. Познакомьтесь с основными компонентами службы "Поиск Azure".
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 05/01/2019
ms.openlocfilehash: d569437a3e6f6f05ddb9c6fa85f62c77ac51f72b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443815"
---
# <a name="c-tutorial-create-your-first-app---azure-search"></a>Руководство для разработчиков C#. Создание приложения для службы "Поиск Azure"

Узнайте, как создать веб-интерфейс для отправки запросов к индексу службы "Поиск Azure" и отображения полученных результатов. В руководстве предполагается наличие у вас готового и размещенного индекса, что позволит сосредоточиться на создании страницы поиска. Индекс содержит вымышленные данные об отелях. Создав базовую страницу, вы сможете в дальнейшем разбить ее на страницы, добавить функции фасетного поиска, автозаполнения и отображения поисковых подсказок, выполнив инструкции из последующих руководств.

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * настройка среды разработки;
> * моделирование структур данных;
> * создание веб-страницы;
> * определение методов;
> * Тестирование приложения

Вы также узнаете, насколько простым является вызов поиска. Ключевые инструкции в создаваемом вами коде заключены в следующие несколько строк.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

Этот вызов запускает поиск по данным в Azure и возвращает результаты.

![Поиск по слову pool](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется следующее:

[Установите Visual Studio](https://visualstudio.microsoft.com/), чтобы использовать решение в качестве интегрированной среды разработки.

### <a name="install-and-run-the-project-from-github"></a>Установка и запуск проекта из GitHub

1. Найдите пример приложения на GitHub: [Создание первого приложения](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Выберите действие **Clone or download** (Клонировать или скачать), чтобы создать частную локальную копию этого проекта.
1. В Visual Studio откройте базовую страницу поиска этого решения и выберите **Запуск без отладки** (или нажмите клавишу F5).
1. Выполните поиск по нескольким словам (например, wifi, view, bar, parking) и изучите результаты.

    ![Поиск по слову wifi](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

Мы надеемся, что проблем с проектом не возникло и приложение Azure успешно работает. В этом приложении присутствует ряд основных компонентов расширенных поисковых функций, поэтому рекомендуется последовательно выполнить все инструкции из этого руководства.

Чтобы самостоятельно воссоздать этот проект и усвоить сведения о компонентах Поиска Azure, выполните следующие действия.

## <a name="set-up-a-development-environment"></a>Настройка среды разработки

1. В Visual Studio 2017 или более поздней версии выберите **Новый проект**, а затем — **Веб-приложение ASP.NET Core**. Присвойте проекту имя, например FirstAzureSearchApp.

    ![Создание облачного проекта](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. Когда вы щелкнете **ОК**, вам будет предложен второй набор параметров для проекта этого типа. Выберите **Веб-приложение (модель-представление-контроллер)** .

    ![Создание проекта MVC](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. Далее в меню **Средства** выберите **Диспетчер пакетов NuGet**, а затем **Manage NuGet Packages for Solution...** (Управление пакетами NuGet для решения...). Нам нужно установить один пакет. На вкладке **Обзор** введите в поле поиска "Azure Search" (Поиск Azure). В результатах поиска выберите **Microsoft.Azure.Search** (версии 9.0.1 или более поздней) и установите эту службу. Для завершения установки нужно указать сведения в еще нескольких диалоговых окнах.

    ![Добавление библиотек Azure с помощью NuGet](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-search"></a>Инициализация Поиска Azure

В этом примере мы используем общедоступные данные об отелях. Это произвольный набор из 50 вымышленных отелей с именами и описаниями, который создан исключительно для предоставления демонстрационных данных. Для доступа к этим данным следует указать имя и ключ.

1. Откройте файл appsettings.json в новом проекте и замените строки, настроенные по умолчанию, следующими значениями имени и ключа. Представленный здесь ключ API — это не произвольный пример ключа, а _именно тот_ ключ, который необходим для доступа к данным об отелях. Теперь файл appsettings.json будет выглядеть следующим образом:

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. Мы еще не закончили работу с этим файлом. Выберите его свойства и укажите для параметра **Копировать в выходной каталог** значение **Копировать, если новее**.

    ![Копирование параметров приложения в выходные данные](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>Моделирование структур данных

Модели (классы C#) используются для обмена данными между клиентом (представление), сервером (контроллер) и облаком Azure с использованием архитектуры MVC (модель, представление и контроллер). Обычно эти модели отражают структуру конкретных данных, к которым выполняется доступ. Кроме того, нам нужна модель для поддержки взаимодействия между представлением и контроллером.

1. Откройте папку **Models** в обозревателе решений. Здесь вы увидите одну стандартную модель: **ErrorViewModel.cs**.

2. Щелкните правой кнопкой мыши папку **Models** и выберите **Добавить**, а затем — **Новый элемент**. В появившемся диалоговом окне выберите **ASP.NET Core** и первый элемент **Class** (Класс). Присвойте CS-файлу имя Hotel.cs и щелкните **Добавить**. Замените содержимое файла Hotel.cs кодом, приведенным ниже. Обратите внимание, что члены этого класса, поля **Address** и **Room**, сами являются классами, поэтому для них нам тоже нужны модели.

    ```cs
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnLucene)]
            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrLucene)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Category { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public bool? ParkingIncluded { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public double? Rating { get; set; }

            public Address Address { get; set; }

            [IsFilterable, IsSortable]
            public GeographyPoint Location { get; set; }

            public Room[] Rooms { get; set; }
        }
    }
    ```

3. Повторите тот же процесс создания модели для класса **Address**, поместив его в файл Address.cs. Замените содержимое этого файла следующим кодом.

    ```cs
    using Microsoft.Azure.Search;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

4. И еще раз повторите процедуру для класса **Room**, используя имя файла Room.cs. Замените содержимое этого файла следующим кодом.

    ```cs
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]

            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string Type { get; set; }

            [IsFilterable, IsFacetable]
            public double? BaseRate { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string BedOptions { get; set; }

            [IsFilterable, IsFacetable]

            public int SleepsCount { get; set; }

            [IsFilterable, IsFacetable]
            public bool? SmokingAllowed { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }
        }
    }
    ```

5. Набор классов **Hotel**, **Address** и **Room** именуется в Azure [_сложным типом_](search-howto-complex-data-types.md). Это очень важная концепция в Поиске Azure. Сложные типы могут иметь много уровней вложенности, классов и подклассов, поддерживая намного более сложные структуры данных, чем _простые типы_ (классы, содержащие только примитивные члены). Нам потребуется еще одна модель, поэтому снова повторите процесс создания класса модели, присвойте файлу имя SearchData.cs и замените созданный по умолчанию код следующим фрагментом.

    ```cs
    using Microsoft.Azure.Search.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public DocumentSearchResult<Hotel> resultList;
        }
    }
    ```

    Этот класс содержит введенные пользователем данные (**searchText**) и выходные данные поиска (**resultList**). Тип выходных данных (**DocumentSearchResult&lt;Hotel&gt;** ) критически важен, так как этот тип точно соответствует результатам поиска, и эту ссылку необходимо передать в представление.



## <a name="create-a-web-page"></a>Создание веб-страницы

Созданный проект по умолчанию создаст несколько клиентских представлений. Точный состав представлений зависит от используемой версии платформы .NET Core (в нашем примере это 2.1). Все они находятся в папке проекта **Views**. Вам придется внести изменения только в файл Index.cshtml (в папке **Views/Home**).

Полностью удалите содержимое файла Index.cshtml и создайте его заново, выполнив следующие действия.

1. В этом представлении мы применим два небольших изображения. Вы можете использовать собственные изображения или скопировать файлы изображений azure-logo.png и search.png из проекта GitHub. Эти два изображения следует поместить в папку **wwwroot/images**.

2. Первая строка файла Index.cshtml должна содержать ссылку на модель, которую мы будем использовать для обмена данными между клиентом (представлением) и сервером (контроллером). Это будет созданная нами модель **SearchData**. Добавьте эту строку в файл Index.cshtml.

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. Обычно принято присваивать представлениям названия, и для этого добавьте следующие строки:

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. После заголовка введите ссылку на таблицу стилей HTML, которую мы создадим чуть позже.

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. Теперь переходим к основным механизмам представления. Важно помнить о том, что представление должно обрабатывать два сценария. Во-первых, нужно отображать страницу при первом запуске приложения, когда пользователь еще не ввел текст для поиска. Во-вторых, нужно оформлять отображение результатов рядом с полем поиска для продолжения взаимодействия с пользователем. Чтобы обработать эти два сценария, нужно проверять, содержит ли переданная в представление модель значение NULL или нет. Модель со значением NULL означает, что мы имеем дело с первой из двух ситуаций (начальный запуск приложения). Добавьте следующий код в файл Index.cshtml и просмотрите комментарии.

    ```cs
    <body>
    <h1 class="sampleTitle">
        <img src="~/images/azure-logo.png" width="80" />
        Hotels Search
    </h1>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        // Display the search text box, with the search icon to the right of it.
        <div class="searchBoxForm">
            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
        </div>

        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Results.Count) Results
            </p>

            @for (var i = 0; i < Model.resultList.Results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

6. Наконец, мы добавим таблицу стилей. В Visual Studio выберите элемент **Создать/Файл** в меню **Файл**, а затем — **Таблица стилей** (при выделенном варианте **Общие**). Замените код по умолчанию приведенным ниже: Мы не будем здесь подробно рассматривать этот файл, так как он содержит стандартные стили HTML.

    ```html
    textarea.box1 {
        width: 648px;
        height: 30px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
    }

    textarea.box2 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }

    .sampleTitle {
        font: 32px/normal 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 32px;
        text-align: left;
    }

    .sampleText {
        font: 16px/bold 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 14px;
        text-align: left;
        height: 30px;
    }

    .searchBoxForm {
        width: 648px;
        box-shadow: 0 0 0 1px rgba(0,0,0,.1), 0 2px 4px 0 rgba(0,0,0,.16);
        background-color: #fff;
        display: inline-block;
        border-collapse: collapse;
        border-spacing: 0;
        list-style: none;
        color: #666;
    }

    .searchBox {
        width: 568px;
        font-size: 16px;
        margin: 5px 0 1px 20px;
        padding: 0 10px 0 0;
        border: 0;
        max-height: 30px;
        outline: none;
        box-sizing: content-box;
        height: 35px;
        vertical-align: top;
    }

    .searchBoxSubmit {
        background-color: #fff;
        border-color: #fff;
        background-image: url(/images/search.png);
        background-repeat: no-repeat;
        height: 20px;
        width: 20px;
        text-indent: -99em;
        border-width: 0;
        border-style: solid;
        margin: 10px;
        outline: 0;
    }
    ```

7. Сохраните файл таблицы стилей с именем hotels.css в папку wwwroot/css рядом с предложенным по умолчанию файлом site.css.

Этим мы завершаем подготовку представления. Мы уже многое успели сделать. Модели и представление полностью готовы, осталось лишь собрать все элементы в контроллере.

## <a name="define-methods"></a>Определение методов

Нам нужно изменить содержимое одного контроллера (**Home**), который создается по умолчанию.

1. Откройте файл HomeController.cs и замените операторы **using** следующим кодом.

    ```cs
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using FirstAzureSearchApp.Models;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    ```

### <a name="add-index-methods"></a>Добавление методов Index

Нам потребуются два метода **Index**, один из которых выполняется без параметров (для первого открытия приложения), а второй принимает модель в качестве параметра (если пользователь уже ввел текст для поиска). Первый из этих методов создается по умолчанию. 

1. Добавьте следующий метод после уже существующего метода **Index()** .

    ```cs
        [HttpPost]
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the Azure Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    Обратите внимание, что метод объявлен как **async** и содержит вызов **await** для **RunQueryAsync**. Эти ключевые слова позволяют выполнять вызовы асинхронно, чтобы не блокировать потоки на сервере.

    Блок **Catch** использует модель ошибок, которая создана для нас по умолчанию.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Изучение механизма обработки ошибок и других представлений и методов, созданных по умолчанию

В зависимости от используемой версии .NET Core набор создаваемых по умолчанию представлений будет немного различаться. Для .NET Core 2.1 по умолчанию создаются представления Index, About, Contact, Privacy и Error. Для .NET Core 2.2 по умолчанию создаются представления Index, Privacy и Error. В любом случае вы можете просмотреть эти страницы, запустив приложение, и изучить их обработку в контроллере.

Представление Error мы будет тестировать позже.

В примере на GitHub мы удалили неиспользуемые представления и связанные с ними действия.

### <a name="add-the-runqueryasync-method"></a>Добавление метода RunQueryAsync

Вызов к Поиску Azure инкапсулируется в метод **RunQueryAsync**.

1. Сначала добавьте статические переменные, которые настраивают службу Azure, и вызов для их запуска.

    ```cs
        private static SearchServiceClient _serviceClient;
        private static ISearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using the appsettings file.
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Pull the values from the appsettings.json file.
            string searchServiceName = _configuration["SearchServiceName"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(queryApiKey));
            _indexClient = _serviceClient.Indexes.GetClient("hotels");
        }
    ```

2. Теперь добавьте метод **RunQueryAsync**.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                // Enter Hotel property names into this list so only these values will be returned.
                // If Select is empty, all values will be returned, which can be inefficient.
                Select = new[] { "HotelName", "Description" }
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // Display the results.
            return View("Index", model);
        }
    ```

    В этом методе мы убедимся, что конфигурация Azure успешно запущена, а затем настроим некоторые параметры поиска. Имена полей в параметре **Select** в точности совпадают с именами свойств в классе **hotel**. Вы можете совсем убрать параметр **Select**, и тогда будут возвращаться все свойства. Но отсутствие параметра **Select** снижает эффективность работы, если мы используем только подмножество данных. Если вы укажете интересующие вас свойства, будут возвращаться значения только этих свойств.

    В этом руководстве и приложении используются исключительно асинхронные вызовы службы поиска (**model.resultList = await _indexClient.Documents.SearchAsync&lt;Hotel&gt;(model.searchText, parameters)** ). Класс **DocumentSearchResult** довольно интересен, и часто бывает полезным (если приложение выполняется) установить в нем точку останова и с помощью отладчика изучить содержимое **model.resultList**. Вы увидите, что он интуитивно понятен, предоставляет искомые данные и почти ничего сверх того.

Итак, наступает самый важный момент.

### <a name="test-the-app"></a>Тестирование приложения

Мы проверим правильность выполнения приложения.

1. Выберите команду **Отладка/Запуск без отладчика** или нажмите клавишу F5. Если вы все создали правильно, откроется стартовое представление Index.

     ![Открытие приложения](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. Введите текст для поиска, например beach (пляж) или любой другой вариант, который придет вам в голову, и щелкните значок поиска. Вы получите некоторый набор результатов.

     ![Поиск по слову beach](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. Попробуйте ввести five star (пятизвездочный). Вы сразу заметите, что результаты отсутствуют. Более сложный поиск будет воспринимать "пятизвездочный" как синоним слова "первоклассный" и возвратит соответствующие результаты. Поиск Azure поддерживает использование синонимов, но мы не будем обсуждать этот вопрос в руководствах для начинающих.
 
4. Выполните поиск по запросу hot (горячий). В результатах будут _отсутствовать_ записи со словом hotel (отель). Наша функция поиска умеет искать только целые слова, и результатов здесь будет мало.

5. Поэкспериментируйте с разными словами: pool (бассейн), sunshiny (солнечный), view (вид) и так далее. Вы наверняка убедитесь, что Поиск Azure выдает правильный, хотя и самый простой, результат.

## <a name="test-edge-conditions-and-errors"></a>Проверка пограничных условий и ошибок

Важно убедиться, что все функции обработки ошибок работают правильно. 

1. В методе **Index** после вызова **try {** введите строку **Throw new Exception()** . Это исключение создает ошибку при поиске любого текста.

2. Откройте приложение, введите для поиска слово bar и щелкните значок поиска. Исключение сразу откроет представление ошибки.

     ![Принудительный вызов ошибки](./media/tutorial-csharp-create-first-app/azure-search-error.png)

    > [!Important]
    > Считается, что отображение внутренних номеров ошибок на страницах ошибок, несет угрозу безопасности. Если вы планируете сделать это приложение общедоступным, изучите рекомендации по безопасности для отображения сообщений об ошибках.

3. Удалите строку **Throw new Exception()** , убедившись в том, что ошибки обрабатываются как положено.

## <a name="takeaways"></a>Общие выводы

По результатам этого проекта можно сделать такие выводы:

* Вызов службы "Поиск Azure" и интерпретация результатов не представляют никаких сложностей.
* Асинхронный вызов немного усложняет контроллер, но это правильный подход к разработке качественных приложений.
* Наше приложение выполняет простой текстовый поиск в полном соответствии с настройками в **searchParameters**. Но наш единственный класс может быть заполнен большим числом элементов, что повышает сложность поиска. Чтобы существенно увеличить возможности нашего приложения, не потребуется много усилий.

## <a name="next-steps"></a>Дополнительная информация

Чтобы пользователи могли удобно работать с Поиском Azure, нам нужно добавить еще несколько функций, в частности разбивку на страницы (по номерам страниц или с бесконечной прокруткой) и автозаполнение с поисковыми подсказками. Нам нужно также подумать над усложнением параметров поиска (например, возможностью поиска отелей в радиусе от заданной точки или упорядочением результатов поиска).

Вопросы такого рода оптимизации рассматриваются в следующих руководствах серии. Давайте начнем с разбивки на страницы.

> [!div class="nextstepaction"]
> [Руководство для разработчиков C#. Разбиение результатов поиска на страницы (Поиск Azure)](tutorial-csharp-paging.md)


