---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 065cb4daa9501ee658d364dad43b9e03798e4083
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185701"
---
Задание создает выходной файл JSON c метаданными об обнаруженных и отслеживаемых лицах. Метаданные включают координаты, указывающие расположение лиц, а также идентификационный код лица, указывающий на отслеживание этого человека. Если лицо в анфас теряется или перекрывается в кадре, его идентификационный номер может быть сброшен, в результате чего нескольким людям назначаются несколько идентификаторов.

Выходной файл JSON включает следующие элементы:

### <a name="root-json-elements"></a>Элементы корневого JSON

| Элемент | Описание |
| --- | --- |
| version |Версия API видео. |
| timescale |Количество тактов в секунду видео. |
| offset |Смещение времени для отметки времени. В API видео версии 1.0 это значение всегда равно 0. В будущих поддерживаемых сценариях это значение может измениться. |
| width, hight |Ширина и высота выходного видеокадра в пикселях.|
| framerate |Количество кадров в секунду видео. |
| [fragments](#fragments-json-elements) |Метаданные разделены на разные сегменты, называемые фрагментами. Каждый фрагмент имеет начало, длительность, номер интервала и события. |

### <a name="fragments-json-elements"></a>Элементы JSON фрагментов

|Элемент|Описание|
|---|---|
| start |Время начала первого события в тактах. |
| длительность |Продолжительность фрагмента в тактах. |
| index | Определяет индекс кадра текущего события (применяется только к Azure Media Redactor). |
| interval |Интервал каждой записи события внутри фрагмента в тактах. |
| события |Каждое событие содержит лица, обнаруженные и отслеживаемые в течение этого промежутка времени. Оно представляет собой массив событий. Внешний массив представляет один интервал времени. Внутренний массив состоит из нуля или более событий, которые выполнялись в этот момент времени. Пустые скобки означают, что лица не обнаружены. |
| id |Идентификатор отслеживаемого лица. Если лицо становится необнаруживаемым, это значение может быть случайным образом изменено. Человек должен иметь один и тот же идентификатор на протяжении всего видео, однако это условие не может быть гарантированным из-за ограничений алгоритма обнаружения (перекрытие и т. д.). |
| x, y |Верхние левые координаты X и Y прямоугольника, ограничивающего лицо, в нормализованном масштабе от 0,0 до 1,0. <br/>Координаты X и Y всегда указываются в альбомной ориентации, поэтому при наличии портретного видео (или перевернутого — в iOS) потребуется соответствующим образом поменять координаты. |
| width, height |Ширина и высота прямоугольника, ограничивающего лицо, в нормализованном масштабе от 0,0 до 1,0. |
| facesDetected |Этот элемент находится в конце результатов JSON и означает количество лиц, обнаруженных алгоритмом на видео. Поскольку идентификаторы могут быть случайно сброшены в случае невозможности обнаружения лица (например, человек пропадает с экрана, отворачивается), это число не всегда соответствует реальному количеству лиц на видео. |
