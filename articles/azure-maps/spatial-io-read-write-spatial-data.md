---
title: Читать и писать пространственные данные Карты Microsoft Azure
description: Узнайте, как читать и писать данные с помощью модуля Spatial IO, предоставленного Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4c47335689401ebce98224992c74c3396821a1dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334156"
---
# <a name="read-and-write-spatial-data"></a>Чтение и запись пространственных данных

В таблице ниже перечислены форматы пространственных файлов, которые поддерживаются для чтения и записи с помощью модуля Spatial IO.

| Формат данных       | Чтение | запись |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| ГеоРСС            | ✓  |  ✓  |
| Gml               | ✓  |  ✓  |
| Gpx               | ✓  |  ✓  |
| Kml               | ✓  |  ✓  |
| Kmz               | ✓  |  ✓  |
| Пространственный CSV       | ✓  |  ✓  |
| Известный текст   | ✓  |  ✓  |

В следующих разделах изложены все различные инструменты для чтения и записи пространственных данных с помощью модуля Spatial IO.

## <a name="read-spatial-data"></a>Чтение пространственных данных

Функция `atlas.io.read` является основной функцией, используемой для чтения общих форматов пространственных данных, таких как KML, GPX, GeoRSS, GeoJSON и CSV файлы с пространственными данными. Эта функция также может читать сжатые версии этих форматов, как почтовый файл или файл KM. Формат файла КМЗ представляет собой сжатую версию KML, которая также может включать в себя такие активы, как изображения. Кроме того, функция чтения может принимать URL-адрес, который указывает на файл в любом из этих форматов. URL-адреса должны размещаться на конечную точку с включенной CORS или прокси-сервис должен быть предоставлен в опциях чтения. Прокси-сервис используется для загрузки ресурсов на домены, которые не включены CORS. Функция чтения возвращает обещание добавить значки изображений на карту и обрабатывает данные асинхронно, чтобы свести к минимуму воздействие на поток uI.

При чтении сжатого файла, как молния или КМЗ, он будет расстегнул и отсканирован для первого действительного файла. Например, doc.kml или файл с другим допустимым расширением, например: .kml, .xml, geojson, .json, .csv, .tsv или .txt. Затем изображения, на которые ссылаются в файлах KML и GeoRSS, предварительно загружаются, чтобы обеспечить их доступность. Недоступные данные изображения могут загрузить альтернативное резервное изображение или будут удалены из стилей. Изображения, извлеченные из файлов КМЗ, будут преобразованы в URIs данных данных.

Результатом функции чтения `SpatialDataSet` является объект. Этот объект расширяет класс GeoJSON FeatureCollection. Он может быть легко `DataSource` передан в качестве есть, чтобы сделать его особенности на карте. Информация `SpatialDataSet` не только содержит сведения о функциях, но и может включать наземные накладки KML, метрики обработки и другие детали, изложенные в следующей таблице.

| Имя свойства | Тип | Описание | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Привязка всех данных в наборе данных. |
| `features` | `Feature[]` | Функции GeoJSON в наборе данных. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | Массив KML GroundOverlays. |
| `icons` | Запись&lt;строки, строка&gt; | Набор URL-адресов значков. Имя значка ключа, значение и URL. |
| properties | any | Информация о собственности, предоставляемая на уровне документов в наборе пространственных данных. |
| `stats` | `SpatialDataSetStats` | Статистика о содержании и времени обработки набора пространственных данных. |
| `type` | `'FeatureCollection'` | Значение типа GeoJSON только для чтения. |

## <a name="examples-of-reading-spatial-data"></a>Примеры чтения пространственных данных

Следующий код показывает, как читать набор пространственных данных, и `SimpleDataLayer` отобразить его на карте с помощью класса. В коде используется файл GPX, на который указывает URL.

<br/>

<iframe height='500' scrolling='no' title='Загрузка пространственных данных проста' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Смотрите Pen <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>Load Пространственные данные Простые</a> по Azure Maps ( )<a href='https://codepen.io/azuremaps'>@azuremaps</a>на <a href='https://codepen.io'>CodePen</a>.
</iframe>

На следующем демо-версии кода показано, как читать и загружать KML, или KM, на карту. KML может содержать наземные накладки, которые `ImageLyaer` `OgcMapLayer`будут в виде или . Эти наложения должны быть добавлены на карте отдельно от объектов. Кроме того, если набор данных имеет пользовательские значки, эти значки должны быть загружены на ресурсы карт, прежде чем объекты будут загружены.

<br/>

<iframe height='500' scrolling='no' title='Нагрузка KML Onto Карта' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Смотрите Pen <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>Load KML Onto Карта</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

Вы можете по желанию предоставить прокси-сервис для доступа к кроссдоменным ресурсам, которые могут не иметь включенного CORS. Функция чтения будет пытаться получить доступ к файлам на другом домене с помощью CORS в первую очередь. После первого раза он не может получить доступ к любому ресурсу на другом домене с помощью CORS он будет запрашивать дополнительные файлы только в том случае, если прокси-сервис был предоставлен. Функция чтения придает URL-адрес файла концу предоставленного URL-адреса прокси. На этом фрагменте кода показано, как передать прокси-сервис в прочитанном функции:

```javascript
//Read a file from a URL or pass in a raw data as a string.
atlas.io.read('https://nonCorsDomain.example.com/mySuperCoolData.xml', {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

Демо ниже показывает, как читать делимитированные файл и отобразить его на карте. В этом случае в коде используется файл CSV с пространственными столбцов данных.

<br/>

<iframe height='500' scrolling='no' title='Добавление файла с лимитированным' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Смотрите Pen <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>Добавить делимитированный файл</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>azure Maps ( ) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="write-spatial-data"></a>Запись пространственных данных

В пространственном модуле IO есть две основные функции записи. Функция `atlas.io.write` генерирует строку, в `atlas.io.writeCompressed` то время как функция генерирует сжатый почтовый файл. Сжатый почтовый файл будет содержать текстовый файл с пространственными данными в нем. Обе эти функции возвращают обещание добавить данные в файл. И, они оба могут написать `SpatialDataSet`любой из следующих данных: , `DataSource`, `ImageLayer`, `OgcMapLayer`коллекция функций, функция, геометрия, или массив любой комбинации этих типов данных. При написании с помощью обеих функций можно указать нужный формат файла. Если формат файла не указан, то данные будут записаны как KML.

Инструмент ниже демонстрирует большинство вариантов записи, которые `atlas.io.write` могут быть использованы с функцией.

<br/>

<iframe height='700' scrolling='no' title='Параметры записи пространственных данных' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Смотрите <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>параметры записи пространственных данных</a> Pen<a href='https://codepen.io/azuremaps'>@azuremaps</a>по картам Azure Maps () на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="example-of-writing-spatial-data"></a>Пример написания пространственных данных

Следующий пример позволяет перетащить и упасть, а затем загрузить пространственные файлы на карте. Вы можете экспортировать данные GeoJSON с карты и записывать их в одном из поддерживаемых форматов пространственных данных в виде строки или в виде сжатого файла.

<br/>

<iframe height='700' scrolling='no' title='Перетащите и отбросьте пространственные файлы на карту' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Смотрите Pen <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>Drag и падение пространственных файлов на</a> карту<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

Вы можете по желанию предоставить прокси-сервис для доступа к кроссдоменным ресурсам, которые могут не иметь включенного CORS. Этот фрагмент кода показывает, что вы можете включить прокси-сервис:

```javascript
atlas.io.read(data, {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(
    //Success
    function(r) {
        //some code goes here ...
    }
);
```

## <a name="read-and-write-well-known-text-wkt"></a>Читать и писать известный текст (WKT)

[Известный текст](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (WKT) является стандартом Открытого геопространственного консорциума (OGC) для представления пространственных геометрий в виде текста. Многие геопространственные системы поддерживают WKT, такие как Azure S'L и Azure PostgreS'L с помощью плагина PostGIS. Как и большинство стандартов OGC, координаты отформатированы как "длинная широта", чтобы привести их в соответствие с конвенцией "x y". Например, точка при долготе -110 и широте 45 может быть написана как `POINT(-110 45)` с использованием формата WKT.

Известный текст можно прочитать `atlas.io.ogc.WKT.read` с помощью `atlas.io.ogc.WKT.write` функции и написать с помощью функции.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Примеры чтения и письма Хорошо известный текст (WKT)

Следующий код показывает, как читать известную строку `POINT(-122.34009 47.60995)` текста и отобразить ее на карте с помощью слоя пузыря.

<br/>

<iframe height='500' scrolling='no' title='Читайте хорошо известный текст' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Смотрите Pen <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>Читать хорошо известный</a> текст<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

Следующий код демонстрирует чтение и написание хорошо известного текста взад и вперед.

<br/>

<iframe height='700' scrolling='no' title='Читать и писать известный текст' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Смотрите Pen <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>Читать и писать известный текст</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="read-and-write-gml"></a>Читать и писать GML

GML — это пространственная спецификация файлов XML, которая часто используется в качестве расширения для других спецификаций XML. Данные GeoJSON могут быть записаны как XML с Тегами GML с помощью функции. `atlas.io.core.GmlWriter.write` XML, содержащий GML, можно `atlas.io.core.GmlReader.read` прочитать с помощью функции. Функция чтения имеет два варианта:

- Вариант `isAxisOrderLonLat` - порядок координат оси "широта, долгота" или "долгота, широта" может варьироваться в зависимости от набора данных, и это не всегда четко определены. По умолчанию читатель GML читает данные координат как "широту, долготу", но установка этой опции на истину будет читать его как "долгота, широта".
- Опция `propertyTypes` - Эта опция представляет собой таблицу поиска ключевого значения, где ключом является имя свойства в наборе данных. Значение — это тип объекта, на который можно отбросить значение при разборе. Значения поддерживаемого типа: `string` `number`, `boolean`, `date`и . Если свойствнее не в таблице поиска или тип не определен, свойство будет разобрано как строка.

Функция `atlas.io.read` будет по `atlas.io.core.GmlReader.read` умолчанию выполнять функцию, когда обнаруживает, что входные данные являются XML, но данные не являются одним из других форматов поддержки пространственного XML.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [atlas.io статические функции](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

> [!div class="nextstepaction"]
> [ПространственныйНабор данных](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

> [!div class="nextstepaction"]
> [ПространственныеDataSetStats](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

> [!div class="nextstepaction"]
> [GmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [GmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [функции atlas.io.ogc.WKT](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Добавление слоя карты OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Подключение к службе WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Кредитное использование основных операций](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Детали формата поддерживаемых данных](spatial-io-supported-data-format-details.md)
