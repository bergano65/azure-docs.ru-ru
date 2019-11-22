---
title: Учебник по C#. Упорядочение результатов
titleSuffix: Azure Cognitive Search
description: В этом учебнике показано, как упорядочить результаты поиска. В его основе лежит имеющийся проект гостиниц с упорядочиванием по первичному и дополнительному свойству, а также профилем повышения для добавления критериев.
manager: nitinme
author: PeterTurcan
ms.author: v-pettur
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 9230e555f9ea56ca4cda3fcabe1f9719e36a8f62
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112681"
---
# <a name="c-tutorial-order-the-results---azure-cognitive-search"></a>Руководство по C#. Упорядочивание результатов в службе "Когнитивный поиск Azure".

Вплоть до этого момента в нашей серии руководств вы возвращали и отображали все результаты в стандартном порядке. Он может определяться порядком, в котором расположены данные, или значениями стандартного _профиля повышения_, который используется при отсутствии параметров сортировки. Из этого руководства вы узнаете, как правильно упорядочивать результаты и как упорядочивать по дополнительному свойству те выбранные результаты, у которых совпадают значения основного свойства. Вместо упорядочения по числовым значениям в последнем примере мы покажем, как упорядочить значения по пользовательскому профилю повышения. Мы также немного изучим _сложные типы_.

Чтобы быстро перейти к сравнению результатов, мы создадим этот проект на основе проекта с бесконечной прокруткой, который мы создали, выполнив инструкции в статье [C# Tutorial: Search results pagination — Azure Cognitive Search](tutorial-csharp-paging.md) (Учебник по C#. Разбиение результатов поиска на страницы — Когнитивный поиск Azure).

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * упорядочение результатов по одному свойству;
> * упорядочение результатов по нескольким свойствам;
> * фильтрация результатов в зависимости от расстояния до географической точки;
> * упорядочение результатов по профилю повышения.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется следующее:

Получить версию проекта с бесконечной прокруткой из статьи [C# Tutorial: Search results pagination — Azure Cognitive Search](tutorial-csharp-paging.md) (Учебник по C#. Разбиение результатов поиска на страницы — Когнитивный поиск Azure). Это может быть ваша собственная версия проекта либо версия, установленная с сайта GitHub: [Создайте первое приложение](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="order-results-based-on-one-property"></a>Упорядочение результатов по одному свойству

При сортировке результатов по одному свойству, например по рейтингу отелей, нам нужно не только упорядочить результаты, нам но и убедиться в правильности этого порядка. Другими словами, при сортировке по рейтингу важно отображать значения этого рейтинга.

В этом руководстве мы добавим в отображение результатов еще несколько элементов: самая низкая и самая высокая цена номеров для каждого отеля. По мере изучения упорядочения мы добавим значения, которые позволят отображать в представлении тот параметр, по которому упорядочены результаты.

Для поддержки упорядочения не придется менять модели. Обновление потребуется только для представления и контроллера. Для начала откройте контроллер Home.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Добавление свойства OrderBy в параметры поиска

1. Чтобы упорядочить результаты по одному числовому свойству, достаточно лишь присвоить параметру **OrderBy** значение имени нужного свойства. Добавьте в методе **Index(SearchData model)** следующую строку к параметрам поиска.

    ```cs
        OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > По умолчанию упорядочение выполняется по возрастанию, но для наглядности вы можете добавить к свойству ключевое слово **asc**. Порядок по убыванию определяется ключевым словом **desc**.

2. Теперь запустите приложение и введите любое слово, часто используемое в поиске. Результаты будут возвращаться в некотором порядке, правильность которого пока не могут проверить ни сами разработчики, ни пользователи.

3. Давайте сделаем так, чтобы упорядочение по рейтингу стало очевидным в результатах. Во-первых, замените классы **box1** и **box2** в файле hotels.css следующими классами (это новые классы, которые потребуются нам для работы с этим руководством).

    ```html
    textarea.box1A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box1B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box2A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box2B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box3 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }
    ```

    >[!Tip]
    >Браузеры обычно кэшируют CSS-файлы и это может привести к тому, что будет использоваться старый файл без внесенных изменений. Чтобы обойти эту проблему, мы рекомендуем добавить в ссылку строку запроса с параметром версии. Например:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    >Обновите номер версии, если вы считаете, что браузер использует старый CSS-файл.

4. Добавьте свойство **Rating** к параметру **Select** в методе **Index(SearchData model)** .

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

5. Откройте представление (index.cshtml) и замените цикл отрисовки (с комментарием **&lt;!-- Show the hotel data. --&gt;** ) следующим кодом.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

6. Рейтинг должен быть доступен как на первой отображаемой странице, так и на всех последующих страницах, которые вызываются через механизм бесконечный прокрутки. Для второй из этих ситуаций необходимо обновить действие **Next** в контроллере и функцию **scrolled** в представлении. Начните изменения с контроллера, заменив метод **Next** следующим кодом. Этот код создает и передает текстовое значение рейтинга.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";

                // Add three strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

7. Теперь обновите функцию **scrolled** в представлении, чтобы отображать этот текст.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 3) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 2] + '</textarea>';
                            }
                        });
                    }
                }
            </script>

    ```

8. Снова запустите приложение. Выполните поиск по любому распространенному слову, например wifi, и убедитесь, что результаты сортируются по убыванию рейтинга отелей.

    ![Упорядочение по рейтингу](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Вы можете заметить, что у нескольких отелей значения рейтинга совпадают, и их положение в результатах снова зависит от расположения данных, то есть может считаться произвольным.

    Прежде чем добавить второй уровень упорядочения, давайте добавим код для отображения диапазона стоимости номеров. Этот код позволит нам, во-первых, продемонстрировать извлечение данных из _сложного типа_, а, во-вторых, рассмотреть упорядочение результатов по цене (например, сначала самые дешевые).

### <a name="add-the-range-of-room-rates-to-the-view"></a>Добавление диапазона стоимости номеров в представление

1. Добавьте в модель Hotel.cs свойства, содержащие стоимость самого дешевого и самого дорогого номера.

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

2. Стоимость номеров мы будем вычислять в конце действия **Index(SearchData model)** в контроллере Home. Разместите эти вычисления после сохранения временных данных.

    ```cs
                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    // Calculate room rates.
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
    ```

3. Добавьте свойство **Rooms** в параметр **Select** в методе действия **Index(SearchData model)** в контроллере.

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

4. Измените в представлении цикл отрисовки, чтобы отображать диапазон стоимости на первой странице результатов.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

5. Измените метод **Next** в контроллере Home, чтобы передавать этот диапазон стоимости для последующих страниц результатов.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

6. Обновите функцию **scrolled** в представлении, чтобы обрабатывать текстовое значение диапазона стоимости.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 4) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

7. Запустите приложение и убедитесь, что оно отображает диапазоны стоимости номеров.

    ![Отображение диапазонов стоимости номеров](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

Свойство **OrderBy** в параметрах поиска не будет принимать такие значения, как **Rooms.BaseRate**, для отображения самой низкой стоимости номеров, даже если номера уже отсортированы по стоимости. В этом случае номера не сортируются по стоимости. Чтобы отображать отели из тестового набора данных в порядке увеличения стоимости номеров, вам придется сортировать результаты в контроллере Home и отправлять их в представление уже в нужном порядке.

## <a name="order-results-based-on-multiple-values"></a>Упорядочение результатов по нескольким значениям

Теперь мы перейдем к тому, как различать отели с одинаковым рейтингом. Один из вариантов — упорядочение по дате последнего ремонта отеля. Другими словами, чем позднее был проведен ремонт в отеле, тем более высокую позицию он будет занимать в результатах.

1. Чтобы добавить второй уровень упорядочения, измените свойства **OrderBy** и **Select** в методе **Index(SearchData model)** так, чтобы они содержали свойство **LastRenovationDate**.

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >В список **OrderBy** можно включить любое количество свойств. Если у отелей совпадают и рейтинг, и дата последнего ремонта, можно ввести третье свойство различения между этими отелями.

2. Теперь нам понадобится включить в представление еще и дату ремонта, чтобы контролировать правильность порядка. Для сведений о ремонте, пожалуй, будет достаточно отобразить только год. Замените цикл отрисовки в представлении следующим кодом.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

3. Измените метод **Далее** в контроллере Home, чтобы он отправлял значение года из параметра даты последнего ремонта.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";
                var lastRenovatedText = $"Last renovated: {model.resultList.Results[n].Document.LastRenovationDate.Value.Year}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. Обновите в представлении функцию **scrolled**, чтобы отображать этот текст.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 5) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

5. Запустите приложение. Выполните поиск по распространенному слову, например pool (бассейн) или view (вид) и убедитесь, что отели с одинаковым рейтингом теперь отображаются в порядке даты последнего ремонта.

    ![Упорядочение по дате ремонта](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>Фильтрация результатов в зависимости от расстояния до географической точки

Рейтинг и дату ремонта обычно лучше отображать в порядке убывания. Алфавитный список будет хорошим примером для упорядочения по возрастанию (например, если в параметре **OrderBy** указать только одно свойство **HotelName**, оно будет отображаться в алфавитном порядке). Но для данных из нашего примера более важным будет расстояние от некоторой географической точки.

Чтобы отображать результаты в зависимости от географического расстояния, следует выполнить несколько шагов.

1. Отфильтруйте все отели, расстояние до которых превышает некоторое значение, введя фильтр по параметрам долготы, широты и радиуса. Долгота передается первым параметром в функцию POINT. Радиус измеряется в километрах.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

2. Представленный выше фильтр _не_ упорядочивает результаты по расстоянию, а только исключает посторонние значения. Чтобы упорядочить эти результаты, введите параметр **OrderBy** со ссылкой на метод geoDistance.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

3. Хотя Когнитивный поиск Azure возвращает результаты с применением фильтра по расстоянию, само расстояние между точкой, определенной в данных, и референтной точкой _не_ возвращается. Если вы намерены отображать это значение в результатах, следует заново вычислить его в представлении или в контроллере.

    Следующий код вычисляет расстояние между двумя точками по значениям долготы и широты.

    ```cs
        const double EarthRadius = 6371;

        public static double Degrees2Radians(double deg)
        {
            return deg * Math.PI / 180;
        }

        public static double DistanceInKm( double lat1,  double lon1, double lat2, double lon2)
        {
            double dlon = Degrees2Radians(lon2 - lon1);
            double dlat = Degrees2Radians(lat2 - lat1);

            double a = (Math.Sin(dlat / 2) * Math.Sin(dlat / 2)) + Math.Cos(Degrees2Radians(lat1)) * Math.Cos(Degrees2Radians(lat2)) * (Math.Sin(dlon / 2) * Math.Sin(dlon / 2));
            double angle = 2 * Math.Atan2(Math.Sqrt(a), Math.Sqrt(1 - a));
            return angle * EarthRadius;
        }
    ```

4. Теперь вам нужно объединить эти механизмы. Но в рамках этого руководства мы остановимся на этих фрагментах кода, оставив читателю самостоятельно потренироваться в создании приложений на основе карты. Чтобы дальше развить этот пример, можно передать в поиск название города и радиус или выбирать на карте точку и значение радиуса. Эти дополнительные возможности можно изучить в следующих документах:

* [Документация по службе карт Azure](https://docs.microsoft.com/azure/azure-maps/)
* [Поиск адреса с помощью службы поиска Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

## <a name="order-results-based-on-a-scoring-profile"></a>Упорядочение результатов по профилю повышения

Пока все примеры в этом руководстве использовали упорядочение по числовым значениям (рейтинг, дата ремонта, географическое расстояние), реализуя процесс _точного_ упорядочения. Но некоторые поисковые запросы для некоторых данных не позволяют выполнить простое сравнение двух элементов. Когнитивный поиск Azure поддерживает понятие _оценки_. _Профили повышения_ можно использовать для более сложных и качественных сравнений по наборам данных, что будет очень полезно, например, для определения порядка отображения путем сравнения текстовых данных.

Профили повышения обычно определяются не пользователями, а администраторами наборов данных. Для данных об отелях создано несколько профилей повышения. Давайте рассмотрим, как определяется профиль повышения, а затем напишем код для их применения в поиске.

### <a name="how-scoring-profiles-are-defined"></a>Определение профилей повышения

Давайте рассмотрим три примера профилей повышения и изучим, как каждый из них _должен_ влиять на порядок результатов. Вам, как разработчику приложения, не нужно создавать эти профили, ведь они создаются администратором данных, но понимание их синтаксиса будет вам полезно.

1. Это стандартный профиль повышения для набора данных об отелях, который используется при отсутствии параметров **OrderBy** или **ScoringProfile**. Этот профиль повышает _оценку_ отеля, если искомый текст присутствует в названии отеля, описании или списке тегов (удобств). Обратите внимание, как весовые коэффициенты повышения отдают приоритеты определенным полям. Если искомый текст встречается в другом поле, кроме перечисленных ниже, он получает весовой коэффициент 1. Очевидно, что чем выше оценка, тем выше отображается результат в представлении.

     ```cs
    {
            "name": "boostByField",
            "text": {
                "weights": {
                    "HotelName": 2,
                    "Description": 1.5,
                    "Description_fr": 1.5,
                    "Tags": 3
                }
            }
        }

    ```

2. Следующий профиль повышения существенно увеличивает оценку, если переданный параметр включает один или несколько тегов из списка (условно именуемого списком "удобств"). Важной характеристикой этого профиля является то, что параметр является _обязательным_ и должен содержать текстовое значение. Если параметр пуст или не указан, возникает ошибка.
 
    ```cs
            {
            "name": "boostAmenities",
            "functions": [
                {
                    "type": "tag",
                    "fieldName": "Tags",
                    "boost": 5,
                    "tag": {
                        "tagsParameter": "amenities"
                    }
                }
            ]
        }
    ```

3. В этом третьем примере рейтинг значительно влияет на оценку. Дата последнего ремонта также повышает оценку, но только если он выполнялся не ранее, чем 730 дней (2 года) назад от текущей даты.

    ```cs
            {
            "name": "renovatedAndHighlyRated",
            "functions": [
                {
                    "type": "magnitude",
                    "fieldName": "Rating",
                    "boost": 20,
                    "interpolation": "linear",
                    "magnitude": {
                        "boostingRangeStart": 0,
                        "boostingRangeEnd": 5,
                        "constantBoostBeyondRange": false
                    }
                },
                {
                    "type": "freshness",
                    "fieldName": "LastRenovationDate",
                    "boost": 10,
                    "interpolation": "quadratic",
                    "freshness": {
                        "boostingDuration": "P730D"
                    }
                }
            ]
        }

    ```

    Давайте посмотрим, работают ли эти профили так, как задумано.

### <a name="add-code-to-the-view-to-compare-profiles"></a>Добавление кода сравнения профилей в представление

1. Откройте файл index.cshtml и замените раздел &lt;body&gt; следующим кодом.

    ```cs
    <body>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        <table>
            <tr>
                <td></td>
                <td>
                    <h1 class="sampleTitle">
                        <img src="~/images/azure-logo.png" width="80" />
                        Hotels Search - Order Results
                    </h1>
                </td>
            </tr>
            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it. -->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
                    </div>

                    <div class="searchBoxForm">
                        <b>&nbsp;Order:&nbsp;</b>
                        @Html.RadioButtonFor(m => m.scoring, "Default") Default&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "RatingRenovation") By numerical Rating&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "boostAmenities") By Amenities&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "renovatedAndHighlyRated") By Renovated date/Rating profile&nbsp;&nbsp;
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.facetText != null)
                        {
                            <h5 class="facetheader">Amenities:</h5>
                            <ul class="facetlist">
                                @for (var c = 0; c < Model.facetText.Length; c++)
                                {
                                    <li> @Html.CheckBoxFor(m => m.facetOn[c], new { @id = "check" + c.ToString() }) @Model.facetText[c] </li>
                                }

                            </ul>
                        }
                    </div>
                </td>
                <td>
                    @if (Model != null && Model.resultList != null)
                    {
                        // Show the total result count.
                        <p class="sampleText">
                            @Html.DisplayFor(m => m.resultList.Count) Results <br />
                        </p>

                        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                            <!-- Show the hotel data. -->
                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                                var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                                var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);
                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel details.
                                @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                                @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                                @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
                                @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
                            }
                        </div>

                        <script>
                            function scrolled() {
                                if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                                    $.getJSON("/Home/Next", function (data) {
                                        var div = document.getElementById('myDiv');

                                        // Append the returned data to the current list of hotels.
                                        for (var i = 0; i < data.length; i += 5) {
                                            div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                            div.innerHTML += '<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                            div.innerHTML += '<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                                        }
                                    });
                                }
                            }
                        </script>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

2. Откройте файл SearchData.cs и замените класс **SearchData** следующим кодом.

    ```cs
    public class SearchData
    {
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each amenity.
        public string[] facetText { get; set; }

        // Array to hold the setting for each amenitity.
        public bool[] facetOn { get; set; }

        // The text to search for.
        public string searchText { get; set; }

        // Record if the next page is requested.
        public string paging { get; set; }

        // The list of results.
        public DocumentSearchResult<Hotel> resultList;

        public string scoring { get; set; }       
    }
    ```

3. Откройте файл hotels.css и добавьте следующие классы HTML.

    ```html
    .facetlist {
        list-style: none;
    }
    
    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;
    }
    ```

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Добавление кода с определением профиля оценки в контроллер

1. Откройте файл с контроллером Home. Добавьте следующую инструкцию **using**, которая поможет в создании списков.

    ```cs
    using System.Linq;
    ```

2.  В нашем примере первый вызов метода **Index** должен делать немного больше, чем просто вернуть начальное представление. Теперь этот метод извлекает до 20 удобств, которые можно отобразить в представлении.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search for up to 20 amenities.
                Facets = new List<string> { "Tags,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

            // Convert the results to a list that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(facets);

            // Save the facet text for the next view.
            SaveFacets(model, false);

            // Render the view including the facets.
            return View(model);
        }
    ```

3. Нам нужны два частных метода, которые сохраняют аспекты во временном хранилище и восстанавливают их из временного хранилища для заполнения модели.

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

4. Нам нужно настроить параметры **OrderBy** и **ScoringProfile**. Замените существующий метод **Index(SearchData model)** следующим кодом.

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                InitSearch();

                int page;

                if (model.paging != null && model.paging == "next")
                {
                    // Recover the facet text, and the facet check box settings.
                    RecoverFacets(model, true);

                    // Increment the page.
                    page = (int)TempData["page"] + 1;

                    // Recover the search text.
                    model.searchText = TempData["searchfor"].ToString();
                }
                else
                {
                    // First search with text. 
                    // Recover the facet text, but ignore the check box settings, and use the current model settings.
                    RecoverFacets(model,false);

                    // First call. Check for valid text input, and valid scoring profile.
                    if (model.searchText == null)
                    {
                        model.searchText = "";
                    }
                    if (model.scoring == null)
                    {
                        model.scoring = "Default";
                    }
                    page = 0;
                }

                // Set empty defaults for ordering and scoring parameters.
                var orderby = new List<string>();
                string profile = "";
                var scoringParams = new List<ScoringParameter>();

                // Set the ordering based on the user's radio button selection.
                switch (model.scoring)
                {
                    case "RatingRenovation":
                        orderby.Add("Rating desc");
                        orderby.Add("LastRenovationDate desc");
                        break;

                    case "boostAmenities":
                        {
                            profile = model.scoring;
                            var setAmenities = new List<string>();

                            // Create a string list of amenities that have been clicked.
                            for (int a = 0; a < model.facetOn.Length; a++)
                            {
                                if (model.facetOn[a])
                                {
                                    setAmenities.Add(model.facetText[a]);
                                }
                            }
                            if (setAmenities.Count > 0)
                            {
                                // Only set scoring parameters if there are any.
                                var sp = new ScoringParameter("amenities", setAmenities);
                                scoringParams.Add(sp);
                            }
                            else
                            {
                                // No amenities selected, so set profile back to default.
                                profile = "";
                            }
                        }
                        break;

                    case "renovatedAndHighlyRated":
                        profile = model.scoring;
                        break;

                    default:
                        break;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Set the ordering/scoring parameters.
                    OrderBy = orderby,
                    ScoringProfile = profile,
                    ScoringParameters = scoringParams,

                    // Select the data properties to be returned.
                    Select = new[] { "HotelName", "Description", "Tags", "Rooms", "Rating", "LastRenovationDate" },
                    SearchMode = SearchMode.All,

                    // Skip past results that have already been returned.
                    Skip = page * GlobalVariables.ResultsPerPage,

                    // Take only the next page worth of results.
                    Top = GlobalVariables.ResultsPerPage,

                    // Include the total number of results.
                    IncludeTotalResultCount = true,
                };

                // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
                model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;
                TempData["scoring"] = model.scoring;
                SaveFacets(model,true);

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    Прочтите комментарии для каждого из блоков выбора **switch**.

5. Нам не нужно вносить изменения в действие **Next**, если вы уже выполнили дополнительный код из предыдущего раздела, посвященного упорядочению по нескольким свойствам.

### <a name="run-and-test-the-app"></a>Запуск и тестирование приложения

1. Запустите приложение. Вы увидите в представлении полный набор удобств.

2. Если выбрать для сортировки вариант "By numerical Rating" (По числовому рейтингу), будет использоваться ранее созданное упорядочение по числовым значениям с дополнительным упорядочением по дате ремонта для отелей с одинаковым рейтингом.

![Поиск по слову beach с упорядочением по рейтингу](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

3. Теперь попробуйте профиль By amenities (По удобствам). Попробуйте разные наборы удобств и убедитесь, что отели с наличием этих удобств оказываются выше в списке результатов.

![Поиск по слову beach с упорядочением по профилю](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

4. Попробуйте вариант By Renovated date/Rating profile (По дате ремонта и профилю рейтинга) и убедитесь, что результаты соответствуют ожиданиям. Только недавно отремонтированные отели должны получать бонус за "актуальность" (_freshness_).

### <a name="resources"></a>Ресурсы

Подробные сведения см. в статье [Add scoring profiles to an Azure Search index](https://docs.microsoft.com/azure/search/index-add-scoring-profiles) (Добавление профилей повышения в индекс Когнитивного поиска Azure).

## <a name="takeaways"></a>Общие выводы

По результатам этого проекта можно сделать такие выводы:

* Пользователи будут ожидать, что результаты выводятся упорядоченными и первыми отображаются самые важные из них.
* Данные нужно структурировать так, чтобы упорядочение выполнялось очень просто. Мы не смогли легко выполнить сортировку по стоимости номеров, так как структура данных не поддерживает такое упорядочение без использования дополнительного кода.
* Можно использовать много уровней упорядочения, чтобы различать результаты с одинаковыми значениями более высоких уровней упорядочения.
* Для некоторых результатов естественным будет упорядочение по возрастанию (например, расстояние от некоторой точки), а для других — по убыванию (например, оценки гостей).
* Вы можете определить профили повышения, когда числовые параметры для набора данных неприменимы или не дают осмысленных результатов. Оценка каждого результата позволяет более точно упорядочивать и отображать результаты.

## <a name="next-steps"></a>Дополнительная информация

Вы завершили серию учебников по C#, и получили ценные знания об API-интерфейсах Когнитивного поиска Azure.

Подробные сведения и учебники вы можете найти в библиотеке документации [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure) или в других разделах [документации по Когнитивному поиску Azure](https://docs.microsoft.com/azure/search/).
