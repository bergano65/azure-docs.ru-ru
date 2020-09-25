---
title: Сведения о поддерживаемых форматах данных | Карты Microsoft Azure
description: Сведения о том, как разделенные пространственные данные анализируются в модуле пространственного ввода-вывода.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7227813f607ca18ee50f503a30b290414f333e21
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91310175"
---
# <a name="supported-data-format-details"></a>Сведения о поддерживаемых форматах данных

Эта статья содержит сведения о поддержке чтения и записи для всех XML-тегов и хорошо известных типов геометрических объектов Text. В нем также подробно описано, как разбираются пространственные данные с разделителями в модуле пространственного ввода-вывода.

## <a name="supported-xml-namespaces"></a>Поддерживаемые пространства имен XML

Модуль пространственных ввода-вывода поддерживает теги XML из следующих пространств имен.

| Префикс пространства имен | Универсальный код ресурса (URI) пространства имен   | Примечания                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | Поддержка только для чтения в файлах Жеорсс.           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | Поддержка только для чтения в файлах Жеорсс.                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | Поддержка только для чтения в файлах GPX). Выполняет синтаксический анализ и использует Дисплайколор. Все остальные свойства, добавленные в метаданные фигуры. |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | Поддерживается в файлах GPX). Использует цвет линии. |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | Только для чтения. Жеорсс выполняет запись с использованием формата Atom.              |

## <a name="supported-xml-elements"></a>Поддерживаемые XML-элементы

Модуль пространственных ввода-вывода поддерживает следующие элементы XML. Все XML-теги, которые не поддерживаются, будут преобразованы в объект JSON. Затем каждый тег будет добавлен как свойство в `properties` поле родительской фигуры или слоя.

### <a name="kml-elements"></a>Элементы КМЛ

Модуль пространственных ввода-вывода поддерживает следующие элементы КМЛ.

| Имя элемента         | Чтение    | запись   | Примечания                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | partial | да     | Объект анализируется, но не используется для позиционирования фигуры.                                                                    |
| `AddressDetails`     | partial | нет      | Объект анализируется, но не используется для позиционирования фигуры.                                                                    |
| `atom:author`        | да     | да     |                                                                                                                            |
| `atom:link`          | да     | да     |                                                                                                                            |
| `atom:name`          | да     | да     |                                                                                                                            |
| `BalloonStyle`       | partial | partial | `displayMode` не поддерживается. Преобразуется в `PopupTemplate` . Для записи добавьте свойство в `popupTemplate` качестве свойства компонента, для которого необходимо создать запись. |
| `begin`              | да     | да     |                                                                                                                            |
| `color`              | да     | да     | Включает `#AABBGGRR` и `#BBGGRR` . Синтаксический анализ в строке цвета CSS                                                           |
| `colorMode`          | да     | нет      |                                                                                                                            |
| `coordinates`        | да     | да     |                                                                                                                            |
| `Data`               | да     | да     |                                                                                                                            |
| `description`        | да     | да     |                                                                                                                            |
| `displayName`        | да     | да     |                                                                                                                            |
| `Document`           | да     | да     |                                                                                                                            |
| `drawOrder`          | partial | нет      | Чтение для наложения земельных участков и использование для их сортировки. 
| `east`               | да     | да     |                                                                                                                            |
| `end`                | да     | да     |                                                                                                                            |
| `ExtendedData`       | да     | да     | Поддерживает нетипизированные `Data` , `SimpleData` или `Schema` и, а также замены сущностей в форме `$[dataName]` .                      |
| `extrude`            | partial | partial | Поддерживается только для многоугольников. Многообъектная геометрия с многоугольниками разной высоты будет разбита на отдельные функции. Стили линий не поддерживаются. Многоугольники с высотой 0 будут подготавливаться к просмотру как плоский многоугольник. При чтении высота первой координаты наружного кольца будет добавлена в качестве свойства высоты многоугольника. Затем для отрисовки многоугольника на карте будет использоваться высота первой координаты. |
| `fill`               | да     | да     |                                                                                                                            |
| `Folder`             | да     | да     |                                                                                                                            |
| `GroundOverlay`      | да     | да     | `color` не поддерживается                                                                                                   |
| `heading`            | partial | нет      | Анализируется, но не подготавливается к просмотру `SimpleDataLayer` . Выполняет запись только в том случае, если данные хранятся в свойстве фигуры.                 |
| `hotSpot`            | да     | partial | Выполняет запись только в том случае, если данные хранятся в свойстве фигуры. Единицы выводятся только как "Пиксели".                         |
| `href`               | да     | да     |                                                                                                                            |
| `Icon`               | partial | partial | Анализируется, но не подготавливается к просмотру `SimpleDataLayer` . Записывает только свойство Icon фигуры, если оно содержит данные URI. Поддерживается только `href`. |
| `IconStyle`          | partial | partial | `icon``heading`значения,, `colorMode` и `hotspots` анализируются, но не отображаются`SimpleDataLayer`         |
| `innerBoundaryIs`    | да     | да     |                                                                                                                            |
| `kml`                | да     | да     |                                                                                                                            |
| `LabelStyle`         | нет      | нет      |                                                                                                                            |
| `LatLonBox`          | да     | да     |                                                                                                                            |
| `gx:LatLonQuad`      | да     | да     |                                                                                                                            |
| `LinearRing`         | да     | да     |                                                                                                                            |
| `LineString`         | да     | да     |                                                                                                                            |
| `LineStyle`          | да     | да     | `colorMode` не поддерживается.                                                                                         |
| `Link`               | да     | нет      | `href`Для сетевых соединений поддерживается только свойство.                                                                   |
| `MultiGeometry`      | partial | partial | Может быть разбит на отдельные компоненты при чтении.                                                                     |
| `name`               | да     | да     |                                                                                                                            |
| `NetworkLink`        | да     | нет      | Ссылки должны находиться в том же домене, что и документ.                                                                  |
| `NetworkLinkControl` | нет      | нет      |                                                                                                                            |
| `north`              | да     | да     |                                                                                                                            |
| `open`               | да     | да     |                                                                                                                            |
| `outerBoundaryIs`    | да     | да     |                                                                                                                            |
| `outline`            | да     | да     |                                                                                                                            |
| `overlayXY`          | нет      | нет      |                                                                                                                            |
| `Pair`               | partial | нет      | Поддерживается только `normal` стиль в `StyleMap` . `highlight` не поддерживается.                                   |
| `phoneNumber`        | да     | да     |                                                                                                                            |
| `PhotoOverlay`       | нет      | нет      |                                                                                                                            |
| `Placemark`          | да     | да     |                                                                                                                            |
| `Point`              | да     | да     |                                                                                                                            |
| `Polygon`            | да     | да     |                                                                                                                            |
| `PolyStyle`          | да     | да     |                                                                                                                            |
| `Region`             | partial | partial | `LatLongBox` поддерживается на уровне документа.                                                                      |
| `rotation`           | нет      | нет      |                                                                                                                            |
| `rotationXY`         | нет      | нет      |                                                                                                                            |
| `scale`              | нет      | нет      |                                                                                                                            |
| `Schema`             | да     | да     |                                                                                                                            |
| `SchemaData`         | да     | да     |                                                                                                                            |
| `schemaUrl`          | partial | да     | Не поддерживает загрузку стилей из внешних документов, которые не входят в КМЗ.                             |
| `ScreenOverlay`      | нет      | нет      |                                                                                                                            |
| `screenXY`           | нет      | нет      |                                                                                                                            |
| `SimpleData`         | да     | да     |                                                                                                                            |
| `SimpleField`        | да     | да     |                                                                                                                            |
| `size`               | нет      | нет      |                                                                                                                            |
| `Snippet`            | partial | partial | `maxLines` атрибут не учитывается.                                                                                  |
| `south`              | да     | да     |                                                                                                                            |
| `Style`              | да     | да     |                                                                                                                            |
| `StyleMap`           | partial | нет      | Поддерживается только стиль "Стандартный" в `StyleMap` .                                                                        |
| `styleUrl`           | partial | да     | URL-адреса внешних стилей не поддерживаются.                                                                         |
| `text`               | да     | да     | Замена `$[geDirections]` не поддерживается                                                                          |
| `textColor`          | да     | да     |                                                                                                                            |
| `TimeSpan`           | да     | да     |                                                                                                                            |
| `TimeStamp`          | да     | да     |                                                                                                                            |
| `value`              | да     | да     |                                                                                                                            |
| `viewRefreshMode`    | partial | нет      |  При наведении указателя на службу WMS поддерживается только `onStop` для наложения заземления. Будет добавляться `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` к URL-адресу и обновляться при перемещении схемы.  |
| `visibility`         | да     | да     |                                                                                                                            |
| `west`               | да     | да     |                                                                                                                            |
| `when`               | да     | да     |                                                                                                                            |
| `width`              | да     | да     |                                                                                                                            |

### <a name="georss-elements"></a>Элементы Жеорсс

Модуль пространственных ввода-вывода поддерживает следующие элементы Жеорсс.

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
| `geo:lat`                | да     | нет    | Написано как `georss:point` .                                                                   |
| `geo:lon`                | да     | нет    | Написано как `georss:point` .                                                                   |
| `geo:long`               | да     | нет    | Написано как `georss:point` .                                                                   |
| `georss:box`             | да     | нет    | Прочтите как многоугольник и задается `subType` свойство "Rectangle"                                |
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
| `geourl:latitude`        | да     | нет    | Написано как `georss:point` .                                                                   |
| `geourl:longitude`       | да     | нет    | Написано как `georss:point` .                                                                   |
| `position`               | да     | нет    | Некоторые XML-каналы заключают GML с помощью тега расположения вместо того, чтобы упаковывать его с помощью `georss:where` тега. Будет считать этот тег, но будет записан с помощью `georss:where` тега. |
| `rss`                    | да     | нет    | Жеорсс, написанный в формате ATOM.                                                                 |
| `rss:author`             | да     | partial | Написано как `atom:author` .                                                                 |
| `rss:category`           | да     | partial | Написано как `atom:category` .                                                               |
| `rss:channel`            | да     | нет    |                                                                                                |
| `rss:cloud`              | да     | нет    |                                                                                                |
| `rss:comments`           | да     | нет    |                                                                                                |
| `rss:copyright`          | да     | partial | Записывается как `atom:rights` свойство, если у фигуры `rights` `properties` еще нет свойства.       |
| `rss:description`        | да     | partial | Записывается как `atom:content` свойство, если у фигуры `content` `properties` еще нет свойства.      |
| `rss:docs`               | да     | нет    |                                                                                                |
| `rss:enclosure`          | да     | нет    |                                                                                                |
| `rss:generator`          | да     | нет    |                                                                                                |
| `rss:guid`               | да     | partial | Записывается как `atom:id` свойство, если у фигуры `id` `properties` еще нет свойства.         |
| `rss:image`              | да     | partial | Записывается как `atom:logo` свойство, если у фигуры `logo` `properties` еще нет свойства.      |
| `rss:item`               | да     | partial | Написано как `atom:entry` .                                                                  |
| `rss:language`           | да     | нет    |                                                                                                |
| `rss:lastBuildDate`      | да     | partial | Записывается как `atom:updated` свойство, если у фигуры `updated` `properties` еще нет свойства.     |
| `rss:link`               | да     | partial | Написано как `atom:link` .                                                                   |
| `rss:managingEditor`     | да     | partial | Написано как `atom:contributor` .                                                            |
| `rss:pubDate`            | да     | partial | Записывается как `atom:published` свойство, если у фигуры `published` `properties` еще нет свойства.  |
| `rss:rating`             | да     | нет    |                                                                                                |
| `rss:skipDays`           | да     | нет    |                                                                                                |
| `rss:skipHours`          | да     | нет    |                                                                                                |
| `rss:source`             | да     | partial | Записывается как объект, `atom:source` содержащий `atom:link` .                                       |
| `rss:textInput`          | да     | нет    |                                                                                                |
| `rss:title`              | да     | partial | Написано как `atom:title` .                                                                  |
| `rss:ttl`                | да     | нет    |                                                                                                |
| `rss:webMaster`          | да     | нет    |                                                                                                |

### <a name="gml-elements"></a>Элементы GML

Модуль пространственных ввода-вывода поддерживает следующие элементы GML. 

| Имя элемента            | Чтение | запись | Примечания                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | да  | нет    | Написано как `gml:posList` .                                                              |
| `gml:curveMember`       | да  | нет    |                                                                                        |
| `gml:curveMembers`      | да  | нет    |                                                                                        |
| `gml:Box`               | да  | нет    | Написано как `gml:Envelope` .                                                             |
| `gml:description`       | да  | да   |                                                                                        |
| `gml:Envelope`          | да  | да   |                                                                                        |
| `gml:exterior`          | да  | да   |                                                                                        |
| `gml:Feature`           | да  | нет    | Записывается как фигура.                                                                    |
| `gml:FeatureCollection` | да  | нет    | Записывается как коллекция Geometry.                                                      |
| `gml:featureMember`     | да  | нет    | Записывается как коллекция Geometry.                                                      |
| `gml:geometry`          | да  | нет    | Записывается как фигура.                                                                    |
| `gml:geometryMember`    | да  | да   |                                                                                        |
| `gml:geometryMembers`   | да  | да   |                                                                                        |
| `gml:identifier`        | да  | да   |                                                                                        |
| `gml:innerBoundaryIs`   | да  | нет    | Написано с помощью `gml.interior` .                                                          |
| `gml:interior`          | да  | да   |                                                                                        |
| `gml:LinearRing`        | да  | да   |                                                                                        |
| `gml:LineString`        | да  | да   |                                                                                        |
| `gml:lineStringMember`  | да  | да   |                                                                                        |
| `gml:lineStringMembers` | да  | нет    |                                                                                        |
| `gml:MultiCurve`        | да  | нет    | Считывает только `gml:LineString` элементы. Написано как `gml.MultiLineString`                  |
| `gml:MultiGeometry`     | partial  | partial   | Только чтение в качестве Феатуреколлектион.                                              |
| `gml:MultiLineString`   | да  | да   |                                                                                        |
| `gml:MultiPoint`        | да  | да   |                                                                                        |
| `gml:MultiPolygon`      | да  | да   |                                                                                        |
| `gml:MultiSurface`      | да  | нет    | Считывает только `gml:Polygon` элементы. Написано как `gml.MultiPolygon`                        |
| `gml:name`              | да  | да   |                                                                                        |
| `gml:outerBoundaryIs`   | да  | нет    | Написано с помощью `gml.exterior` .                                                          |
| `gml:Point`             | да  | да   |                                                                                        |
| `gml:pointMember`       | да  | да   |                                                                                        |
| `gml:pointMembers`      | да  | нет    |                                                                                        |
| `gml:Polygon`           | да  | да   |                                                                                        |
| `gml:polygonMember`     | да  | да   |                                                                                        |
| `gml:polygonMembers`    | да  | нет    |                                                                                        |
| `gml:pos`               | да  | да   |                                                                                        |
| `gml:posList`           | да  | да   |                                                                                        |
| `gml:surfaceMember`     | да  | да   |                                                                                        |

#### <a name="additional-notes"></a>Дополнительные примечания

- Элементы-члены будут искать геометрию, которая может быть скрыта внутри дочерних элементов. Эта операция поиска необходима, так как многие форматы XML, которые расширяются из GML, могут не размещать геометрию как прямой дочерний элемент элемента Member.
- `srsName` частично поддерживается для координат WGS84 и следующих кодов:[EPSG: 4326](https://epsg.io/4326)) и веб-Меркатора ([EPSG: 3857:](https://epsg.io/3857) или одного из его альтернативных кодов. Любая другая система координат будет проанализирована как WGS84 "как есть".
- Если не указан при чтении веб-канала XML, порядок осей определяется на основе подсказок в веб-канале XML. Предпочтение задается в порядке оси "Широта, Долгота".
- Если для свойств при записи в GML-файл не задано пользовательское пространство имен GML, дополнительные сведения о свойствах добавляться не будут.

### <a name="gpx-elements"></a>Элементы GPX)

Модуль пространственных ввода-вывода поддерживает следующие элементы GPX).

| Имя элемента             | Чтение    | запись   | Примечания                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | да     | да     |                                                                                             |
| `gpx:author`             | да     | да     |                                                                                             |
| `gpx:bounds`             | да     | да     | Преобразуется в Локатионрект при чтении.                                                    |
| `gpx:cmt`                | да     | да     |                                                                                             |
| `gpx:copyright`          | да     | да     |                                                                                             |
| `gpx:desc`               | да     | да     | Копируется в свойство Description при чтении для согласования с другими XML-форматами.               |
| `gpx:dgpsid`             | да     | да     |                                                                                             |
| `gpx:ele`                | да     | да     |                                                                                             |
| `gpx:extensions`         | partial | partial | При чтении извлекаются сведения о стиле. Все остальные расширения будут сведены в простой объект JSON. Записываются только сведения о стиле фигуры. |
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
| `gpx:sym`                | да     | да     | Значение захватывается, но не используется для изменения значка канцелярской кнопки.                               |
| `gpx:text`               | да     | да     |                                                                                             |
| `gpx:time`               | да     | да     |                                                                                             |
| `gpx:trk`                | да     | да     |                                                                                             |
| `gpx:trkpt`              | да     | да     |                                                                                             |
| `gpx:trkseg`             | да     | да     |                                                                                             |
| `gpx:type`               | да     | да     |                                                                                             |
| `gpx:vdop`               | да     | да     |                                                                                             |
| `gpx:wpt`                | да     | да     |                                                                                             |
| `gpx_style:color`        | да     | да     |                                                                                             |
| `gpx_style:line`         | partial | partial | `color``opacity` `width` `lineCap` поддерживаются.                                           |
| `gpx_style:opacity`      | да     | да     |                                                                                             |
| `gpx_style:width`        | да     | да     |                                                                                             |
| `gpxx:DisplayColor`      | да     | нет      | Используется для указания цвета фигуры. При записи `gpx_style:line` вместо него будет использоваться цвет.  |
| `gpxx:RouteExtension`    | partial | нет      | Все свойства считываются в `properties` . Используется только `DisplayColor`.                     |
| `gpxx:TrackExtension`    | partial | нет      | Все свойства считываются в `properties` . Используется только `DisplayColor`.                     |
| `gpxx:WaypointExtension` | partial | нет      | Все свойства считываются в `properties` . Используется только `DisplayColor`.                     |
| `gpx:keywords`           | да     | да     |                                                                                             |
| `gpx:fix`                | да     | да     |                                                                                             |

#### <a name="additional-notes"></a>Дополнительные примечания

При записи;

- Многоточечные точки будут разбиваться на отдельные вайпоинтс.
- Многоугольники и многомногоугольники будут записаны как дорожки. 
  
## <a name="supported-well-known-text-geometry-types"></a>Поддерживаемые хорошо известные типы геометрических объектов Text

| Тип геометрии | Чтение | запись |
|--------------|:----:|:-----:|
| ТОЧКИ | x | x |
| ТОЧКА Z | x | x | 
| ТОЧКА M | x | x<sup>[2]</sup> |
| ТОЧКА ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| LINESTRING | x | x |
| LINESTRING Z | x | x | 
| LINESTRING М | x | x<sup>[2]</sup> |
| LINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| ФИГУРЫ | x | x |
| МНОГОУГОЛЬНИК Z | x | x |
| МНОГОУГОЛЬНИК M | x | x<sup>[2]</sup> |
| МНОГОУГОЛЬНИК ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOINT | x | x |
| MULTIPOINT Z | x | x | 
| MULTIPOINT M | x | x<sup>[2]</sup> |
| ПОМУЛТИПОИНТИНТ ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTILINESTRING | x | x |
| MULTILINESTRING Z | x | x | 
| MULTILINESTRING М | x | x<sup>[2]</sup> |
| MULTILINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOLYGON | x | x |
| МНОГОУГОЛЬНЫЙ Z | x | x | 
| МНОГОУГОЛЬНАЯ M | x | x<sup>[2]</sup> |
| МНОГОУГОЛЬНЫЙ ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| GEOMETRYCOLLECTION | x | x |
| GEOMETRYCOLLECTION Z | x | x | 
| GEOMETRYCOLLECTION М | x | x<sup>[2]</sup> | 
| GEOMETRYCOLLECTION ZM | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1 \] только параметр Z захватывается и добавляется в качестве третьего значения в value.

\[\]параметр 2 M не захвачен.

## <a name="delimited-spatial-data-support"></a>Поддержка пространственных данных с разделителями

Разделенные пространственные данные, такие как файлы значений с разделителями-запятыми (CSV), часто имеют столбцы, содержащие пространственные данные. Например, могут быть столбцы, содержащие сведения широты и долготы. В хорошо известном текстовом формате может существовать столбец, содержащий данные пространственных геометрических объектов.

### <a name="spatial-data-column-detection"></a>Обнаружение столбцов пространственных данных

При чтении файла с разделителями, содержащего пространственные данные, заголовок будет проанализирован для определения столбцов, содержащих поля расположения. Если заголовок содержит сведения о типе, он будет использоваться для приведения значений ячеек к соответствующему типу. Если заголовок не указан, первая строка будет проанализирована и использована для создания заголовка. При анализе первой строки выполняется проверка, чтобы сопоставлять имена столбцов со следующими именами без учета регистра. Порядок имен является приоритетом, если в файле существует два или более имен.

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

Первая строка данных будет проверяться на наличие строк в известном текстовом формате. 

### <a name="delimited-data-column-types"></a>Типы столбцов данных с разделителями

При сканировании строки заголовка все сведения о типе, находящееся в имени столбца, будут извлечены и использованы для приведения ячеек в этом столбце. Ниже приведен пример имени столбца, имеющего значение типа: "ColumnName (typeName)". Поддерживаются следующие имена типов без учета регистра:

#### <a name="numbers"></a>С помощью чисел

- EDM. Int64
- INT
- long
- EDM. Double
- FLOAT
- double
- number

#### <a name="booleans"></a>Boolean

- EDM. Boolean
- bool
- Логическое

#### <a name="dates"></a>Даты

- EDM. DateTime
- Дата
- DATETIME

#### <a name="geography"></a>Geography

- модель EDM. geography
- geography

#### <a name="strings"></a>Строки

- EDM. String
- varchar
- text
- строка

Если сведения о типе не могут быть извлечены из заголовка, а при чтении включен параметр динамической типизации, каждая ячейка будет анализироваться отдельно, чтобы определить, какой тип данных лучше всего использовать для приведения.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

[Чтение и запись пространственных данных](spatial-io-read-write-spatial-data.md)
