---
title: Добавьте всплывающее окно в точку на карте Карты Microsoft Azure
description: В этой статье вы узнаете, как добавить всплывающее окно в точку с помощью Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cf6424d2a6cbcfb7c5052201b5a9190c81fddaff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055952"
---
# <a name="add-a-popup-to-the-map"></a>Добавление всплывающего окна на карту

В этой статье объясняется, как добавить всплывающее окно в точку на карте.

## <a name="understand-the-code"></a>Изучение кода

Следующий код добавляет функцию `name` точки, которая имеет и `description` свойства, на карту с помощью слоя символа. Экземпляр класса [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup) создается, но не отображается. События мыши добавляются в слой символа, чтобы вызвать открытие и закрытие всплывающих. Когда символ маркера завис, `position` свойство всплывающих обновлено обновляется `content` с позицией маркера, и `name` `description` опция обновляется с некоторыми HTML, который обертывания и свойства точки функции завис. Всплывающее окно затем отображается на `open` карте с использованием его функции.

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

Ниже приводится полный выборка кода приведена вышеуказанной функциональности.

<br/>

<iframe height='500' scrolling='no' title='Добавление всплывающего окна с помощью Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Добавление всплывающего окна с помощью Azure Maps</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Повторное использование всплывающего окна с несколькими точками

Есть случаи, когда лучший подход заключается в создании одного всплывающих и повторно использовать его. Например, вы можете иметь большое количество очков и хотите показать только одно всплывающее окно за один раз. При повторном использовании всплывающих элементов количество элементов DOM, созданных приложением, значительно уменьшается, что может обеспечить лучшую производительность. Следующий пример создает 3-точечные функции. Если щелкнуть любую из них, отобразится всплывающее окно с содержимым этой функции точки.

<br/>

<iframe height='500' scrolling='no' title='Повторное использование всплывающего окна с несколькими закреплениями' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>повторного использования всплывающего окна с несколькими закреплениями</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Настройка всплывающего пацана

По умолчанию всплывающее окно имеет белый фон, стрелка указателя внизу и кнопку закрытия в правом верхнем углу. Следующий образец изменяет цвет `fillColor` фона на черный, используя опцию всплывающих данных. Кнопка закрытия удаляется, `CloseButton` установив опцию на ложную. HTML-содержимое всплывающих данных использует 10 пикселей от краев всплывающих данных. Текст сделан белым, поэтому он хорошо появляется на черном фоне.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Индивидуальный всплывающий" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите Pen <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>Индивидуальные Popup</a> по<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-popup-templates-to-the-map"></a>Добавление всплывающих шаблонов на карту

Шаблоны popup упрощают создание макетов на основе данных для всплывающих данных. В приведенных ниже разделах показано использование различных всплывающих шаблонов для создания отформатированных содержимых с использованием свойств функций.

> [!NOTE]
> По умолчанию все содержимое, отображаемый, использует всплывающий шаблон, будет застечено в песочнице в iframe в качестве функции безопасности. Однако существуют ограничения:
>
> - Все скрипты, формы, блокировка указателей и функция верхней навигации отключены. Ссылки могут открываться в новой вкладке при нажатии. 
> - Старые браузеры, не `srcdoc` поддерживающие параметр на iframes, будут ограничены рендерингом небольшого количества содержимого.
> 
> Если вы доверяете загрузке данных во всплывающие данные и потенциально хотите, чтобы эти скрипты, загруженные во всплывающие данные, могли получить доступ к вашему приложению, вы можете отключить это, установив опцию всплывающих шаблонов `sandboxContent` на ложную. 

### <a name="string-template"></a>Шаблон строки

Шаблон строки заменяет заполнителей значениями свойств функций. Свойства функции не должны быть присвоены значение типа строки. Например, `value1` держит сятку. Эти значения затем передаются в свойство содержимого `popupTemplate`. 

Опция `numberFormat` определяет формат номера для отображения. `numberFormat` Если не указано, код будет использовать формат даты всплывающих шаблонов. Вариант `numberFormat` форматирует номера, используя [функцию Number.toLocaleString.](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) Для формата больших чисел рассмотрите возможность использования `numberFormat` опции с функциями [numberFormat.format](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format). Например, фрагмент кода ниже `maximumFractionDigits` использует для ограничения числа цифр фракции двумя цифрами.

> [!Note]
> Есть только один способ визуализации изображений шаблона String. Во-первых, шаблон строки должен иметь тег изображения в нем. Значение, передаваемые на тег изображения, должно быть URL-адресом изображения. Затем шаблон строки должен `isImage` быть установлен `HyperLinkFormatOptions`на истину в . Опция `isImage` указывает, что гиперссылка предназначена для изображения, а гиперссылка будет загружена в тег изображения. При нажатии гиперссылки изображение откроется.

```javascript
var templateOptions = {
  content: 'This template uses a string template with placeholders.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 1 - String template',
    value1: 1.2345678,
    value2: {
        subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6]
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="propertyinfo-template"></a>Шаблон PropertyInfo

Шаблон PropertyInfo отображает доступные свойства функции. Опция `label` определяет текст для отображения пользователю. Если `label` не указано, то гиперссылка будет отображаться. И, если гиперссылка является изображением, значение, присвоенное тегу "alt", будет отображаться. В `dateFormat` определяем формат даты, и если формат даты не указан, то дата будет отображаться как строка. Опция `hyperlinkFormat` отображает интерактивные ссылки, `email` аналогичным образом, параметр может быть использован для визуализации кликабельных адресов электронной почты.

Перед тем, как шаблон PropertyInfo отобразит свойства конечному пользователю, он повторно проверяет, действительно ли свойства определены для этой функции. Он также игнорирует отображение стиля и свойства названия. Например, он не `color`будет `size` `anchor`отображаться, , `strokeOpacity`и `visibility`. Таким образом, как только проверка пути свойств завершена в бэк-энде, шаблон PropertyInfo показывает содержимое в формате таблицы.

```javascript
var templateOptions = {
  content: [
    {
        propertyPath: 'createDate',
        label: 'Created Date'
    },
    {
        propertyPath: 'dateNumber',
        label: 'Formatted date from number',
        dateFormat: {
          weekday: 'long',
          year: 'numeric',
          month: 'long',
          day: 'numeric',
          timeZone: 'UTC',
          timeZoneName: 'short'
        }
    },
    {
        propertyPath: 'url',
        label: 'Code samples',
        hideLabel: true,
        hyperlinkFormat: {
          lable: 'Go to code samples!',
          target: '_blank'
        }
    },
    {
        propertyPath: 'email',
        label: 'Email us',
        hideLabel: true,
        hyperlinkFormat: {
          target: '_blank',
          scheme: 'mailto:'
        }
    }
  ]
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 2 - PropertyInfo',
    createDate: new Date(),
    dateNumber: 1569880860542,
    url: 'https://aka.ms/AzureMapsSamples',
    email: 'info@microsoft.com'
}),

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="multiple-content-templates"></a>Несколько шаблонов содержимого

Функция может также отображать содержимое, используя комбинацию шаблона строки и шаблона PropertyInfo. В этом случае шаблон Строка отображает значения заполнителей на белом фоне.  И шаблон PropertyInfo отображает изображение полной ширины внутри таблицы. Свойства в этом образце аналогичны свойствам, которые мы объясняли в предыдущих образцах.

```javascript
var templateOptions = {
  content: [
    'This template has two pieces of content; a string template with placeholders and a array of property info which renders a full width image.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
    [{
      propertyPath: 'imageLink',
      label: 'Image',
      hideImageLabel: true,
      hyperlinkFormat: {
        isImage: true
      }
    }]
  ],
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 3 - Multiple content template',
    value1: 1.2345678,
    value2: {
    subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    imageLink: 'https://azuremapscodesamples.azurewebsites.net/common/images/Pike_Market.jpg'
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="points-without-a-defined-template"></a>Очки без определенного шаблона

Когда шаблон Popup не определяется как шаблон строки, шаблон PropertyInfo или комбинация обоих, то он использует настройки по умолчанию. Когда `title` и `description` являются единственными назначенными свойствами, шаблон всплывающих данных показывает белый фон, кнопку закрытия в правом верхнем углу. А на малых и средних экранах изображена стрелка внизу. Настройки по умолчанию отображаемы `title` внутри `description`таблицы для всех свойств, кроме и . Даже при возврате к настройкам по умолчанию шаблоном всплывающих окновсе можно манипулировать программно. Например, пользователи могут отключить обнаружение гиперссылок, а параметры по умолчанию по-прежнему будут применяться к другим свойствам.

Нажмите на точки на карте в CodePen. Существует точка на карте для каждого из следующих шаблонов всплывающих: шаблон строки, шаблон PropertyInfo, и шаблон несколько содержание. Есть также три момента, чтобы показать, как шаблоны рендеринга с помощью параметров по умолчанию.

<br/>

<iframe height='500' scrolling='no' title='Всплывающие шаблоны' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Смотрите Pen <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>PopupTemplates</a> по Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reuse-popup-template"></a>Повторное использование шаблона всплывающих

Как и в повторном использовании всплывающих, вы можете повторно использовать всплывающие шаблоны. Этот подход полезен, если вы хотите показать только один всплывающий шаблон за один раз, для нескольких точек. При повторном использовании всплывающих шаблонов количество элементов DOM, созданных приложением, уменьшается, что затем повышает производительность приложения. В следующем примере используется тот же всплывающий шаблон для трех точек. Если щелкнуть любую из них, отобразится всплывающее окно с содержимым этой функции точки.

<br/>

<iframe height='500' scrolling='no' title='ПовторноеиспользованиеПопперлахлк' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Смотрите Pen <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>ReusePopupTemplate</a> по Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Всплывающие мероприятия

Всплывающие окно могут быть открыты, закрыты и перетаскиваются. Всплывающий класс предоставляет события, чтобы помочь разработчикам реагировать на эти события. В следующем примере выделяется, какие события сравняют, когда пользователь открывает, закрывает или перетаскивает всплывающее окно. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Всплывающие мероприятия" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите события Pen <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>Popup</a> по<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Контекстное меню](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [Всплывающие шаблоны](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popuptemplate)

Полные примеры кода см. в следующих превосходных статьях:

> [!div class="nextstepaction"]
> [Добавление слоя символов](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Добавление маркера HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Добавление слоя линий](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Добавление слоя многоугольников](map-add-shape.md)
