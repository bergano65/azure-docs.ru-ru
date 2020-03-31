---
title: Детали формата поддерживаемых данных Карты Microsoft Azure
description: Узнайте, как разграничение пространственных данных разбирается в пространственном модуле IO.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3353620f1751e939a04543115fe704555fb3bc21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334079"
---
# <a name="supported-data-format-details"></a>Детали формата поддерживаемых данных

В этой статье приводится подробная информация о поддержке чтения и записи для всех меток XML и хорошо известных типов геометрии текста. В нем также подробно описывается, как разграниченные пространственные данные разбираются в пространственном модуле IO.

## <a name="supported-xml-namespaces"></a>Поддерживаемые пространства имен XML

Модуль пространственного IO поддерживает теги XML из следующих областей имен.

| Префикс пространства имен | Универсальный код ресурса (URI) пространства имен   | Примечания                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | Прочитайте только поддержку в файлах GeoRSS.           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | Прочитайте только поддержку в файлах GeoRSS.                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | Прочитайте только поддержку в файлах GPX. Парс и использует DisplayColor. Все остальные свойства добавлены в форму метаданных. |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | Поддерживается в файлах GPX. Использует цвет линии. |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | Только для чтения. GeoRSS пишет с помощью формата Atom.              |

## <a name="supported-xml-elements"></a>Поддерживаемые элементы XML

Модуль пространственного IO поддерживает следующие элементы XML. Любые теги XML, которые не поддерживаются, будут преобразованы в объект JSON. Затем каждый тег будет добавлен в `properties` качестве свойства в поле родительской формы или слоя.

### <a name="kml-elements"></a>Элементы KML

Пространственный модуль IO поддерживает следующие элементы KML.

| Имя элемента         | Чтение    | запись   | Примечания                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | частично | да     | Объект разбирается, но не используется для позиционирования формы.                                                                    |
| `AddressDetails`     | частично | нет      | Объект разбирается, но не используется для позиционирования формы.                                                                    |
| `atom:author`        | да     | да     |                                                                                                                            |
| `atom:link`          | да     | да     |                                                                                                                            |
| `atom:name`          | да     | да     |                                                                                                                            |
| `BalloonStyle`       | частично | частично | `displayMode` не поддерживается. Преобразовано `PopupTemplate`в . Чтобы написать, `popupTemplate` добавьте свойство в качестве свойства функции, для которую вы хотите написать его. |
| `begin`              | да     | да     |                                                                                                                            |
| `color`              | да     | да     | Включает `#AABBGGRR` `#BBGGRR`в себя и . Разбор в строку цвета CSS                                                           |
| `colorMode`          | да     | нет      |                                                                                                                            |
| `coordinates`        | да     | да     |                                                                                                                            |
| `Data`               | да     | да     |                                                                                                                            |
| `description`        | да     | да     |                                                                                                                            |
| `displayName`        | да     | да     |                                                                                                                            |
| `Document`           | да     | да     |                                                                                                                            |
| `drawOrder`          | частично | нет      | Читайте для наземных накладок и используется для их сортировки. 
| `east`               | да     | да     |                                                                                                                            |
| `end`                | да     | да     |                                                                                                                            |
| `ExtendedData`       | да     | да     | Поддерживает `Data`нетипидированные, `SimpleData` или, `Schema`и `$[dataName]`сущность замены формы.                      |
| `extrude`            | частично | частично | Поддерживается только для полигонов. Мультигеометрия, имеющий полигоны разной высоты, будет разбита на отдельные черты. Стили строк не поддерживаются. Полигоны с высотой 0 будут отображаться как плоский полигон. При чтении высота первой координаты во внешнем кольце будет добавлена в качестве высотного свойства полигона. Затем высота первой координаты будет использоваться для визуализации полигона на карте. |
| `fill`               | да     | да     |                                                                                                                            |
| `Folder`             | да     | да     |                                                                                                                            |
| `GroundOverlay`      | да     | да     | `color`не поддерживается                                                                                                   |
| `heading`            | частично | нет      | Разбор, но не оказанный `SimpleDataLayer`. Только записывает, если данные хранятся в свойстве формы.                 |
| `hotSpot`            | да     | частично | Только записывает, если данные хранятся в свойстве формы. Единицы вывешиваются только как "пиксели".                         |
| `href`               | да     | да     |                                                                                                                            |
| `Icon`               | частично | частично | Разбор, но не оказанный `SimpleDataLayer`. Только пишет свойство значка формы, если она содержит данные URI. Поддерживается только `href`. |
| `IconStyle`          | частично | частично | `icon`, `heading` `colorMode`, `hotspots` , и значения разбираются, но они не отображаются`SimpleDataLayer`         |
| `innerBoundaryIs`    | да     | да     |                                                                                                                            |
| `kml`                | да     | да     |                                                                                                                            |
| `LabelStyle`         | нет      | нет      |                                                                                                                            |
| `LatLonBox`          | да     | да     |                                                                                                                            |
| `gx:LatLonQuad`      | да     | да     |                                                                                                                            |
| `LinearRing`         | да     | да     |                                                                                                                            |
| `LineString`         | да     | да     |                                                                                                                            |
| `LineStyle`          | да     | да     | `colorMode` не поддерживается.                                                                                         |
| `Link`               | да     | нет      | Только `href` свойство поддерживается для сетевых ссылок.                                                                   |
| `MultiGeometry`      | частично | частично | Может быть разбит на отдельные функции при чтении.                                                                     |
| `name`               | да     | да     |                                                                                                                            |
| `NetworkLink`        | да     | нет      | Ссылки должны быть на том же домене, что и документ.                                                                  |
| `NetworkLinkControl` | нет      | нет      |                                                                                                                            |
| `north`              | да     | да     |                                                                                                                            |
| `open`               | да     | да     |                                                                                                                            |
| `outerBoundaryIs`    | да     | да     |                                                                                                                            |
| `outline`            | да     | да     |                                                                                                                            |
| `overlayXY`          | нет      | нет      |                                                                                                                            |
| `Pair`               | частично | нет      | Поддерживается `normal` только `StyleMap` стиль в а. `highlight` не поддерживается.                                   |
| `phoneNumber`        | да     | да     |                                                                                                                            |
| `PhotoOverlay`       | нет      | нет      |                                                                                                                            |
| `Placemark`          | да     | да     |                                                                                                                            |
| `Point`              | да     | да     |                                                                                                                            |
| `Polygon`            | да     | да     |                                                                                                                            |
| `PolyStyle`          | да     | да     |                                                                                                                            |
| `Region`             | частично | частично | `LatLongBox`поддерживается на уровне документа.                                                                      |
| `rotation`           | нет      | нет      |                                                                                                                            |
| `rotationXY`         | нет      | нет      |                                                                                                                            |
| `scale`              | нет      | нет      |                                                                                                                            |
| `Schema`             | да     | да     |                                                                                                                            |
| `SchemaData`         | да     | да     |                                                                                                                            |
| `schemaUrl`          | частично | да     | Не поддерживает стили загрузки из внешних документов, которые не включены в КМЗ.                             |
| `ScreenOverlay`      | нет      | нет      |                                                                                                                            |
| `screenXY`           | нет      | нет      |                                                                                                                            |
| `SimpleData`         | да     | да     |                                                                                                                            |
| `SimpleField`        | да     | да     |                                                                                                                            |
| `size`               | нет      | нет      |                                                                                                                            |
| `Snippet`            | частично | частично | `maxLines`атрибут игнорируется.                                                                                  |
| `south`              | да     | да     |                                                                                                                            |
| `Style`              | да     | да     |                                                                                                                            |
| `StyleMap`           | частично | нет      | Поддерживается только нормальный стиль в а. `StyleMap`                                                                        |
| `styleUrl`           | частично | да     | Внешние URL-адреса стиля не поддерживаются.                                                                         |
| `text`               | да     | да     | Замена `$[geDirections]` не поддерживается                                                                          |
| `textColor`          | да     | да     |                                                                                                                            |
| `TimeSpan`           | да     | да     |                                                                                                                            |
| `TimeStamp`          | да     | да     |                                                                                                                            |
| `value`              | да     | да     |                                                                                                                            |
| `viewRefreshMode`    | частично | нет      |  Если указывая на услугу `onStop` WMS, то поддерживается только для наземных накладок. Будет придатиться `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` к URL и обновлять по мере движения карты.  |
| `visibility`         | да     | да     |                                                                                                                            |
| `west`               | да     | да     |                                                                                                                            |
| `when`               | да     | да     |                                                                                                                            |
| `width`              | да     | да     |                                                                                                                            |

### <a name="georss-elements"></a>Элементы GeoRSS

Пространственный модуль IO поддерживает следующие элементы GeoRSS.

| Имя элемента             | Чтение    | запись | Примечания                                                                                          |
|--------------------------|---------|-------|------------------------------------------------------------------------------------------------|
| `atom:author`            | да     | да   |                                                                                                |
| `atom:category`          | да     | да   |                                                                                                |
| `atom:content`           | да     | да   |                                                                                                |
| `atom:contributor`       | да     | да   |                                                                                                |
| `atom:email`             | да     | да   |                                                                                                |
| `atom:entry`             | да     | да   |                                                                                                |
| `atom:feed`              | да     | да   |                                                                                                |
| `atom:icon`              | да     | да   |                                                                                                |
| `atom:id`                | да     | да   |                                                                                                |
| `atom:link`              | да     | да   |                                                                                                |
| `atom:logo`              | да     | да   |                                                                                                |
| `atom:name`              | да     | да   |                                                                                                |
| `atom:published`         | да     | да   |                                                                                                |
| `atom:rights`            | да     | да   |                                                                                                |
| `atom:source`            | да     | да   |                                                                                                |
| `atom:subtitle`          | да     | да   |                                                                                                |
| `atom:summary`           | да     | да   |                                                                                                |
| `atom:title`             | да     | да   |                                                                                                |
| `atom:updated`           | да     | да   |                                                                                                |
| `atom:uri`               | да     | да   |                                                                                                |
| `geo:lat`                | да     | нет    | Написано `georss:point`как .                                                                   |
| `geo:lon`                | да     | нет    | Написано `georss:point`как .                                                                   |
| `geo:long`               | да     | нет    | Написано `georss:point`как .                                                                   |
| `georss:box`             | да     | нет    | Читать как полигон и с `subType` учетом собственности "Rectangle"                                |
| `georss:circle`          | да     | да   |                                                                                                |
| `georss:elev`            | да     | да   |                                                                                                |
| `georss:featurename`     | да     | да   |                                                                                                |
| `georss:featuretypetag`  | да     | да   |                                                                                                |
| `georss:floor`           | да     | да   |                                                                                                |
| `georss:line`            | да     | да   |                                                                                                |
| `georss:point`           | да     | да   |                                                                                                |
| `georss:polygon`         | да     | да   |                                                                                                |
| `georss:radius`          | да     | да   |                                                                                                |
| `georss:relationshiptag` | да     | да   |                                                                                                |
| `georss:where`           | да     | да   |                                                                                                |
| `geourl:latitude`        | да     | нет    | Написано `georss:point`как .                                                                   |
| `geourl:longitude`       | да     | нет    | Написано `georss:point`как .                                                                   |
| `position`               | да     | нет    | Некоторые каналы XML будут обернуть GML с тегом `georss:where` положения вместо того, чтобы обернуть его тегом. Будет читать этот тег, но `georss:where` будет писать с помощью тега. |
| `rss`                    | да     | нет    | GeoRSS написанв в формате ATOM.                                                                 |
| `rss:author`             | да     | частично | Написано `atom:author`как .                                                                 |
| `rss:category`           | да     | частично | Написано `atom:category`как .                                                               |
| `rss:channel`            | да     | нет    |                                                                                                |
| `rss:cloud`              | да     | нет    |                                                                                                |
| `rss:comments`           | да     | нет    |                                                                                                |
| `rss:copyright`          | да     | частично | Написано `atom:rights` как форма if не `rights` `properties` имеет свойства уже.       |
| `rss:description`        | да     | частично | Написано `atom:content` как форма if не `content` `properties` имеет свойства уже.      |
| `rss:docs`               | да     | нет    |                                                                                                |
| `rss:enclosure`          | да     | нет    |                                                                                                |
| `rss:generator`          | да     | нет    |                                                                                                |
| `rss:guid`               | да     | частично | Написано `atom:id` как форма if не `id` `properties` имеет свойства уже.         |
| `rss:image`              | да     | частично | Написано `atom:logo` как форма if не `logo` `properties` имеет свойства уже.      |
| `rss:item`               | да     | частично | Написано `atom:entry`как .                                                                  |
| `rss:language`           | да     | нет    |                                                                                                |
| `rss:lastBuildDate`      | да     | частично | Написано `atom:updated` как форма if не `updated` `properties` имеет свойства уже.     |
| `rss:link`               | да     | частично | Написано `atom:link`как .                                                                   |
| `rss:managingEditor`     | да     | частично | Написано `atom:contributor`как .                                                            |
| `rss:pubDate`            | да     | частично | Написано `atom:published` как форма if не `published` `properties` имеет свойства уже.  |
| `rss:rating`             | да     | нет    |                                                                                                |
| `rss:skipDays`           | да     | нет    |                                                                                                |
| `rss:skipHours`          | да     | нет    |                                                                                                |
| `rss:source`             | да     | частично | Написано `atom:source` как `atom:link`содержащий .                                       |
| `rss:textInput`          | да     | нет    |                                                                                                |
| `rss:title`              | да     | частично | Написано `atom:title`как .                                                                  |
| `rss:ttl`                | да     | нет    |                                                                                                |
| `rss:webMaster`          | да     | нет    |                                                                                                |

### <a name="gml-elements"></a>Элементы GML

Модуль пространственного IO поддерживает следующие элементы GML. 

| Имя элемента            | Чтение | запись | Примечания                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | да  | нет    | Написано `gml:posList`как .                                                              |
| `gml:curveMember`       | да  | нет    |                                                                                        |
| `gml:curveMembers`      | да  | нет    |                                                                                        |
| `gml:Box`               | да  | нет    | Написано `gml:Envelope`как .                                                             |
| `gml:description`       | да  | да   |                                                                                        |
| `gml:Envelope`          | да  | да   |                                                                                        |
| `gml:exterior`          | да  | да   |                                                                                        |
| `gml:Feature`           | да  | нет    | Написано как форма.                                                                    |
| `gml:FeatureCollection` | да  | нет    | Написано как коллекция геометрии.                                                      |
| `gml:featureMember`     | да  | нет    | Написано как коллекция геометрии.                                                      |
| `gml:geometry`          | да  | нет    | Написано как форма.                                                                    |
| `gml:geometryMember`    | да  | да   |                                                                                        |
| `gml:geometryMembers`   | да  | да   |                                                                                        |
| `gml:identifier`        | да  | да   |                                                                                        |
| `gml:innerBoundaryIs`   | да  | нет    | Написано `gml.interior`с использованием .                                                          |
| `gml:interior`          | да  | да   |                                                                                        |
| `gml:LinearRing`        | да  | да   |                                                                                        |
| `gml:LineString`        | да  | да   |                                                                                        |
| `gml:lineStringMember`  | да  | да   |                                                                                        |
| `gml:lineStringMembers` | да  | нет    |                                                                                        |
| `gml:MultiCurve`        | да  | нет    | Только `gml:LineString` читает членов. Написано как`gml.MultiLineString`                  |
| `gml:MultiGeometry`     | частично  | частично   | Только читать как FeatureCollection.                                              |
| `gml:MultiLineString`   | да  | да   |                                                                                        |
| `gml:MultiPoint`        | да  | да   |                                                                                        |
| `gml:MultiPolygon`      | да  | да   |                                                                                        |
| `gml:MultiSurface`      | да  | нет    | Только `gml:Polygon` читает членов. Написано как`gml.MultiPolygon`                        |
| `gml:name`              | да  | да   |                                                                                        |
| `gml:outerBoundaryIs`   | да  | нет    | Написано `gml.exterior`с использованием .                                                          |
| `gml:Point`             | да  | да   |                                                                                        |
| `gml:pointMember`       | да  | да   |                                                                                        |
| `gml:pointMembers`      | да  | нет    |                                                                                        |
| `gml:Polygon`           | да  | да   |                                                                                        |
| `gml:polygonMember`     | да  | да   |                                                                                        |
| `gml:polygonMembers`    | да  | нет    |                                                                                        |
| `gml:pos`               | да  | да   |                                                                                        |
| `gml:posList`           | да  | да   |                                                                                        |
| `gml:surfaceMember`     | да  | да   |                                                                                        |

#### <a name="additional-notes"></a>дополнительные заметки

- Элементы участника будут искать геометрию, которая может быть похоронена в элементах ребенка. Эта операция поиска необходима, так как многие форматы XML, которые простираются от GML, не могут поместить геометрию в качестве непосредственного ребенка элемента-члена.
- `srsName`частично поддерживается для координат WGS84 и следующих кодов:[EPSG:4326](https://epsg.io/4326)) и веб-Mercator[(EPSG:3857](https://epsg.io/3857) или один из его альтернативных кодов. Любая другая система координат будет разогнана как WGS84 как есть.
- Если не указано при чтении корма XML, порядок оси определяется на основе подсказок в корме XML. Предпочтение отдается порядку оси "широта, долгота".
- Если для свойств при записи в файл GML не указано специальное пространство gML namespace, дополнительная информация о собственности не будет добавлена.

### <a name="gpx-elements"></a>Элементы GPX

Модуль пространственного IO поддерживает следующие элементы GPX.

| Имя элемента             | Чтение    | запись   | Примечания                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | да     | да     |                                                                                             |
| `gpx:author`             | да     | да     |                                                                                             |
| `gpx:bounds`             | да     | да     | Преобразовано в LocationRect при чтении.                                                    |
| `gpx:cmt`                | да     | да     |                                                                                             |
| `gpx:copyright`          | да     | да     |                                                                                             |
| `gpx:desc`               | да     | да     | Скопировано в свойство описания при чтении, чтобы привести их в соответствие с другими форматами XML.               |
| `gpx:dgpsid`             | да     | да     |                                                                                             |
| `gpx:ele`                | да     | да     |                                                                                             |
| `gpx:extensions`         | частично | частично | При чтении, информация о стиле извлекается. Все остальные расширения будут сплющены в простой объект JSON. Написана только информация о стиле формы. |
| `gpx:geoidheight`        | да     | да     |                                                                                             |
| `gpx:gpx`                | да     | да     |                                                                                             |
| `gpx:hdop`               | да     | да     |                                                                                             |
| `gpx:link`               | да     | да     |                                                                                             |
| `gpx:magvar`             | да     | да     |                                                                                             |
| `gpx:metadata`           | да     | да     |                                                                                             |
| `gpx:name`               | да     | да     |                                                                                             |
| `gpx:pdop`               | да     | да     |                                                                                             |
| `gpx:rte`                | да     | да     |                                                                                             |
| `gpx:rtept`              | да     | да     |                                                                                             |
| `gpx:sat`                | да     | да     |                                                                                             |
| `gpx:src`                | да     | да     |                                                                                             |
| `gpx:sym`                | да     | да     | Значение захвачено, но оно не используется для изменения значка pushpin.                               |
| `gpx:text`               | да     | да     |                                                                                             |
| `gpx:time`               | да     | да     |                                                                                             |
| `gpx:trk`                | да     | да     |                                                                                             |
| `gpx:trkpt`              | да     | да     |                                                                                             |
| `gpx:trkseg`             | да     | да     |                                                                                             |
| `gpx:type`               | да     | да     |                                                                                             |
| `gpx:vdop`               | да     | да     |                                                                                             |
| `gpx:wpt`                | да     | да     |                                                                                             |
| `gpx_style:color`        | да     | да     |                                                                                             |
| `gpx_style:line`         | частично | частично | `color`, `opacity` `width`, `lineCap` поддерживаются.                                           |
| `gpx_style:opacity`      | да     | да     |                                                                                             |
| `gpx_style:width`        | да     | да     |                                                                                             |
| `gpxx:DisplayColor`      | да     | нет      | Используется для определения цвета формы. При написании вместо `gpx_style:line` него будет использоваться цвет.  |
| `gpxx:RouteExtension`    | частично | нет      | Все свойства считываются в `properties`. Используется только `DisplayColor`.                     |
| `gpxx:TrackExtension`    | частично | нет      | Все свойства считываются в `properties`. Используется только `DisplayColor`.                     |
| `gpxx:WaypointExtension` | частично | нет      | Все свойства считываются в `properties`. Используется только `DisplayColor`.                     |
| `gpx:keywords`           | да     | да     |                                                                                             |
| `gpx:fix`                | да     | да     |                                                                                             |

#### <a name="additional-notes"></a>дополнительные заметки

При написании;

- MultiPoints будут разбиты на отдельные путевые точки.
- Полигоны и мультиполегоны будут написаны как треки. 
  
## <a name="supported-well-known-text-geometry-types"></a>Поддерживаемые хорошо известные типы геометрии текста

| Тип геометрии | Чтение | запись |
|--------------|:----:|:-----:|
| Точки | x | x |
| ПУЭнТ | x | x | 
| POINT M | x | х<sup>2»</sup> |
| ПУЭнТ ЗМ | х<sup>х.2»</sup><sup>[2]</sup> | | 
| Linestring | x | x |
| ЛИНИЯСТРИНГ | x | x | 
| ЛИНИЯСТОНТ М | x | х<sup>2»</sup> |
| ЛИНИЯСТРИНГ ЗМ | х<sup>х.2»</sup><sup>[2]</sup> | | 
| Полигона | x | x |
| ПОЛИГОН | x | x |
| ПОЛИГОН М | x | х<sup>2»</sup> |
| ПОЛИГОН ЗМ | х<sup>х.2»</sup><sup>[2]</sup> | | 
| Multipoint | x | x |
| MULTIPOINT (м.н.) | x | x | 
| MULTIPOINT M | x | х<sup>2»</sup> |
| ПОМОНЗАПИНТ ЗМ | х<sup>х.2»</sup><sup>[2]</sup> | | 
| МНОГОЛИНЕВАЯ СТРОКА | x | x |
| МУЛЬТИЛИНЕНЕСТРЕТг (МУЛЬТИЛИНЕНЕСТРЕ | x | x | 
| МУЛЬТИЛИНЕИНСТ М | x | х<sup>2»</sup> |
| МУЛЬТИЛИНЕИНСТ ЗМ | х<sup>х.2»</sup><sup>[2]</sup> | | 
| Multipolygon | x | x |
| МУЛЬТИПОЛИСОН | x | x | 
| МУЛЬТИПОЛИСОН М | x | х<sup>2»</sup> |
| МУЛЬТИПОЛИГОН ЗМ | х<sup>х.2»</sup><sup>[2]</sup> | | 
| GEOMETRYCOLLECTION | x | x |
| GEOMETRYCOLLECTION Z | x | x | 
| GEOMETRYCOLLECTION M | x | х<sup>2»</sup> | 
| GEOMETRYCOLLECTION ЗМ | х<sup>х.2»</sup><sup>[2]</sup> | x | 

\[1\] Только параметр «захват» и добавлен в качестве третьего значения в значении Position.

\[Параметр 2\] M не захвачен.

## <a name="delimited-spatial-data-support"></a>Разграниченная поддержка пространственных данных

Раздельное пространственное значение данных, таких как файлы значения, разделенные запятой (CSV), часто имеют столбцы, содержащие пространственные данные. Например, могут быть столбцы, содержащие информацию о широте и долготе. В хорошо известном формате текста может быть столбец, содержащий данные пространственной геометрии.

### <a name="spatial-data-column-detection"></a>Обнаружение пространственных столбцов данных

При чтении делимитированного файла, содержащего пространственные данные, будет проанализирован заголовок, чтобы определить, какие столбцы содержат поля расположения. Если заголовок содержит информацию о типе, он будет использоваться для отбрасывания значений ячейки в соответствующий тип. Если заголовок не указан, первый ряд будет проанализирован и использован для создания заголовка. При анализе первого ряда выполняется проверка, чтобы сопоставить имена столбцов со следующими именами в безчувственном случае. Порядок имен является приоритетным, если в файле есть два или более имен.

#### <a name="latitude"></a>Широта

- `latitude`
- `lat`
- `latdd`
- `lat_dd`
- `latitude83`
- `latdecdeg`
- `y`
- `ycenter`
- `point-y`

#### <a name="longitude"></a>Долгота

- `longitude`
- `lon`
- `lng`
- `long`
- `longdd`
- `long_dd`
- `longitude83`
- `longdecdeg`
- `x`
- `xcenter`
- `point-x`

#### <a name="elevation"></a>Elevation

- `elevation`
- `elv`
- `altitude`
- `alt`
- `z`

#### <a name="geography"></a>Geography

Первый ряд данных будет отсканирован для строк, которые находятся в формате Well-Known Text. 

### <a name="delimited-data-column-types"></a>Разграниченные типы столбцов данных

При сканировании строки заголовка любая информация типа, содержась в названии столбца, будет извлечена и использована для отбрасывания ячеек в этой колонке. Вот пример имени столбца, которое имеет значение типа: "ColumnName (typeName)". Поддерживаются следующие имена нечувствительных типов:

#### <a name="numbers"></a>Числа

- edm.int64
- INT
- long
- edm.double
- FLOAT
- double
- number

#### <a name="booleans"></a>Boolean

- edm.boolean
- bool
- Логическое

#### <a name="dates"></a>даты.

- edm.datetime
- Дата
- DATETIME

#### <a name="geography"></a>Geography

- edm.geography
- geography

#### <a name="strings"></a>Строки

- edm.string
- varchar
- text
- строка

Если информация типа не может быть извлечена из заголовка, и динамическая опция ввода включена при чтении, то каждая ячейка будет индивидуально проанализирована, чтобы определить, какой тип данных лучше всего подходит для отливания как.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Чтение и запись пространственных данных](spatial-io-read-write-spatial-data.md)
